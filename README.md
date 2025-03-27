# kasten-k10-scenarios
# Challenges

1. Install Kasten 
   - Install Kasten,
   - configure primary storage, and 
   - enable Kasten Dashboard access 
2. Install MinIO
   - Install MinIO
   - configure a Kasten Location Profile 
3. Install App
   - Install an example, stateful application that will be protected using Kasten 
4. Backup App
   - Create and run your first backup policy 
5. Restore App
   - Delete and restore your application
6. (BONUS) Backup via API
   - Understand how to perform actions via API

## Install Kasten

### Part1. Pre-Flight Checks
1. In the ✅ Terminal tab, begin by adding the Kasten repository to helm, as this is required to run the pre-flight check script:
   
   ```shell
   helm repo add kasten https://charts.kasten.io/
   helm repo update
   ```

   💡 Note:

   `Running the pre-flight checks is not required to install Kasten, but is highly recommended.`

2. Run the pre-flight check script and examine the output (Yes, the error is expected!):

   ```shell
   curl -s https://docs.kasten.io/tools/k10_primer.sh  | bash
   ```

   You should expect to see the available `StorageClass` for the cluster and its associated `VolumeSnapshotClass` - but the VolumeSnapshotClass does not include the annotation required for Kasten! This annotation must be applied to each VolumeSnapshotClass on the cluster in order for Kasten to request snapshots of those volumes.
3. Run the command below to apply the necessary annotation and re-run the pre-flight script:
   
   ```shell
   kubectl annotate volumesnapshotclass csi-hostpath-snapclass \
   k10.kasten.io/is-snapshot-class=true
   curl -s https://docs.kasten.io/tools/k10_primer.sh  | bash
   ```

   You should now see the proper annotation has been applied.

   💡 Note:
   For production CSI drivers, this script can also be used to validate CSI snapshot capabilities. See docs.kasten.io. https://docs.kasten.io/latest/install/storage.html#csi-preflight

### Part 2. Install Kasten

1. Now that we are confident our Kubernetes cluster meets the basic Kasten requirements, proceed with the installation in the ✅ Terminal:

   ```shell
   # Create a new namespace for the Kasten deployment
   kubectl create ns kasten-io
   
   # Install Kasten
   helm install k10 kasten/k10 \
   --namespace=kasten-io \
   ```   
  
2. To ensure the Kasten installation completes successfully, monitor Pod status until every Pod appears as READY:
   
   ```shell
   watch "kubectl -n kasten-io get pods"
   ```

   💡 Note:
   The watch command will provide updated output of the kubectl command every 2 seconds.

### Part3. Accessing the Kasten Dashboard

Kasten Dashboard is accessibla via `kasten.<username>.strivly.academy`
4. Specify an Email and Company name.
5. Click Accept Terms.
6. Click No, Thanks to dismiss the guided tour.

--> You are now logged in as the default k10-admin user. We will explore options for authentication and role based access control in an upcoming lab.

## Install Minio

### Part1. Review MinIO Configuration

1. In the ✅ Terminal tab, begin by validating the MinIO Pods are running:
   ```shell
   kubectl get all -n minio
   ```
2. Select the 🪣 MinIO tab
3. Log in using the default credentials
4. Under Administrator in the left-hand menu, select Buckets. Click the pre-staged lab-bucket-immutable bucket to view its configuration.
5. You should observe that Object Locking is enabled and that Retention is configured for 30 Days.

   This means that we should expect new versions of objects written to this bucket are unable to be modified or deleted for 30 days. Kasten integrates with S3 or S3-compatible object storage solutions that support S3 Object Lock in order to ensure immutability of backup data, putting it out of the hands of malware and bad actors.

 ### Part2. Configure a Location Profile

1. In the ✅ Terminal tab, run the following command and copy the ENDPOINT value for use in an upcoming step:
   ```shell
   echo "MinIO endpoint: \n"
   kubectl get ingress -n minio
   ```
