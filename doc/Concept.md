
### Comparison table

| Criteria                          | Minikube                                                                                          | Kind (Kubernetes IN Docker)                                                                        | K3d (K3s in Docker)                                                                                |
|-----------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Description**                   | Minikube is a tool that runs a single-node Kubernetes cluster inside a VM on your local machine.  | Kind is a tool for running local Kubernetes clusters using Docker container “nodes”.              | K3d is a lightweight wrapper to run K3s (Rancher Lab’s minimal Kubernetes distribution) in Docker containers. |
| **Purpose**                       | To provide a local Kubernetes cluster primarily for development and testing purposes.             | To facilitate Kubernetes development and testing by creating clusters within Docker containers.   | To create and manage lightweight Kubernetes clusters for local development and CI/CD pipelines.   |
| **Supported OS**                  | Windows, macOS, Linux                                                                             | Windows, macOS, Linux                                                                             | Windows, macOS, Linux                                                                             |
| **Supported CPU Architectures**   | x86-64, ARM                                                                                       | x86-64, ARM                                                                                       | x86-64, ARM                                                                                       |
| **Automation Possibilities**      | Supports scripting and automation through its CLI.                                                | Highly scriptable via its CLI and easily integrates into CI pipelines.                            | Highly scriptable via its CLI, designed to integrate into CI/CD workflows.                        |
| **Cluster Monitoring Functions**  | Can be configured with various monitoring tools (e.g., Prometheus, Grafana).                      | Requires additional setup for monitoring tools.                                                    | Requires additional setup for monitoring tools.                                                    |
| **Cluster Control Functions**     | Offers a comprehensive set of cluster control functions.                                          | Basic cluster control functions available, with more control possible via Kubernetes APIs.        | Basic cluster control functions available, with more control possible via Kubernetes APIs.        |
| **Ease of Use**                   | User-friendly with a variety of options and a straightforward setup process.                      | Simple to use, especially for Docker users; no need for VM setup.                                  | Simple to use, particularly for lightweight clusters; minimal configuration required.              |
| **Speed of Deployment**           | Moderate (depends on VM creation and configuration).                                              | Fast (leverages Docker containers).                                                               | Very fast (leverages lightweight K3s and Docker containers).                                      |
| **Stability**                     | Stable, with a long history and active development.                                               | Stable, but may require more manual intervention for certain use cases.                           | Stable, leveraging the robustness of K3s within Docker containers.                                |
| **Documentation and Community**   | Extensive documentation and a large, active community.                                            | Good documentation and a growing community.                                                       | Good documentation and community support, but slightly smaller than Minikube's.                   |
| **Complexity of Configuration**   | Can be complex due to various configuration options and VM dependencies.                          | Relatively simple configuration, primarily around Docker.                                         | Simple configuration, focused on lightweight Kubernetes clusters.                                |

### Pros and Cons

| Criteria                          | Minikube                                                                                          | Kind                                                                                              | K3d                                                                                               |
|-----------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Pros**                          | - User-friendly<br>- Extensive documentation<br>- Wide community support                         | - Lightweight<br>- Fast deployment<br>- Integrates well with Docker                               | - Very lightweight<br>- Extremely fast deployment<br>- Good for CI/CD<br>- Minimal resource usage |
| **Cons**                          | - Requires VM, which can be resource-intensive<br>- Slower deployment compared to container-based solutions | - May need more manual setup for advanced use cases<br>- Smaller community than Minikube         | - Smaller community<br>- Limited to K3s features, which may differ from full Kubernetes          |

### Docker VS Podman

