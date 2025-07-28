# Scale-In an existing deployment of Oracle PrivateAI Container

**IMPORTANT:** This example assumes that you have an existing Oracle PrivateAI Container Deployment with `replicas=3`

In this example, we will Scale In an existing deployment with `replicas=3` to `replicas=2`.

Use the file: [pai-sample-scale-in.yaml](./pai-sample-scale-in.yaml) for this use case as below:

1. Check the status of the deployment:
    ```sh
    # Check the status of the Kubernetes Pods:
    kubectl get all -n pai
    ```
2. Apply the `pai-sample-scale-in.yaml` file to scale in:
    ```sh
    kubectl apply -f pai-sample-scale-in.yaml
    ```
3. Check the status of the deployment:
    ```sh
    # Check the status of the Kubernetes Pods:
    kubectl get all -n pai
    ```

    You will see, Kubernetes Pods are reduced in number once the scale in is done automatically.
  