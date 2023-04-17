# Cost Management Demo

## Prerequisites

You will need a free Red Hat account for this demo.  If you work for Red Hat, do *not* use an account linked to your *redhat.com* email address, as that will force you to use SSO with MFA.  For this demo you need to be able to create a credentials secret with a standard username and password.

## Red Hat Account Credentials Secret

**DO NOT CHECK THIS FILE INTO GIT!**

In the `instance` directory, create a credentials secret named `credentials-secret.yaml` with contents like this:

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

Of course, substitute your own Red Hat username and password.  This file is specifically added to the `.gitignore` file to make sure you dont' accidentally commit this to your own git repository.

## Install the Cost Management Metrics Operator

Next, install the *Cost Management Metrics Operator*.  You can do this through the OperatorHub interface in the OpenShift Admin UI, or you can do it the GitOps way and run the following command:

```
oc apply -k overlays/operator
```

After a minute or two, the operator will be installed and ready.

## Create a Cost Management Operator Instance

Next, create a Cost Management Operator Instance.  There are a few things to consider when doing so:

### Credentials

By default, the 