There exists `docker` alternative, - `podman`. Main advantages are free license as it is opensource and additional security as it does not require to run daemon process with root privileges.
While docker needs a daemon process to run. And docker commercial usage requires licensing. See more details [here](https://www.docker.com/pricing/)

After experiments with Podman a few issues were encountered:
* It can not be installed at Ubuntu Linux that runs at Github Codespaces. While at OSX laptop it can insalled easily along with Desktop App and CLI utility.
* In order to let it run with `k3d` for example it requires some hacking. `k3d` documentation warns that [Podman support is experimental](https://k3d.io/v5.4.1/usage/advanced/podman/)
* `kind` can't load podman images, needs some extraction into archive and still it does not work.
* All present kubernetes ecosystem mostly relies on Docker while Podman support is experimental.

### Kind + Podman

At my OSX laptop it was possible to create clusted via `kind` + `podman`. [Read more here](https://kind.sigs.k8s.io/docs/user/rootless/)
But still it warns that it is an experiamental feature:
```
KIND_EXPERIMENTAL_PROVIDER=podman kind create cluster --name kind-podman-test-cluster
using podman due to KIND_EXPERIMENTAL_PROVIDER
enabling experimental podman provider
Creating cluster "kind-podman-test-cluster-2" ...
 ✓ Ensuring node image (kindest/node:v1.30.0) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
```

### Minikube + Podman   

It also it warns that it is an experimental feature and moreover it got got some errors at startup.

```
minikube start -p minikube-test-cluster --driver=podman --container-runtime=cri-o
😄  [minikube-test-cluster] minikube v1.33.1 on Darwin 14.5 (arm64)
✨  Using the podman (experimental) driver based on user configuration
📌  Using Podman driver with root privileges
👍  Starting "minikube-test-cluster" primary control-plane node in "minikube-test-cluster" cluster
🚜  Pulling base image v0.0.44 ...
💾  Downloading Kubernetes v1.30.0 preload ...
    > preloaded-images-k8s-v18-v1...:  350.34 MiB / 350.34 MiB  100.00% 22.83 M
E0724 01:46:59.396978   32970 cache.go:189] Error downloading kic artifacts:  not yet implemented, see issue #8426
🔥  Creating podman container (CPUs=2, Memory=3570MB) ...
🎁  Preparing Kubernetes v1.30.0 on CRI-O 1.24.6 ...
E0724 01:47:29.518474   32970 start.go:131] Unable to get host IP: RoutableHostIPFromInside is currently only implemented for linux
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube-test-cluster" cluster and "default" namespace by default
```

### k3d + Podman at macOS
Experimental configuration is shown [here](https://k3d.io/v5.6.3/usage/advanced/podman/#macos)
It seem it is too much docker dependand, that it requires some hacking to substitute docker environemnt variables in order to get k3d working with podman.
At my local configuration I did not get it working at OSX.

```
DOCKER_SOCK=/run/podman/podman.sock DOCKER_HOST=ssh://root@127.0.0.1:60305 k3d cluster create k3d-podman-test
INFO[0000] Prep: Network
INFO[0001] Created network 'k3d-k3d-podman-test'
INFO[0001] Created image volume k3d-k3d-podman-test-images
INFO[0002] Starting new tools node...
INFO[0002] Creating node 'k3d-k3d-podman-test-server-0'
INFO[0003] Creating LoadBalancer 'k3d-k3d-podman-test-serverlb'
INFO[0004] Starting node 'k3d-k3d-podman-test-tools'
ERRO[0004] Failed to run tools container for cluster 'k3d-podman-test'
ERRO[0004] failed to ensure tools node: failed to run k3d-tools node for cluster 'k3d-podman-test': failed to start node 'k3d-k3d-podman-test-tools': runtime failed to start node 'k3d-k3d-podman-test-tools': docker failed to start container for node 'k3d-k3d-podman-test-tools': Error response from daemon: failed to create new hosts file: unable to replace "host-gateway" of host entry "host.k3d.internal:host-gateway": host containers internal IP address is empty
ERRO[0004] Failed to create cluster >>> Rolling Back
INFO[0004] Deleting cluster 'k3d-podman-test'
INFO[0007] Deleting cluster network 'k3d-k3d-podman-test'
INFO[0007] Deleting 1 attached volumes...
FATA[0007] Cluster creation FAILED, all changes have been rolled back!
```

So `podman` does not seem to be a good option for start-up project where speed of development matters and so infrastructure should be able to be maintained smoothly as well.

### Decision Graph
TBD (Provide options, decisions and conditions via draw.io)

### Recommended Tool
`k3d` is ligtweight solution with minimal set of core features that shoud be enough for MVP and PoC start-up project phases.
And later on in case of project success and growth it will be possible to migrate into full-fledged and heavy `k8s` solution.

### Usage Demo

![Image](.data/k3d-test-demo.gif)

[asciinema demo link](https://asciinema.org/a/KDYmKLnz0TPskucNIakHYs20O)