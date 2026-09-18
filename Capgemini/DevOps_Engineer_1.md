# Capgemini

**Exp- 6+ yrs overall experience (considering 3 yrs in devops)**

- How to assign memory to pod and how to make sure if pod should not get memory constraint. What to do if it happens.
- ---------------------------------------------------------------------------------------------------
How to assign memory to a Pod
You assign memory using:
requests → minimum memory the Pod needs
limits → maximum memory the Pod can use
Example:
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: nginx
    resources:
      requests:
        memory: "256Mi"
      limits:
        memory: "512Mi"

What this means Kubernetes guarantees 256Mi memory for the Pod. Pod can grow up to 512Mi.
If it tries to use more than 512Mi, it will be terminated (OOMKilled).
**What happens if memory limit is not set?**
The Pod can use memory beyond 256Mi if available on the node.
**Benefits:**
Application is less likely to be killed because of hitting a limit.
**Risks:**
One Pod can consume too much memory.
Other Pods on the node may suffer.
Node can run out of memory.

-----------------------------------------------------------------------------------------------------
- How to pass variable in azure pipeline? how to parameterize pipeline
- What is availability zone and explain the layout on ground level.(in depth)
- How mysql will interact with azure key vault and it should happen thru privately and should not go anything on public
- Diff between git fetch and git pull (what happens in background in depth)
- What happens behind the hood when git add command is provided. File is added but what happens in the background how git knows on this command it has to add file. ( in dept related to git database)
