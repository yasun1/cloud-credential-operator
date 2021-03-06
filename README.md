# OpenShift Credentials Minter

Cred Minter is a controller that will sync on a CredentailsRequest custom
resource definition. CredentialsRequests allow OpenShift components to
request fine grained credentials for a particular cloud provider.

## Design Principles

  * The controller should be able to run in either a cluster itself, or in a centralized management cluster, most likely along side [Hive](https://github.com/openshift/hive).
  * Controller expects access to a set of credentials we refer to as the "root" credentials.
  * If the root credentials are missing, but all credentials requests are fulfilled and valid, this is considered a valid state. (i.e. the root creds were removed from the cluster after use)
  * If the root credentials are able to create additional credentials, we will create fine grained permissions as defined in the credentials request.
  * If the root credentials cannot create additional credentials, but do themselves fulfill the requirements of the credentials request, they will be used. (with logged warnings and a condition on the credentials request)
  * If the root credentials fulfill neither of the above requirements, the controller will fail to generate the credentials, report failure back to the Cluster Version Operator, and thus block upgrading.

## AWS

Currently AWS is the only supported cloud provider. If running in-cluster, your credentials are expected to exist in kube-system/aws-creds with the keys aws_access_key_id and aws_secret_access_key.

# Running from source

Login to a cluster with admin credentials:

```
$ make install
$ make run
```

# Deploying in cluster

 1. export IMG=quay.io/dgoodwin/cloud-credential-operator:latest
   * You can upload to a personal repo if you wish to build images from source.
 1. make buildah-push
 1. make deploy

Cred Minter should now be running in openshift-cloud-credential-operator.

# Future Work

  1. Integration with the OpenShift Installer.
  1. Cred Minter Operator.
  1. CI + dist-git image publishing.
