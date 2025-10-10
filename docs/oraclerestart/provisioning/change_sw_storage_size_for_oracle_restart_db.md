# Change the size of Software Storage Location for an existing Oracle Restart Database

### In this Use case:
* You have previously deployed an Oracle Restart Database in Kubernetes (for example, on OKE or OpenShift) using the Oracle Restart Database Controller.
* The Software Home Location for Grid Infrastructure and Database, the ASM Disks are provisioned as Persistent Volumes using custom storage class during the initial deployment. An updated YAML file is applied to `increase` the size of the Software Home Location.

**NOTE:** The `decrease` in the size of Software Home Location for an existing Oracle Restart Database is _not_ allowed.

This example uses `oraclerestart_prov_storage_class_before_sw_home_resize.yaml` to initially provision an Oracle Restart Database using Oracle Restart Controller with:

  * Oracle Restart Pod
  * Headless services for Oracle Restart
    * Oracle Restart Node hostname
  * Node Port 30007 mapped to port 1521 for Database Listener. If you are using Loadbalancer service then you will see lbservice.
  * Persistent volumes for ASM Disks created automatically using the Storage Class for Oracle ASM storage
  * Persistent volume for Software location is created automatically using the Storage Class. The GI HOME and the RDBMS HOME in the Oracle Restart Pod will be mounted using the corresponding Persistent Volume Claim. Its size is specified by `swLocStorageSizeInGb`.
  * Namespace: `orestart`
  * Staged Software location on the worker nodes is specified by `hostSwStageLocation`. The Grid Infrastructure and RDBMS Binaries are copied to this location on the worker node.
  * Name of Custom Storage Class is specified by `storageClass`.
  * You will be using storageclass to dynamically allcate the storage. using the storage class **oci-bv**.

### In this Example: 
  * Oracle Restart Database Slim Image `dbocir/oracle/database-orestart:19.3.0-slim` is used. It is built using files from this [GitHub location](https://github.com/oracle/docker-images/tree/main/OracleDatabase/RAC/OracleRealApplicationClusters#building-oracle-rac-database-container-slim-image). 
  * The disks provisioned using storageclass are mounted inside the Oracle Restart Pod as `/dev/asm-disk1` and `/dev/asm-disk2`. 
  * Specify the size of these devices along with names using the parameter `swLocStorageSizeInGb`. Size is by-default in GBs.

**NOTE:** When no separate diskgroup names are specified for CRS Files, Database Files, Recovery Area Files and Redo Log Files, the default diskgroup named `+DATA` is created from the disks specified by the parameter `crsAsmDeviceList`.
  
### Steps - Deploy the Oracle Restart Database
* Skip this step if you have already deployed the Oracle Restart database using storage class.
* Use the file [oraclerestart_prov_storage_class_before_sw_home_resize.yaml](./oraclerestart_prov_storage_class_before_sw_home_resize.yaml) for this use case in this procedure.
* Update the Oracle Restart container image. In this example, we have `dbocir/oracle/database-orestart:19.3.0-slim` in [oraclerestart_prov_storage_class_before_sw_home_resize.yaml](./oraclerestart_prov_storage_class_before_sw_home_resize.yaml) file to point to the container image you have built. 
* Deploy the `oraclerestart_prov_storage_class_before_sw_home_resize.yaml` file:
    ```sh
    kubectl apply -f oraclerestart_prov_storage_class_before_sw_home_resize.yaml
    oraclerestart.database.oracle.com/oraclerestart-sample created
    ```
* Check the status of the deployment:
    ```sh
    # Check the status of the Kubernetes Pods:    
    kubectl get all -n orestart

    # Check the logs of a particular pod. For example, to check status of pod "dbmc1-0":    
    kubectl exec -it pod/dbmc1-0 -n orestart -- bash -c "tail -f /tmp/orod/oracle_db_setup.log"
    ===============================
      ORACLE DATABASE IS READY TO USE
    ===============================
    ```
* Check Details of Kubernetes CRD Object as shown in this [example](./orestart_storage_class_object_before_sw_home_resize.txt)

### Steps - Update the Software Home Location in Oracle Restart Database
*  To `increase` the size of the Software Home Location, you can use the updated file [oraclerestart_prov_storage_class_after_sw_home_resize.yaml](./oraclerestart_prov_storage_class_after_sw_home_resize.yaml). 
* Update the Oracle Restart container image. In this example, we use the file `dbocir/oracle/database-orestart:19.3.0-slim` in [oraclerestart_prov_storage_class_before_sw_home_resize.yaml](./oraclerestart_prov_storage_class_before_sw_home_resize.yaml) to point to the container image that you have built.
*  Deploy the `oraclerestart_prov_storage_class_after_sw_home_resize.yaml` file:
    ```sh
    $ kubectl apply -f oraclerestart_prov_storage_class_after_sw_home_resize.yaml
    oraclerestart.database.oracle.com/oraclerestart-sample configured
    ```
   You will notice the Persistent Volume for the Software Location has been resized. You can check Details of updated Kubernetes CRD Object as shown in this [example](./orestart_storage_class_object_after_sw_home_resize.txt)