2. In the 🖥️ Kasten tab, expand Profiles in the sidebar and click Locations.
3. Click + New Profile to begin defining a new backup export location.
4. Fill out the following fields but DO NOT click Save Profile yet:
   Profile Name	s3-immutable
   Storage Provider	S3 Compatible
   S3 Access Key	minio
   S3 Secret	mini123
   Endpoint	Paste the ENDPOINT value from Step 1
   Region	custom
   Bucket	lab-bucket-immutable
5. Select Enable Immutable Backups and click the Validate Bucket button.
   You should expect for all checks to pass, as shown. If you encounter errors, verify your Endpoint and Bucket values are correct before attempting to validate again.
6. Drag the Protection Period slider to set how far into the past you want to be able to access immutable backups.

   Note
   The maximum Protection Period will be equal to the bucket's retention period (30 days, in this lab) minus 20 days. For example, if you needed to ensure your last 30 days of Kasten backups are immutable, the default retention on the bucket would need to be at least 50 days.
7. Click Save Profile, You should expect your s3-immutable Location Profile to appear as Valid, Now you're ready to start protecting apps!
8. Click the < / > yaml button to view the YAML generated by creating a Location Profile through the Dashboard.
9. As you can see from this example, Kasten Location Profiles can be created declaratively as a profile.config.kio.kasten.io object referencing a Secret to store access and secret keys. This Kubernetes-native implementation makes it simple to configure backup targets using a GitOps tools.

## Install MongoDB

### Part1. Deploy MongoDB

1. In the ✅ Terminal, run the following commands to install a MongoDB instance into the mongodb namespace:

   ```shell
   # Add MongoDB Helm repository
   helm repo add bitnami https://charts.bitnami.com/bitnami
   
   # Create mongodb namespace
   kubectl create namespace mongodb
   
   # Install mongodb HA cluster using Helm chart
   helm install mongo bitnami/mongodb --namespace mongodb \
   --set architecture="replicaset" --version 13.6.7
   ```

2. Monitor the deployment until mongo-mongodb-0, mongo-mongodb-1, and mongo-mongodb-arbiter-0 Pods have reached a READY state:

   ```shell
   watch "kubectl -n mongodb get pods"
   ```
3. Press CTRL + C to exit watch.
4. Run the following to list the PersistentVolumeClaims provisioned as part of the mongodb StatefulSet:
   ```shell
   kubectl get pvc -n mongodb
   ```
   You should expect to see 2x 8Gi volumes, 1 volume per mongo-mongodb-... database Pod, dynamically provisioned using the default csi-hostpath-sc StorageClass.
   
   💡 Note:
   MongoDB achieves high availability through replication across multiple nodes. The primary replica receives all write operations and all secondary instances apply operations from the primary to maintain the same dataset. Other types of database may depend on a shared volume across two or more database servers.

### Part2. Add MongoDB Data

1. In the ✅ Terminal, run the following to connect to your instance using the mongosh CLI utility:
   ```shell
   export MONGODB_ROOT_PASSWORD=$(kubectl \
   get secret mongo-mongodb --namespace mongodb \
   -o jsonpath="{.data.mongodb-root-password}" \
   | base64 --decode)
   kubectl exec -it statefulset/mongo-mongodb \
   -n mongodb \
   -- mongosh admin \
   --authenticationDatabase admin \
   -u root -p $MONGODB_ROOT_PASSWORD \
   --host "mongo-mongodb-0.mongo-mongodb-headless:27017,mongo-mongodb-1.mongo-mongodb-headless:27017"
   ```
2. From the rs0 [direct: primary] admin> prompt, run the following to create a collection and populate it with data:
   ```shell
   db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )
   db.log.insert({ item: "card", qty: 15 })
   db.log.insert({ item: "dice", qty: 3 })
   db.log.find()
   ```
   Validate the db.log.find() command returns the 2 inserted records
3. From the rs0 [direct: primary] admin> prompt, run the following to terminate your mongosh connection to the database and return to your cluster's command prompt:
   ```shell
   exit
   ```
