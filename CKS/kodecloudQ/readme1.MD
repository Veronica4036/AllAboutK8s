Task
A pod has been created in the omni namespace. However, there are a couple of issues with it.

The pod has been created with more permissions than it needs.
It allows read access in the directory /usr/share/nginx/html/internal causing an Internal Site to be accessed publicly.

To check this, click on the button called Site (above the terminal) and add /internal/ to the end of the URL.
Use the below recommendations to fix this.
Use the AppArmor profile created at /etc/apparmor.d/frontend to restrict the internal site.
There are several service accounts created in the omni namespace. Apply the principle of least privilege and use the service account with the minimum privileges (excluding the default service account).
Once the pod is recreated with the correct service account, delete the other unused service accounts in omni namespace (excluding the default service account).


You can recreate the pod but do not create a new service accounts and do not use the default service account.
Solution
References:

AppArmor official documentation
On the controlplane node, load the AppArmor profile:

root@controlplane:~# apparmor_parser -q /etc/apparmor.d/frontend

The profile name used by this file is restricted-frontend (open the /etc/apparmor.d/frontend file to check).

To verify that the profile was successfully loaded, use the aa-status command:

root@controlplane:~# aa-status | grep restricted-frontend
   restricted-frontend

The pod should only use the service account called frontend-default as it has the least privileges of all the service accounts in the omni namespace (excluding default)
The other service accounts, fe and frontend have additional permissions (check the roles and rolebindings associated with these accounts)

Use the below YAML File to re-create the frontend-site pod:

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: frontend-site
  namespace: omni
spec:
  securityContext:
    appArmorProfile:
      type: Localhost
      localhostProfile: restricted-frontend
  serviceAccountName: frontend-default #Use the service account with least privileges
  containers:
  - image: nginx:alpine
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
       path: /data/pages
       type: Directory

Next, Delete the unused service accounts in the 'omni' namespace.

controlplane$ kubectl -n omni delete sa frontend
controlplane$ kubectl -n omni delete sa fe

Details

correct service account used?


obsolete service accounts deleted?


internal-site restricted?


pod running?

Q. 2

Task
A pod has been created in the orion namespace. It uses secrets as environment variables. Extract the decoded secret for the CONNECTOR_PASSWORD and place it under /root/CKS/secrets/CONNECTOR_PASSWORD.

You are not done, instead of using secrets as an environment variable, mount the secret as a read-only volume at path /mnt/connector/password that can be then used by the application inside.

Solution
To extract the secret, run:

kubectl -n orion get secrets a-safe-secret -o jsonpath='{.data.CONNECTOR_PASSWORD}' | base64 --decode >/root/CKS/secrets/CONNECTOR_PASSWORD 

One way that is more secure to distribute secrets is to mount it as a read-only volume.

Use the following YAML file to recreate the POD with secret mounted as a volume:

apiVersion: v1
kind: Pod
metadata:
  name: app-xyz
  namespace: orion
  labels:
    name: app-xyz
spec:
  containers:
    - name: app-xyz
      image: nginx
      ports:
        - containerPort: 3306
      volumeMounts:
        - name: secret-volume
          mountPath: /mnt/connector/password
          readOnly: true
  volumes:
    - name: secret-volume
      secret:
        secretName: a-safe-secret

Details

pod secured?


secret mounted as read-only?


existing secret extracted to file?

Q. 3

Task
The Release Engineering Team has shared some YAML manifests and Dockerfiles with you to review. The files are located under /opt/course/.

As a container security expert, you are asked to perform a manual static analysis and find out possible security issues with respect to unwanted credential exposure. Running processes as root is of no concern in this task.

Write the filenames which have issues into /opt/course/security-issues.txt.

Note: In the Dockerfiles and YAML manifests, assume that the referred files, folders, secrets and volume mounts are present. Disregard syntax or logic errors.

Solution
File q3_file1.Dockerfile copies a file secret-token over (COPY secret-token .), uses it (RUN /etc/register.sh ./secret-token) and deletes it afterwards (RUN rm ./secret-token). But because of
the way Docker works, every RUN, COPY and ADD command creates a new layer and every layer is persistet in the image.
This means that even if the file secret-token get's deleted, it's still included with the image.

File q3_file2.yaml contains plain text password under env section value: P@sSw0rd. Its secure practice to utilize k8s's secrets to store passwords.

echo -e "q3_file1.Dockerfile\nq3_file2.yaml" > /opt/course/security-issues.txt

Details

Filenames which have issues written into /opt/course/security-issues.txt?

Q. 4

Task
Create a new pod called audit-nginx in the default namespace using the nginx image. Secure the syscalls that this pod can use by using the audit.json seccomp profile in the pod's security context.

The audit.json is provided at /root/CKS directory. Make sure to move it under the profiles directory inside the default seccomp directory before creating the pod

Solution
Copy the audit.json seccomp profile to /var/lib/kubelet/seccomp/profiles on the controlplane node:

controlplane$ mv /root/CKS/audit.json /var/lib/kubelet/seccomp/profiles

Next, recreate the pod using the below YAML File

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: audit-nginx
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/audit.json
  containers:
  - image: nginx
    name: nginx

Details

audit-nginx uses the right image?


pod running?


pod uses the correct seccomp profile?

Q. 5

Task
The CIS Benchmark report for the Controller Manager and Scheduler is available at the tab called CIS Report 1.

Inspect this report and fix the issues reported as FAIL.

Solution
The fixes are mentioned in the same report. Update the Controller Manager and Scheduler static pod definition file as per the recommendations.
1. Make sure that the --profiling=false parameter is set.

