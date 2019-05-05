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
   
#### Add Prometheus Monitoring and Grafana Dashboards


   
   
  
