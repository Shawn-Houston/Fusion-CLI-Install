# Install Fusion with Data Foundation and Backup & Recovery

## You must have access to IBM repos/pull-secret before starting


1. oc apply -f catalog_source.yaml -n openshift-marketplace
    1. oc get CatalogSources ibm-operator-catalog -n openshift-marketplace -o jsonpath='{.status.connectionState.lastObservedState}{"\n"}'
    2. wait for 'READY'
2. oc create -f isf-ns.yaml
3. oc create -f isf-operator-opgroup.yaml
4. oc create -f isf-operator-sub.yaml
    1. oc get Subscription isf-operator -n ibm-spectrum-fusion-ns -o jsonpath='{.status.conditions[0].message}{"\n"}'
    2. wait for 'all available catalogsources are healthy'
5. oc create -f fusion-instance.yaml
    1. oc get SpectrumFusion spectrumfusion -n ibm-spectrum-fusion-ns -o jsonpath='{.status.status}{"\n"}'
    2. wait for 'Completed'
6. oc label ns openshift-local-storage openshift.io/cluster-monitoring="true"
7. oc label ns openshift-storage openshift.io/cluster-monitoring="true"
8. oc create -f FusionServiceInstance-DF.yaml
    1. wait for Data Foundation and Local Storage Operator install finish
9. Label each Data Foundation node
    1. oc label node \<nodename> cluster.ocs.openshift.io/openshift-storage=''
10. oc create -f lso-localvolset.yaml
    1. oc get pv|grep localblock
    2. wait for all the available disks on the Data Foundation nodes to show up as PVs
11. oc create -f odf-storagecluster.yaml
    1. oc get StorageCluster ocs-storagecluster -n openshift-storage -o jsonpath='{.status.phase}{"\n"}'
    2. wait for 'Completed'
12. oc create -f FusionServiceInstance-BUnR.yaml
    1. oc get FusionServiceInstance ibm-backup-restore-service-instance -n ibm-spectrum-fusion-ns -o jsonpath='{.status.installStatus.progressPercentage}{"\n"}'
    2. wait for output to show '100' percent