## Backup

### Part1. Create a backup Policy
1. Select the 🖥️ Kasten tab.
2. Navigate to Applications using either the sidebar or the main dashboard card to view all of the application namespaces automatically discovered by Kasten.
   Your mongodb application should appear as Unmanaged, indicating it is not being protected by any policy.
3. Select the ... menu for the mongodb application and click Create a Policy.
4. Leave the default Name and Action.
5. Leave the default Hourly Backup Frequency and Snapshot Retention values.

   💡 Note:
   Toggling Advanced Frequency Options allows users to specify what time hourly snapshots occur, how many snapshots to take per hour, and which snapshots should be used for daily, weekly, monthly, and yearly promotion.

   Toggling Backup Window allows users to specify during what times is Kasten allowed to run the policy. Enabling Use Staggering can intelligently distribute when to start policies during the specified window such that the desired frequency is maintained, but with the least amount of policies running simultaneously, allowing Kasten to reduce the peak load on the cluster.

   These settings should be left unselected for this lab.
6. Toggle Enable Backups via Snapshot Exports and select s3-immutable as the Export Location Profile.

   💡 Note:
   By default, Kasten will export all data associated with the snapshot to ensure you have a durable, off-cluster copy. However, there are circumstances where you may only want to export references to the snapshot, such as migrating a workload in AWS from one availability zone to another. This ability to only export snapshot metadata can dramatically improve performance in these specific instances. This can be configured under Advanced Export Settings.
7. Under Select Applications, verify the mongodb namespace has been selected.

   💡 Note:
   Targeting application(s) based on namespace is generally the most straightforward method of defining a backup policy. However, Kasten also allows you to identify applications based on native Kubernetes labels. This is especially helpful if you want to define "blanket" policies that will apply to current and future applications, such as hasData: true or backup: gold.

   Kasten also provides rich filtering capabilities to include or exclude resources based on Kubernetes API Group, API Version, Resource Type, Resource Name, and Labels. For example, you could exclude backup for Secrets resources where a label includes an indication that the secret is externally managed.
8. Leave the remaining settings as default.

   💡 Note:
   When performing many tasks within the Kasten UI, you can press the </> YAML button to expose the native Kubernetes YAML that defines the resource created through the UI. This can be useful for familiarizing yourself with the Kubernetes-native APIs defined by Kasten and for extracting snippets for use in GitOps or Infrastructure-as-Code tools.
9. Click Create Policy.

### Part2. Perform an On-Demand Backup

Rather than waiting until the top of the hour for the policy to execute automatically, you will manually initiate your first run of the policy.

1. On the Policies page, click the Run Once icon associated with your mongodb-backup policy.
2. Click Yes, Continue to start the manual policy run.

   💡 Note:
   As a best practice, on-demand policy runs should specify a Snapshot Expiration date to ensure that snapshots and exported backup data are automatically expired and cleaned up by Kasten. This can be ignored in the lab, as the cluster will be destroyed once the lab is completed.
3. Using the sidebar, return to the main Dashboard and scroll down to monitor the Policy Run under Actions.
4. From the Dashboard, click on your Policy Run for a more detailed view. Try clicking into each individual Action to view to associated data, including YAML, a complete list of the application metadata and volume snapshots protected, how much volume data was processed by the Kasten datamover, and how much unique data was transferred to the Location Profile.

### Part3. Explore Location Profile

Next, you will explore the Kopia repository folder structure and verify backup data cannot be deleted after enabling immutability on the Location Profile.

1. Once the Policy Run completes successfully, return to the 🪣 MinIO tab.

   Note
   If prompted to log in to the MinIO console again, use minioadmin as the username and password.
2. Under User > Object Browser in the MinIO sidebar, select the lab-bucket-immutable bucket.
3. Expand the k10 directory and verify there is a single directory with a <UUID> value.

   This value is used to identify a specific cluster, as multiple clusters may share a single Location Profile for exporting backup data. Specifically, the value corresponds to the uid of the default namespace of the cluster. Optionally, you can verify this by running kubectl get namespace default -o jsonpath={.metadata.uid} in the ✅ Terminal.
