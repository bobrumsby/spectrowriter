# kubectl Troubleshooting Commands

You can use the Kubernetes CLI, `kubectl`, to connect to the Kubernetes server and troubleshoot or debug problems with your pods and containers. The following `kubectl` commands are useful:

- `get pods`
- `logs`
- `exec`
- `debug`

**Tip:** Start with the simple `get pods` command, then issue `logs`, then `exec`. If these commands do not provide enough information, try using the more advanced `debug` command. 

The following sections provide introductory reference information for these four commands. For complete syntax, more examples, and information about other commands, see [kubectl Commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands).

## get pods

Get a list of all available pods and their status. You might need to specify the namespace for the command. 

For example, list the pods in the namespace `myk8s_namespace` by issuing the following command:

```shell
kubectl get pods -n myk8s_namespace
```

### Sample Output

```shell
NAME                       READY   STATUS    RESTARTS   AGE
web-7c9d8f6b6b-abc12       1/1     Running   0          18m
api-5d4f7b8c9d-xyz34       1/1     Running   3          2h
worker-6f7a8b9c0d-lmn56    0/1     Pending   0          7m
```

## logs

Return the log output (`stdout` and `stderr`) for a specific pod and its containers. Use this command to review log history or to debug problems with either a pod or a container inside a pod. Specify the pod name and the namespace in the command. For example:

```shell
kubectl logs web-7c9d8f6b6b-abc12 -n myk8s_namespace
```

### Sample Output

```shell
Starting web server...
Listening on port 8080
GET /health 200
GET / 200
```
You can also use the `-c` argument to specify a container.

## exec

Start an interactive session or issue a specific command inside a running container to get information about the environment or explore what might be wrong with it. Specify the pod name and the namespace. For example:

```shell
kubectl exec web-7c9d8f6b6b-abc12 -n myk8s_namespace -- printenv
```

This example issues the `printenv` command inside the pod's container.

### Sample Output

```shell
HOSTNAME=web-7c9d8f6b6b-abc12
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOME=/root
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT_HTTPS=443
MY_APP_ENV=production
MY_APP_LOG_LEVEL=info
```

You can also use the `-c` argument to specify a container.

## debug

Troubleshoot pods by creating interactive debugging sessions or temporary debugging containers. For example, the following command creates a debugging container that uses BusyBox command-line tools (such as `ls`, `ps`, and `sh`). Although the original container doesn't have access to a shell or these debugging tools, you can use them to troubleshoot your environment.

```shell
kubectl debug -n myk8s_namespace pod/web-7c9d8f6b6b-abc12 --image=busybox -it --target=web -- sh
```



### Sample Output

```shell
Defaulting debug container name to debugger-8f7c2.
If you don’t see a command prompt, try pressing enter.

/ #
/ # ps
PID   USER     TIME  COMMAND
1     root      0:00  app
7     root      0:00  sh
/ # ls
bin   dev   etc   proc  sbin  sys  tmp  usr  var
/ # env | head
HOSTNAME=web-7c9d8f6b6b-abc12
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOME=/root
KUBERNETES_SERVICE_HOST=10.96.0.1
/ # cat /proc/1/status | head
Name:   app
State:  S (sleeping)
Pid:    1
```

