# Useful kubectl commands for troubleshooting

You can use the Kubernetes CLI, kubectl, to connect to the Kubernetes server and troubleshoot or debug problems with your pods and containers. The following kubectl commands are useful:

* get pods
* logs
* exec
* debug

**Tip:** Start with the simple `kubectl get pods` command, then run `kubectl logs`, then `kubectl exec`. In cases where these three commands don't provide enough information, try using the more advanced `kubectl debug` command. 

The following sections provide introductory reference information for these four commands. For more commands, complete syntax, and examples, see https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands. Another basic but informative command is `kubectl describe`.


## get pods

Get a list of all available pods and their status. You might need to specify the namespace for the command. 

For example, list the pods in the namespace named `myk8s_namespace` by running the following command:

```shell
kubectl get pods -n myk8s_namespace
```

This command returns output that looks like this:

```shell
NAME                       READY   STATUS    RESTARTS   AGE
web-7c9d8f6b6b-abc12      1/1     Running   0           18m
api-5d4f7b8c9d-xyz34      1/1     Running   3           2h
worker-6f7a8b9c0d-lmn56    0/1     Pending   0           7m
```

## logs

Return the log output (stdout and stderr) for a specific pod and its containers. Use this command to review log history or debug problems with a pod or a container inside a pod. Specify the pod name and the namespace in the command. For example:

```shell
kubectl logs web-7c9d8f6b6b-abc12 -n myk8s_namespace
```

This command returns output that looks like this:

```shell
Starting web server...
Listening on port 8080
GET /health 200
GET / 200
```

You can also use the `-c` argument to specify a container.

## exec

Start an interactive session or run a specific command inside a running container to get information about the environment or explore what might be wrong with it. Specify the pod name and the namespace. For example:

```shell
kubectl exec web-7c9d8f6b6b-abc12 -n myk8s_namespace -- printenv
```

This example executes the `printenv` command inside the pod’s container and returns output that looks like this: 

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

Troubleshoot pods by creating interactive debugging sessions or temporary debugging containers. For example, the following command creates a debugging container that uses BusyBox command-line tools (such as `ls`, `ps`, and `sh`). You can troubleshoot your environment although the original container doesn't have access to a shell or these debugging tools. 

```shell
kubectl debug -n myk8s_namespace pod/web-7c9d8f6b6b-abc12 --image=busybox -it --target=web -- sh

```

This command returns output that looks like this:

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
