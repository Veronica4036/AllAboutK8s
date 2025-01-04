sbom format
- spdx 
- cycloneDX

give 1 example for both

SBOM workflow:
generate sbom (using syft) --> store sbom --> scan sbom (with grype) --> anaylze results --> remediate issues -> monitor

Generate an SBOM for the docker.io/kodekloud/webapp-color:latest image in SPDX format using Syft and store it in /root/webapp-spdx.sbom.
To generate an SBOM for the docker.io/kodekloud/webapp-color:latest image in SPDX format, run the command below:
syft docker.io/kodekloud/webapp-color:latest -o spdx > /root/webapp-spdx.sbom

Generate an SBOM for the docker.io/kodekloud/webapp-color:latest image in CycloneDX JSON format using Syft and store it in /root/webapp-sbom.json.
To generate an SBOM for the docker.io/kodekloud/webapp-color:latest image in CycloneDX JSON format, run the command below:
syft docker.io/kodekloud/webapp-color:latest -o cyclonedx-json > /root/webapp-sbom.json

this is a way to get the supported output values:
```
syft nginx -o --help
[0000] ERROR 1 error occurred:
        * unsupported output format "--help", supported formats are: 
Available formats:
   - cyclonedx-json @ 1.2, 1.3, 1.4, 1.5, 1.6
   - cyclonedx-xml @ 1.0, 1.1, 1.2, 1.3, 1.4, 1.5, 1.6
   - github-json
   - spdx-json @ 2.2, 2.3
   - spdx-tag-value @ 2.1, 2.2, 2.3
   - syft-json
   - syft-table
   - syft-text

```


Analyze the /root/webapp-sbom.json SBOM using Grype to generate a vulnerability report and store it in /root/grype-report.json.
Run the command to push order numbers to the flask-order-service:
grype sbom:/root/webapp-sbom.json -o json > /root/grype-report.json