Details

apiserver working?


Issues fixed for controller?


Issues fixed for scheduler?

Q. 6

Task
There is something suspicious happening with one of the pods running an httpd image in this cluster.
The Falco service shows frequent alerts that start with: File below a known binary directory opened for writing.

Identify the rule causing this alert and update it as per the below requirements:

Output should be displayed as: CRITICAL File below a known binary directory opened for writing (user_id=user_id file_updated=file_name command=command_that_was_run)
Alerts are logged to /opt/security_incidents/alerts.log
Do not update the default rules file directly. Rather use the falco_rules.local.yaml file to override.
Note: Once the alert has been updated, you may have to wait for up to a minute for the alerts to be written to the new log location.

Solution
Enable file_output in /etc/falco/falco.yaml on the controlplane node:

file_output:
  enabled: true
  keep_alive: false
  filename: /opt/security_incidents/alerts.log

Next, add the updated rule under the /etc/falco/falco_rules.local.yaml and hot reload the Falco service:

- rule: Write below binary dir
  desc: an attempt to write to any file below a set of binary directories
  condition: >
    bin_dir and evt.dir = < and open_write
    and not package_mgmt_procs
    and not exe_running_docker_save
    and not python_running_get_pip
    and not python_running_ms_oms
    and not user_known_write_below_binary_dir_activities
  output: >
    File below a known binary directory opened for writing (user_id=%user.uid file_updated=%fd.name command=%proc.cmdline)
  priority: CRITICAL
  tags: [filesystem, mitre_persistence]

To perform hot-reload falco use 'kill -1 /SIGHUP':

kill -1 $(cat /var/run/falco.pid)

Alternatively, you can also restart the falco service by running:

systemctl restart falco

Details

task completed?

Q. 7

Task
Generate a SPDX-Json SBOM of image registry.k8s.io/kube-apiserver:v1.31.0.

Store it at /opt/course/sbom1.json.

Install bom using :

git clone https://github.com/kubernetes-sigs/bom.git
cd bom
go build -o bom ./cmd/bom
mv bom /usr/local/bin/

Solution
bom utility is already installed.

Run below cmd to save sbom to the desired file.

bom generate --image registry.k8s.io/kube-apiserver:v1.31.0 --format json --output /opt/course/sbom1.json

Details

SPDX-Json SBOM of image registry.k8s.io/kube-apiserver stored at /opt/course/sbom1.json ?

Q. 8

Task
We need to make sure that when pods are created in this cluster, they cannot use the latest image tag, irrespective of the repository being used.

To achieve this, a simple Admission Webhook Server has been developed and deployed. A service called image-bouncer-webhook is exposed in the cluster internally. This Webhook server ensures that the developers of the team cannot use the latest image tag. Make use of the following specs to integrate it with the cluster using an ImagePolicyWebhook:


Create a new admission configuration file at /etc/admission-controllers/admission-configuration.yaml
The kubeconfig file with the credentials to connect to the webhook server is located at /root/CKS/ImagePolicy/admission-kubeconfig.yaml. Note: The directory /root/CKS/ImagePolicy/ has already been mounted on the kube-apiserver at path /etc/admission-controllers so use this path to reference the admission configuration.
Make sure that if the latest tag is used, the request must be rejected at all times.
Enable the Admission Controller.
Finally, delete the existing pod in the magnum namespace that is in violation of the policy and recreate it, ensuring the same image but using the tag 1.27.

NOTE: If the kube-apiserver becomes unresponsive, this can affect the validation of this exam. In such a case, please restore the kube-apiserver using the backup file created at: /root/backup/kube-apiserver.yaml, wait for the API to be available again and proceed.

Solution
Create the below admission-configuration inside /root/CKS/ImagePolicy directory in the controlplane node:

apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
- name: ImagePolicyWebhook
  configuration:
    imagePolicy:
      kubeConfigFile: /etc/admission-controllers/admission-kubeconfig.yaml
      allowTTL: 50
      denyTTL: 50
      retryBackoff: 500
      defaultAllow: false

The /root/CKS/ImagePolicy is mounted at the path /etc/admission-controllers directory in the kube-apiserver. So, you can directly place the files under /root/CKS/ImagePolicy.

Here is a snippet of the volume and volumeMounts (already added to apiserver config):

  containers:
  .
  .
  .
  volumeMounts:
  - mountPath: /etc/admission-controllers
      name: admission-controllers
      readOnly: true

  volumes:
  - hostPath:
      path: /root/CKS/ImagePolicy/
      type: DirectoryOrCreate
    name: admission-controllers

Next, update the kube-apiserver command flags and add ImagePolicyWebhook to the enable-admission-plugins flag. Use the configuration file that was created in the previous step as the value of admission-control-config-file.

Note: Remember, this command will be run inside the kube-apiserver container, so the path must be /etc/admission-controllers/admission-configuration.yaml (mounted from /root/CKS/ImagePolicy in controlplane).

    - --admission-control-config-file=/etc/admission-controllers/admission-configuration.yaml
    - --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook

In case we mess up while solving the question, API server could become unresponsive.

For example:

The connection to the server controlplane:6443 was refused - did you specify the right host or port?

In such case scenario restore kube-apiserver to it's default state using the backup provided at /root/backup/kube-apiserver.yaml

Run the below command to restore the kube-apiserver initial state:

cp -v /root/backup/kube-apiserver.yaml /etc/kubernetes/manifests

Details

ImagePolicyWebhook enabled and API server running?


policy implemented?


pod recreated with the correct image?


pod running?


