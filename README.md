# sftp
Sample SFTP on Openshift


This aims to be a sample and teach you how to deploy an sftp on Openshift, I used an article from the [medium blog](https://medium.com/compendium/install-an-sftp-server-on-openshift-818ea30a4319)


To start we need to create two ConfigMap, the first will be used to create the sftp users.

Creating the sftp-etc-sftp:
```yaml
oc apply -f sftp-etc-sftp-cm.yaml
```

The second one will be used to keep the ssh keys, without persistent ssh keys, the end-users will get login errors after a pod restart due to changed host keys.
The content/value of these files could be lifted from the running pod via Pods > sftp-N-AAAA > Terminal and the cat-commands:
```console
cat /etc/ssh/ssh_host_ed25519_key
cat /etc/ssh/ssh_host_ed25519_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/sshd_config
```
After that replace the values in the file sftp-etc-ssh.yaml
```yaml

oc apply -f sftp-etc-ssh.yaml
```
Also, you can generate new keys with the following two commands. Use empty passphrases. Then cat their content:
```terminal
ssh-keygen -t ed25519 -f ssh_host_ed25519_key < /dev/null
ssh-keygen -t rsa -b 4096 -f ssh_host_rsa_key < /dev/null
```
Allow running as root
Will change the policy for the default user in your current project/namespace (int-sftp) to run as any user ID (root):
```console
oc adm policy add-scc-to-user anyuid -z default
scc “anyuid” added to: [“system:serviceaccount:int-sftp:default”]
```

Finally, you can create the deployment.
```console
oc apply -f deployment.yaml
```