4. Expand the <UUID>/migration directories and verify mongodb-backup and repo directories.

   The mongodb-backup directory corresponds to the Policy name, and contains an encrypted repository of application metadata (e.g. StatefulSets, ConfigMaps, etc.).
5. Expand the repo directory and verify there is a single directory with a <UUID> value.

   This value is used to define the repository location for volume data associated with a namespace. Specifically, the value corresponds to the uid of the mongodb namespace. Optionally, you can verify this by running kubectl get namespace default -o jsonpath={.metadata.uid} in the ✅ Terminal.
6. Expand the <UUID> to view the encrypted repository of volume data for the mongodb application.
7. Select all of the objects in the directory and click Delete to attempt to permanently remove mongodb backup data.
8. Toggle Delete All Versions to Yes and click Delete to confirm the action.

   You should receive an error in the console indicating the objects are WORM (Write Once Read Many) Protected.

   Even with full administrator permissions, you are unable to remove the objects due to the Object Lock configuration of the bucket.
9. Toggle Delete All Versions to No and click Delete to confirm the action.

   OH NO, THEY'RE GONE! Just kidding!

   Performing a DELETE operation on a versioned S3 object creates a 0-byte DeleteMarker as the latest version of that object - the previous, immutable version of each object remains and can be viewed by selecting Show deleted objects in the MinIO console. You will demonstrate the lab-bucket-immutable data is still available in the next exercise by deleting the mongodb application and restoring from your exported Kasten backup.

## Restore

### Part1. Delete MongoDB

1. In the ✅ Terminal, run the following commands to uninstall MongoDB from the mongodb namespace and delete any associated PersistentVolumeClaims:

   ```shell
   helm uninstall mongo -n mongodb
   kubectl delete pvc -n mongodb --all
   ```
2. Verify MongoDB has been removed:
   
   ```shell
   kubectl get all -n mongodb
   ```
   You should observe No resources found in mongodb namespace.

### Part2. Restore MongoDB

1. Return to 🖥️ Kasten and select Applications using either the sidebar or the main dashboard card.
2. Select the ... menu for the mongodb application and click Restore.

   💡 Note:
   Kasten can also be used to perform bulk restores of multiple namespaces. See docs.kasten.io for additional details on performing multi-app restores.
3. Select the most recent RestorePoint.
4. Select the EXPORTED version to restore from the copy stored on the s3-immutable Location Profile.

   💡 Note:
   When available, using the local snapshot should result in a faster restore than having to transfer data from your remote repository. For the lab the EXPORTED version is used to demonstrate availability of backup data following attempts to delete it during the previous exercise.
5. You will use the default values for this restore operation
6. At the bottom of the form, click Restore > Restore.

   💡 Note:
   If mongodb was uninstalled and its PVCs deleted, why should restoring from the local backup still work? Hint: Run kubectl get volumesnapshot -n mongodb to see the current status of your snapshots.

   You will see that your VolumeSnapshot objects exist within the application namespace. This also means if you were to delete the entire namespace, your VolumeSnapshots would be deleted as well! This is why it's important to export a durable copy of your backups off of the cluster, rather than relying entirely on local snapshots!
7. Return to the main Dashboard to monitor the progress of the restore under Actions.

   You should expect the mongodb restore operation to complete in ~3 minutes.

### Part3. Validate Restore

1. Once the restore has completed, run the following in the ✅ Terminal to connect to your instance using the mongosh CLI utility:

   ```shell
   export MONGODB_ROOT_PASSWORD=$(kubectl \
   get secret mongo-mongodb --namespace mongodb \
   -o jsonpath="{.data.mongodb-root-password}" \
   | base64 --decode)
   kubectl exec -it statefulset/mongo-mongodb \
   -n mongodb \
   -- mongosh admin \
   --authenticationDatabase admin \
   -u root -p $MONGODB_ROOT_PASSWORD \
   --host "mongo-mongodb-0.mongo-mongodb-headless:27017,mongo-mongodb-1.mongo-mongodb-headless:27017"
   ```
