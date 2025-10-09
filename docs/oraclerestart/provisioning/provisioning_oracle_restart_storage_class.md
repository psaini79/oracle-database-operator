# Provisioning an Oracle Restart Database with Custom Storage Class
### In this Usecase:
* The Oracle Grid Infrastructure and Oracle Restart Database are deployed automatically using Oracle Restart Controller with Custom Storage Class. 
* In this use case, the ASM Disks are provisioned as Persistent Volumes using custom storage class during the deployment.
* This example uses `oraclerestart_prov_storage_class.yaml` to provision an Oracle Restart Database using Oracle Restart Controller with:
  * Oracle Restart Pod
  * Headless services for Oracle Restart
    * Oracle Restart node hostname
  * Node Port 30007 mapped to port 1521 for Database Listener
  * Persistent volumes for ASM Disks created automatically using the Storage Class for Oracle ASM storage
  * Software Persistent Volume and Staged Software Persistent Volume using the specified location on the corresponding worker node
  * Namespace: `orestart`
  * Staged Software location on the worker nodes is specified by `hostSwStageLocation`. The Grid Infrastructure and RDBMS Binaries are copied to this location on the worker node.
  * The GI HOME and the RDBMS HOME in the Oracle Restart Pod are mounted using a Persistent Volume created using the Storage Class (specified using `storageClass`). This Persistent Volume is mounted to `/u01` inside the Pod. Size of this Persistent Volume is specified using `swLocStorageSizeInGb`.

### In this Example:
  * Oracle Restart Database Slim Image `localhost/oracle/database-orestart:19.3.0-slim` is used and it is built using files from [GitHub location](https://github.com/oracle/docker-images/tree/main/OracleDatabase/RAC/OracleRealApplicationClusters#building-oracle-rac-database-container-slim-image). Default image created using files from this project is `localhost/oracle/database-rac:19.3.0-slim`. You need to tag it with name `localhost/oracle/database-orestart:19.3.0-slim`. 
  * When you are building the image yourself, update the image value in the `oraclerestart_prov_storage_class.yaml` file to point to the container image you have built. 
  * The disks provisioned using customer storage class (specified by `crsDgStorageClass`) for the Oracle Restart storage are `/dev/asm-disk1` and `/dev/asm-disk2`. 
  * Specify the size of these devices along with names using the parameter `storageSizeInGb`. Size is by-default in GBs.

**NOTE:** When no separate diskgroup names are specified for CRS Files, Database Files, Recovery Area Files and Redo Log Files, then the default diskgroup named `+DATA` is created from the disks specified by the parameter `crsAsmDeviceList`.
  
### Steps: Deploy Oracle Restart Database
* Use the file: [oraclerestart_prov_storage_class.yaml](./oraclerestart_prov_storage_class.yaml) for this use case as below:
* Deploy the `oraclerestart_prov_storage_class.yaml` file:
    ```sh
    kubectl apply -f oraclerestart_prov_storage_class.yaml
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
* Check Details of Kubernetes CRD Object as in this [example](./orestart_storage_class_object.txt)
