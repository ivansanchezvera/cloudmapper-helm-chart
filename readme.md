# How to Install
Add repo via command line, just type:
helm repo add cloudmapper-helm-chart-repo-ivan https://ivansanchezvera.github.io/cloudmapper-helm-chart

Check the repo in the repo list (the repo should be in the list):
helm repo list 

To run this helm chart you must provide values to the variables on the secret.yaml file (on base64).
In this case, I will use override.yaml as an example. So just create a file named override.yaml and then Override the following variables with your values in **base64**:
- aws_key: your_aws_key
- aws_secrets: your_aws_secrets
- aws_region: us-east-1
- aws_id: your_aws_account_id
- aws_account_name: your_aws_account_name
- aws_cidr: your_vpn_cidr

Your override.yaml file should look like this:
aws_key: eW91cl9hd3Nfa2V5
aws_secrets: eW91cl9hd3Nfc2VjcmV0
aws_region: us-east-1
aws_id: eW91cl9hd3NfaWQ=
aws_account_name: Y2xvdWRtYXBwZXJoZWxtQGV4YW1wbGUuY29t
aws_cidr: 172.20.0.0/16

### Execute the helm chart via helm install:
helm install cloudmapper-helm-package-local-test1 cloudmappert-chart-0.1.0.tgz --values override.yaml --debug

### To uninstall just do the basic uninstall command:
helm uninstall cloudmapper-helm-package-local-test1

## Troubleshooting
# Problem: Cannot see the cloudmapper diagrams via the web-browser.
# Possible solution: Make sure that your service is active and the nodeport is exposed. 
Run:
kubectl get all 

Check that everything is ready and running. Also check the ports. Remember we need to set a NodePort to externalize our service (it ranges from 30000 to 32767 ). So the service port should within those ranges.

If using minikube try exposing the service via tunnel (this is a know problem in minikube on macs as far as I know). Try exposing the service doing:
minikube service <name_of_service>
Example:
minikube service cloudmapper-helm-repo-test1-cloudmappert-chart

Remember that we can obtain the name of the service via: 
kubectl get all 
or more details about the service using:
kubectl get service -o wide

It is also useful to check the logs of the pods via:
kubectl logs <name_of_the_pod>

Remember that we can obtain the name of the service via: 
kubectl get all 
or more details about the service using:
kubectl get pod -o wide

# Problem: I get the Error: INSTALLATION FAILED: Secret in version "v1" cannot be handled as a Secret: illegal base64 data... message.
# Possible solution: Make sure that you are overriding the values with base64 ones. An easy approach its described at the beggining of this document by using override.yaml .
To encode values on base64 you can use an online encoder or in the command line just try:
echo -n <your_plaintext_value> | base64

to decode just try:
echo -n <your_base64encoded_value> | base64 --decode


# Problem: I get the Error: Error: INSTALLATION FAILED: cannot re-use a name that is still in use ... message.
# Possible solution: Just try changing the name while running the chart or specify --generate-name
The syntanx to install the helm chart is:
helm install <the_name_for_your_chart> cloudmapper-helm-chart-repo-ivan/cloudmappert-chart --values override.yaml --debug

# Problem: My pod takes a long time running, it havent reach the ready status.
# Possible solution: Wait at for about an hour. Since this is running the full cloudmapper configure process, it is going thru a buch of AWS resources.
You can override this via the config/audit_config_override.yaml file. [Check the official cloudmapper repo for more info on this] (https://github.com/duo-labs/cloudmapper#using-audit-config-overrides).

## References:
Based on:
- the [cloudmapper repo](https://github.com/duo-labs/cloudmapper) from [duolabs](https://github.com/duo-labs)
- the cloudkats [dockerhub image for cloudmapper](https://hub.docker.com/r/cloudkats/cloudmapper).