# Cost Management Demo

## Prerequisites

You will need a free Red Hat account for this demo.  If you work for Red Hat, do *not* use an account linked to your *redhat.com* email address, as that will force you to use SSO with MFA.  For this demo you need to be able to create a credentials secret with a standard username and password.

## Red Hat Account Credentials Secret

**DO NOT CHECK THIS FILE INTO GIT!**

In the `base/instance` directory, create a credentials secret named `credentials-secret.yaml` with contents like this:

```
apiVersion: v1
kind: Secret
metadata:
  name: redhat-credentials
  namespace: costmanagement-metrics-operator
type: Opaque
stringData:
  password: <your Red Hat username>
  username: <your Red Hat password>
```

Of course, substitute your own Red Hat username and password.  This file is specifically added to the `.gitignore` file to make sure you don't accidentally commit this to your own git repository.

## Install the Cost Management Metrics Operator

Next, install the *Cost Management Metrics Operator*.  You can do this through the OperatorHub interface in the OpenShift Admin UI, or you can do it the GitOps way and run the following command:

```
oc apply -k overlays/operator
```

After a minute or two, the operator will be installed and ready.

## Create a Cost Management Operator Instance

Next, create a Cost Management Operator Instance.  There are a few things to consider when doing so:

### Credentials

By default, the operator will use `token` based authentication.  Nothing needs to be done when using this method if your cluster is already registered with OpenShift Cluster Manager.

In this demo, you may want to have cost metrics sent to a different Red Hat account (for example, if you use a demo cluster and want metrics sent to your own Red Hat account).  In this case, you will specifiy "basic" authentication in the cost management config CRD and reference the name of the `Secret` containing your username and password.

### CostMangementMetricsConfig

For this demo, the "base" `CostManagementMetricsConfig` CRD looks like this:

```
kind: CostManagementMetricsConfig
apiVersion: costmanagement-metrics-cfg.openshift.io/v1beta1
metadata:
  name: costmanagementmetrics
  namespace: costmanagement-metrics-operator
spec:
  authentication:
    type: basic
    secret_name: redhat-credentials
  packaging:
    max_reports_to_store: 30
    max_size_MB: 100
  prometheus_config: {}
  source:
    check_cycle: 1440
    create_source: true
    name: Replace Me With Unique Name Per Cluster
  upload:
    upload_cycle: 20
    upload_toggle: true
```

There are a few differences between this and the "default" instance you might use:

1. `authentication` stanza is set to `basic` and references a secret named `redhat-credentials` where your username and password are stored.
2. `create_source` is set to `true`.  This will automatically create a new "source" in your console.redhat.com dashboard.
3. `upload_cycle` is set to `20`.  This means a new set of metrics will be uploaded every 20 minutes.  The default is `360` (6 hours), which is too long for a demo!
4. The `name` is *Replace Me With Unique Name Per Cluster* this will be patched in the overlay for each environment.

### Create a DEV Cost Management Cluster Config Instance

To deploy an instance in your "DEV" cluster, run the following command:

```
oc apply -k overlays/instance/dev
```

This will create the instance and patch in a new name of "DEV - Cost Demo".  This is imporatant, since the name needs to be unique for each cluster.

## Now Wait...

It takes some time for Metrics to start showing up in your Cost Management console.  This will generally take between 30-60 minitues for everything to appear.  If you are running this as your own demo, make sure you set this up well ahead of time.



