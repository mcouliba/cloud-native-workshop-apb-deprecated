Environment Installer
=========

The provided Ansible Playbook Bundle (APB) automates preparing an OpenShift cluster 
by deploying required services (lab instructions, Gogs, Nexus, etc).

# Build and Deploy the APB on Openshift

## Prepare (Only if you change the apb.yml file)
First update the `com.redhat.apb.spec` LABEL from the Dockerfile file with a base64 encoded version of apb.yml
```bash
$ git clone https://github.com/<your username>/cloud-native-development-apb.git
$ cd cloud-native-development-apb
$ sed -i '.bak' "s/LABEL \"com.redhat.apb.spec\"=.*/LABEL \"com.redhat.apb.spec\"=\"$(cat apb.yml | base64)\"/g" Dockerfile
$ git commit
$ git push
```

```dockerfile
# Dockerfile
FROM ansibleplaybookbundle/apb-base

LABEL "com.redhat.apb.spec"="dmVyc2lvbjogMS4wCm5hbWU6IG15LXRlc3QtYXBiCmRlc2NyaXB0aW9uOiBUaGlzIGlzIGEgc2Ft..."

COPY playbooks /opt/apb/actions
ADD requirements.yml /opt/apb/actions/requirements.yml
RUN ansible-galaxy install -r /opt/apb/actions/requirements.yml -f
RUN chmod -R g=u /opt/{ansible,apb}
USER apb
```

## Build and Deploy
```bash
$ oc new-build https://github.com/mcouliba/cloud-native-development-apb \
    --name=cloud-native-development-apb \
    -n openshift
```

## List
Querying the ansible service broker will now show your new apb listed.
```bash
$ oc get images | grep cloud-native-development-apb
```

Visiting the OpenShift console UI will now display the new Ansible Playbook Bundle named "Cloud Native Development" in the catalog under the **_All_** tab and **_Other_** tab.
