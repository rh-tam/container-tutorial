# Database on OpenShift  Tutorial
In this exercise, you will create and deploy a MySQL database pod on OpenShift using the `oc new-app` command.

## Getting an Environment
```
export USER=user1
export CID=8b3b
export MASTER_API=https://api.cluster-${CID}.gcp.testdrive.openshift.com:6443


oc login -u ${USER} -p openshift ${MASTER_API}
oc new-project ${USER}-mysql
```

## create new project

### Create a new application from the `rhscl/mysql-57-rhel7` container image using the oc new-app command.

This image requires that you use the -e option to set the MYSQL_USER, MYSQL_PASSWORD, MYSQL_DATABASE, and MYSQL_ROOT_PASSWORD environment variables.

Use the --docker-image option with the oc new-app command to specify the classroom private registry URI so that OpenShift does not try and pull the image from the internet:

```bash
oc new-app --as-deployment-config \
--docker-image=registry.access.redhat.com/rhscl/mysql-57-rhel7:latest \
--name=mysql-openshift \
-e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=testdb \
-e MYSQL_ROOT_PASSWORD=r00tpa55
```

### Verify that the MySQL pod was created successfully and view the details about the pod and its service

- Run the `oc status`
- List the pods `oc get pods`
- Use the `oc describe` command to view more details about the pod
- List the services in this project `oc get svc`
- Retrieve the details of the `mysql-openshift` service `oc describe svc mysql-openshift`
- View details about the deployment `oc describe dc mysql-openshift`

## Expose the service
creating a route with a default name and a fully qualified domain name (FQDN)

```bash
oc expose service mysql-openshift
```

check the Route
```
oc get routes
```

## Validate
Connect to the MySQL database server and verify that the database was created successfully

```
oc port-forward mysql-openshift-1-xxxx 3306:3306
```

Use another terminal to validate the mysql.
Of course, you should have the mysql client in advance.
```
dnf install mysql
```

```
mysql -uuser1 -pmypa55 --protocol tcp -h localhost
```

## Delete and Finish
Delete the project to remove all the resources within the project
```
oc delete project ${USER}-mysql
```