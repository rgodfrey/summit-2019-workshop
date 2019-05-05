# AMQ Online
## Designing Cloud-Based Messaging Services for Business and IoT
### Red Hat Summit 2019 Workshop

#### Install EnMasse

1. Get the EnMasse 0.28 bundle
   ```
   curl -LO https://github.com/EnMasseProject/enmasse/releases/download/0.28.0/enmasse-0.28.0.tgz
   ```

2. Unpack  
   ```
   tar xzf enmasse-0.28.0.tgz
   ```

3. Log in to OpenShift as a user with `cluster-admin` role, and create a new project:
   ```
   oc login -u system:admin
   oc new-project enmasse-infra
   ```
      
4. Deploy the bundle
   ```
   cd enmasse-0.28.0
   oc apply -f install/bundles/enmasse
   ```
      
5. Install example plans and infrastructure configuration:
   ```
   oc apply -f install/components/example-plans
   ```
   
6. Install the authentication services:
   ```
   oc apply -f install/components/example-authservices
   ```
   
7. Install service admin and tenant roles
   ```
   oc apply -f install/components/example-roles
   ```
   
8. Get the console route, and note it down
   ```
   oc get route console
   ```
   
   the output should look something like:
   
   ```
   NAME      HOST/PORT                                     PATH      SERVICES   PORT      TERMINATION   WILDCARD
   console   console-enmasse-infra.192.168.42.233.nip.io             console    https     reencrypt     None
   ```
   
   For this example, the AMQ Console can be reached at `https:://console-enmasse-infra.192.168.42.233.nip.io`
   
#### Add Prometheus Monitoring and Grafana Dashboards

1. Update the monitoring bundle from this repository
   ```
   rm -rf install/bundles/monitoring
   curl -L https://raw.githubusercontent.com/rgodfrey/summit-2019-workshop/master/updated-monitoring.tgz | tar xf -
   ```
   
2. Install the monitoring operator
   ```
   oc new-project enmasse-monitoring
   oc apply -f install/components/monitoring-operator
   ```
  
3. Deploy the kube-state-metrics agent
   ```
   oc project enmasse-infra
   oc apply -f install/components/kube-state-metrics
   ```
   
4. Reinstall monitoring operator if necessary
   _(due to a race condition in the installation, the operator may need to be re-installed)_
   
   a. check how many pods are running in the monitoring project
   ```
   oc get pods -n enmasse-monitoring
   ```
   b. if only the two operator pods exist, then reinstall
   ```
   oc delete project enmasse-monitoring
   oc new-project enmasse-monitoring
   oc apply -f install/components/monitoring-operator
   ```![Alt text](/relative/path/to/img.jpg?raw=true "Optional Title")
   
#### Modify the Address Space Plans
   
1. Create a new file `direct-only-plan.yaml` with the following content

   ```
   apiVersion: admin.enmasse.io/v1beta2
   kind: AddressSpacePlan
   metadata:
     name: direct-only
     labels:
       app: enmasse
   spec:
     displayName: Direct Only
     displayOrder: 0
     infraConfigRef: default
     shortDescription: Messaging infrastructure based on Apache Qpid Dispatch Router and Apache ActiveMQ Artemis.
     longDescription: Messaging infrastructure based on Apache Qpid Dispatch Router and Apache ActiveMQ Artemis. This plan allows up to 3 routers but does not allow for any store-and-forward addressing.
     addressSpaceType: standard
     resourceLimits:
       router: 3.0
       broker: 0.0
       aggregate: 3.0
     addressPlans:
     - standard-small-anycast
     - standard-small-multicast
   ```
   
2. Create the plan
   ```
   oc apply -f direct-only-plan.yaml
   ```
3. Check the plan is present
   ```
   oc get addressspaceschema standard -o jsonpath='{.spec.plans[*].name}' | xargs -n1 | sort
   ```
   should return something like the below (i.e. including the new plan)
   ```
   direct-only
   standard-medium
   standard-small
   standard-unlimited
   standard-unlimited-with-mqtt
   ```
4. Use the console to create a new address space (as a different user, e.g. developer) using this plan

![Create An Address Space](/images/create-direct.gif?raw=true)

5. You can also delete plans
   ```
   oc delete addressspaceplan standard-unlimited-with-mqtt
   ```
   
#### As a tenant, create an address space and send/receive messages

Use the examples at https://github.com/EnMasseProject/enmasse-example-clients
   

   
   
  
