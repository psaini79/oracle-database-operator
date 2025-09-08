# Deploying Oracle PrivateAI Container using Public LoadBalancer

Deploy Oracle PrivateAI Container on your Cloud based Kubernetes cluster.  In this example, the deployment uses the YAML file based on `OCI OKE` cluster. 

**IMPORTANT:** Ensure you have completed the steps for [Prerequisites for running Oracle PrivartAI Controller](./README.md#prerequisites-for-running-oracle-privartai-controller) before using Oracle PrivateAI Controller.

**NOTE:** Modify the file `pai_sample_publiclb.yaml` with the actual Reserved Public IP before deployment.

In this example, we use the file [pai_sample_publiclb.yaml](./provisioning/pai_sample_publiclb.yaml) for this use case:

1. Deploy the `pai_sample_publiclb.yaml` file:
    ```sh
    kubectl apply -f pai_sample_publiclb.yaml
    ```
2. Check the status of the deployment:
    ```sh
    # Check the status of the Kubernetes Pods:
    kubectl get all -n pai

    # Check the logs of a particular pod. For example, to check status of pod "pai-sample-b669d7897-nkkhz":
    kubectl logs pod/pai-sample-b669d7897-nkkhz -n pai
    ```
  