2. From the rs0 [direct: primary] admin> prompt, run the following to validate the contents of your Collection have been successfully restored along with the application:

   ```shell
   db.log.find()
   ```
   You should expect the log collection to contain the previously populated records, similar to the below:
   ```json
   [
      { _id: ObjectId("65a04db1de2d9070c23be3a0"), item: 'card', qty: 15 },
      { _id: ObjectId("65a04db1de2d9070c23be3a1"), item: 'dice', qty: 3 }
   ]
   ```
Congratulations! You've successfully performed your first Kasten application backup and restore.

## Backup via API

1. In the ✅ Terminal, run the following commands to describe the Application in the mongodb namespace:

   ```shell
   kubectl describe applications.apps.kio.kasten.io -n mongodb
   ```

   The Kasten Application custom resource is read-only and automatically created by Kasten when it discovers applications that are running on the Kubernetes cluster. This resource resides within the application namespace.
2. Note the matching Name and Namespace values in the output, these will be used in defining your BackupAction object.

   BackupActions are another Kasten-specific custom resource, and are used to define and execute a snapshot of an application and its data.
3. Run the following to create a manifest for a BackupAction to perform an on-demand backup of your mongodb application:
   
   ```yaml
   cat > lab-backup-action.yaml <<EOF
   apiVersion: actions.kio.kasten.io/v1alpha1
   kind: BackupAction
   metadata:
     generateName: lab-backup-
   # BackupActions reside in the namespace
   # of the resources being protected
     namespace: mongodb
     labels:
       # These labels can be added to actions
       # so they can be filtered in the UI
       k10.kasten.io/appName: "mongodb"
       k10.kasten.io/appNamespace: "mongodb"
   spec:
     subject:
     # References the Kasten Application CR
     # values from the previous step
       name: mongodb
       namespace: mongodb
   EOF
   ```
4. Apply the manifest to initiate the backup:
   
   ```shell
   kubectl create -f lab-backup-action.yaml   
   ```

   Kasten will validate the BackupAction and place it in a queue to be executed.
5. You can monitor the status of the BackupAction in the 🖥️ Kasten or by running the following:
   
   ```shell
   watch 'kubectl get backupactions.actions.kio.kasten.io -n mongodb'
   ```
6. Once your lab-backup-... BackupAction's State reaches Complete, press CTRL+C to end the watch.

   You've now got a local backup of your application - but to ensure you're protected in the event of a site failure, let's export it to your object store!

### Part2. Prepare to Export via API

To create an ExportAction, you'll need two additional pieces of information:

The name of the RestorePoint created by your BackupAction
The name of the Location Profile to which you will export your data

1. In the ✅ Terminal, run the following to return the complete list of BackupActions in the mongodb namespace.

   ```shell
   kubectl get backupactions.actions.kio.kasten.io -n mongodb
   ```
2. Copy the Name of the lab-backup-... BackupAction you created in Part 1 of the exercise.
3. Substitute your BackupAction name and run the following to return details of your on-demand BackupAction:

   ```shell
   # Replace lab-backup-... with the BackupAction
   # name obtained in the previous step
   kubectl describe backupactions.actions.kio.kasten.io -n mongodb lab-backup-...
   ```
4. Note the value of the RestorePoint name (it should match your BackupAction name).

   The RestorePoint object is the collection of application metadata (including VolumeSnapshot references) that was captured by Kasten during backup.

   💡 Note:
   RestorePoints are namespaced objects that can be found in the namespace of the associated application. Kasten also creates a corresponding RestorePointContents object which is cluster-scoped. This ensures that if an application's entire namespace is deleted, backups are still accessible and can be restored by Kasten. This is similar to the relationship between VolumeSnapshot and VolumeSnapshotContent objects (https://kubernetes.io/docs/concepts/storage/volume-snapshots/).
