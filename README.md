# operator-lazar
An experimental Kubernetes [operator] for the [lazar] predictive toxicology
service. For an introduction to operators and the operator SDK your first
port-of-call should be the Operator SDK [User Guide].

>   Here we use the **Ansible** variant of the operator framework,
    which means we don't have to know anything about **Go**, only **Ansible**
    and has its own [Ansible Operator Guide].

## Prerequisites
You will need the Operator SDK if you are going to build
the operator (you don't need the SDK to use the operator).

There is an [installation guide] fop the SDK, on brew (OSX) you could run: -

    $ brew install operator-sdk

## Building the operator
The operator is built with the SDK and deployed to Docker Hub. The operator
has already been deployed so you only need to follow this step if you've
changed the operator or the lazar role.

    $ operator-sdk build informaticsmatters/lazar-operator:0.0.1
    $ docker push informaticsmatters/lazar-operator:0.0.1

## Deploying the operator (OpenShift)
Create the Custom Resource Definition and deploy the service-account
role, operator etc.: -

    $ oc create -f deploy/crds/infra_v1alpha1_infra_crd.yaml \
                -f deploy/service_account.yaml \
                -f deploy/role.yaml \
                -f deploy/role_binding.yaml \
                -f deploy/operator.yaml

This operator requires **cluster-admin** and **anyuid** policies on
the service-account: -

    $ oc adm policy add-role-to-user admin -z lazar-operator
    $ oc adm policy add-cluster-role-to-user admin -z lazar-operator
    $ oc adm policy add-scc-to-user anyuid -z lazar-operator
    
Then, to deploy/apply lazar, apply the Custom Resource: -

    $ oc apply -f deploy/crds/infra_v1alpha1_infra_cr.yaml

To un-deploy lazar you can reply on the role's built-in ability to
delete the objects it created by setting the custom resource `state` value to
`absent`: -

    spec:
      state: absent

And then re-apply the resource: -

    $ oc apply -f deploy/crds/infra_v1alpha1_infra_cr.yaml

---

[ansible oeprator guide]: https://github.com/operator-framework/operator-sdk/blob/master/doc/ansible/user-guide.md
[installation guide]: https://github.com/operator-framework/operator-sdk/blob/master/doc/user/install-operator-sdk.md
[lazar]: https://github.com/OpenRiskNet/home/tree/master/openshift/deployments/lazar
[operator]: https://www.openshift.com/learn/topics/operators
[user guide]: https://github.com/operator-framework/operator-sdk/blob/master/doc/user-guide.md