5. Validate the exact name of the Profile custom resource created in a previous exercise (this should be s3-immutable if the lab was followed exactly):

   ```shell
   kubectl get profiles.config.kio.kasten.io -n kasten-io
   ```

   💡 Note:
   Creating Kasten profiles and policies can also be done easily via API and would commonly be used in Infrastructure as Code scripts for rapidly deploying a new Kubernetes cluster. Add the -o yaml flag to the previous command or use the YAML button when configuring a Location Profile via the UI for examples.

### Part3. Export via API

1. In the ✅ Terminal, substitute your RestorePoint name (and Location Profile name if it differs from s3-immutable) and run:
   
   ```shell
   LOCATION_PROFILE_NAME=s3-immutable; RESTOREPOINT_NAME=lab-backup-...
   ```
2. Run the following the create your ExportAction manifest using the variables set in the previous step:

   ```yaml
      cat > lab-export-action.yaml <<EOF
      apiVersion: actions.kio.kasten.io/v1alpha1
      kind: ExportAction
      metadata:
        generateName: lab-export-
        namespace: kasten-io
      spec:
        subject:
          kind: RestorePoint
          # Uses variable set in previous step
          name: ${RESTOREPOINT_NAME}
          namespace: mongodb
        profile:
          # Uses variable set in previous step
          name: ${LOCATION_PROFILE_NAME}
          namespace: kasten-io
      EOF
   ```
   
3. Validate that your RestorePoint and Profile names appear correct:

   ```shell
   cat lab-export-action.yaml
   ```
   
4. Run the following to apply your ExportAction manifest:
   
   ```shell
   kubectl create -f lab-export-action.yaml
   ```

   Kasten will validate the ExportAction and place it in the queue to be executed.
5. You can monitor the status of the ExportAction by running the following:
   
   ```shell
   watch 'kubectl get exportactions.actions.kio.kasten.io -n kasten-io'
   ```
6. Open the 🖥️ Kasten tab and validate under Actions you see your programmatically created Backup and Export jobs.


### Part4. Executing a Policy via API 

So far you've seen how to manually perform backups and exports, which included defining the application to be backed up, the RestorePoint, and the target location for the export - but didn't you already define all of that in your Policy through the UI? Yes!

The RunAction API provides a simpler means of performing a manual execution of an existing policy, similar to clicking the Run Once button in the UI.

1. In the ✅ Terminal, first review the mongodb-backup Policy custom resource you previously created:

   ```shell
   kubectl get policies.config.kio.kasten.io \
      -n kasten-io mongodb-backup -o yaml
   ```

   Within the spec you should observe the Backup and Export actions already defined.
2. Run the following to create a RunAction manifest to execute a run of the mongodb-backup Policy:
   
   ```yaml
   cat > lab-run-action.yaml <<EOF
   apiVersion: actions.kio.kasten.io/v1alpha1
   kind: RunAction
   metadata:
     generateName: lab-run-
   spec:
     # Expiration is optional
     expiresAt: "2024-12-25T15:00:00Z"
     subject:
       kind: Policy
       # Note the Policy name
       name: mongodb-backup
       namespace: kasten-io
   EOF
   ```

   💡 Note:
   As previously seen in the UI, manual policy runs are not subject to policy retention. Be sure to specify an expiresAt timestamp to enable Kasten to automatically retire the resulting backup.
3. Apply your RunAction manifest:
   
   ```shell
   kubectl create -f lab-run-action.yaml
   ```

   Kasten will validate the action and place it in the queue to be executed.
4. You can monitor the status of the RunAction by running the following:

   ```shell
   watch 'kubectl get runactions.actions.kio.kasten.io -n kasten-io'
   ```
5. Once the action's State reaches Complete, press CTRL+C to end the watch.
6. Again, you can validate the Policy Run appears under Actions on the 🖥️ Kasten.
