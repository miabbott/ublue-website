# `bluefin-dx` - The Bluefin Developer Experience

Bluefin Developer Experience (`bluefin-dx`) is a dedicated developer image with bundled tools.
Unlike traditional Linux systems, the operating system and developer environment are explicitly and purposely decoupled. 
This means that tooling is not installed on the host, and is instead containerized, in a virtual machine, or scoped to the user's home directory. 
It is designed to meet the following use cases:

- Endeavors to be the world's most powerful [cloud native developer environment](https://landscape.cncf.io/)
- Full virtualization support
- Provide fleet management of Bluefin and other Linux systems via [Cockpit](https://cockpit-project.org/) (Incomplete)

## Instructions

You can rebase to `bluefin-dx` by using the following commands:

- `just devmode-on` will switch a `bluefin` image to a `bluefin-dx` image
- `just devmode-off` will switch a `bluefin-dx` image back to stock `bluefin`

Like all Universal Blue images, switching is atomic, allowing for clean switching between modes depending on the use case.

## Features

Bluefin offers four "default" developer workflows. Since development is not dependant on the operating system image, you can use whatever you want.

### Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) is included on the image to facilitate local development. It comes configured for usage with devcontainers and Podman via a small [default configuration file](https://github.com/ublue-os/bluefin/blob/main/dx/usr/etc/skel.d/.config/Code/User/settings.json).

- [Dev Containers Documentation](https://code.visualstudio.com/docs/devcontainers/containers) - you can skip most of the installation instructions and go directly to [the tutorial](https://code.visualstudio.com/docs/devcontainers/tutorial#_install-the-extension)
- [Dev Containers Specification](https://containers.dev/) 
- [Beginner's Series to: Dev Containers](https://www.youtube.com/watch?v=b1RavPr_878) - great introductory tutorial from the [VS Code YouTube channel](https://www.youtube.com/@code/videos)

### Homebrew

`just brew` will install homebrew, and `just brew-shell` will set it up for you. 

For Mac enthusiasts out there, we know homebrew is essential. We hope this will encourage you to use Linux for future development. Thanks to [Osama Albahrani](https://github.com/osalbahr) for [implementing it](https://github.com/Homebrew/brew/pull/15656) in Homebrew.

### Devpod

[DevPod](https://devpod.sh/docs/what-is-devpod) is included to provide infrastructure-independent and client-only reproducible developer environments, powered by [devcontainers](https://containers.dev/)

- Follow [Quickstart VS Code](https://devpod.sh/docs/getting-started/quickstart-vscode) to set up your envitronment.
  
### Devbox

Nix-powered Development Experience powered by [Devbox](https://www.jetpack.io/devbox) and [Fleek](https://getfleek.dev)

- `just nix-devbox` to get started
- `just nix-devbox-global` to install a global profile

### Containerized Development with Distrobox

- Built-in Ubuntu user space
- <kbd>Ctrl</kbd>-<kbd>Alt</kbd>-<kbd>u</kbd> - will launch an Ubuntu image inside a terminal via [Distrobox](https://github.com/89luca89/distrobox) and your home directory will be transparently mounted for the Ubuntu image to access
- A [BlackBox terminal](https://www.omgubuntu.co.uk/2022/07/blackbox-gtk4-terminal-emulator-for-gnome) is used just for this configuration
- Use this container for your typical CLI needs or to install software that is not available via Flatpak or Fedora
- Optional [ubuntu-toolbox image](https://github.com/ublue-os/bluefin/pkgs/container/ubuntu-toolbox) with Python, and other convenience development tools. `just distrobox-bluefin` to get started. To configure `just` follow the [guide](https://ublue.it/guide/just/).
- Optional [universal image](https://mcr.microsoft.com/en-us/product/devcontainers/universal/about) with Python, Node.js, JavaScript, TypeScript, C++, Java, C#, F#, .NET Core, PHP, Go, Ruby, and and Conda. `just distrobox-universal` to get started
- `just assemble` shortcut to declaratively build distroboxes defined in `/etc/distrobox/distrobox.ini`
- Refer to the [Distrobox documentation](https://distrobox.privatedns.org/#distrobox) for more information on using and configuring custom images
- GNOME Terminal - <kbd>Ctrl</kbd>-<kbd>Alt</kbd>-<kbd>t</kbd> - will launch a host-level GNOME Terminal if you need to do host-level things in Fedora (you shouldn't need to do much).

## Other Tooling

### JetBrains

`just jetbrains-toolbox` will fetch and install the [JetBrains Toolbox](https://www.jetbrains.com/toolbox-app) application, which will manage the installation of the JetBrains set of tools. This application will handle installation, removal, and upgrade of the JetBrains products, and is handled completely in your home directory, independent of the operating system image.

Check the [Jetbrains documentation](https://www.jetbrains.com/help/idea/podman.html) for integrating those tools with the podman runtime. 

### Kubernetes and other Cloud Native Tooling

- [kind](https://kind.sigs.k8s.io/) - Run a Kubernetes cluster on your machine. Run `kind create cluster` on the host to get started!
  - [kubectl](https://kubernetes.io/docs/reference/kubectl/) - Administer Kubernetes Clusters
  - helm, ko, flux, minio-client -- if it's an incubated project we intend to add it where appropriate

### Docker Engine

The most current [Docker Engine](https://docs.docker.com/engine/) is included by default. Use `just docker` to turn it on, this will turn on the service and add your users to the `docker` group. You will then need to log out and back into your desktop. 

To configure Visual Studio Code to use Docker go to the extensions section, and click the gear on the bottom right of the remote containers extension to configure it: 

![image](https://github.com/ublue-os/website/assets/1264109/e0eed8e9-ce92-4d68-92af-fb8d1244fcd9)

Then change the following defaults to point to `docker-compose` and `docker`: 

![image](https://github.com/ublue-os/website/assets/1264109/47bb202a-fc57-4cb0-8f24-340e9d86a250)

### Virtualization and Container Runtimes

- [virt-manager](https://virt-manager.org/) and associated tooling (KVM, qemu)
- [Incus](https://linuxcontainers.org/incus/) provides system containers
  - [LXC](https://linuxcontainers.org/) and [LXD](https://ubuntu.com/lxd) are also provided for compatability reasons 

## Machine Learning

Bluefin includes a NGC container that includes the latest [stable PyTorch from Nvidia](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/pytorch) on top of Ubuntu LTS. It includes `python3`, `pip3` , `pandas`, `matplotlib`, and `scikit-learn`. 

!!! note
 The additional `pip` packages are commonly used but not comprehensive. Only `pip` is used and `mamba` or `conda` are not currently tested.

### Pre-requisites

You must already be using `bluefin-dx-nvidia` as it's meant for those GPUs and has nvidia container toolkit support. If you want to test before downloading the larger nvidia container run `nvidia-smi`
inside a regular Ubuntu box (`distrobox create --nvidia -i ubuntu`) to check that everything works correctly. 

To create the box use the following command. **Note** that this container is large (20+ GB!):

```bash
just distrobox-mlbox
```

To enter the working environment:

```bash
distrobox enter mlbox
```

Then the init-hooks will run once. After which, you should be able to run:

```bash
nvidia-smi
```

To check if GPUs are seen and enter the python repo run:

```bash
import torch;torch.cuda.is_available()
```

Various tests can be run to test a transformers inference or training job, or to git clone a pytorch benchmarks repo and run single or multi gpu commands: E.g. to test multi-gpu setup on two 3090s:

```bash
git clone https://github.com/aime-team/pytorch-benchmarks.git
cd pytorch-benchmarks
python3 main.py --num_gpus 2 --compile --model bert-large-uncased --data_name squad --global_batch_size 24
```

On other operating systems, use [this .ini file](https://github.com/ublue-os/bluefin/blob/730f39caae21e48fb91f00010cf0cf8d32ee44bd/dx/usr/share/ublue-os/distrobox/pytorch-nvidia.ini) and run:

```bash
distrobox assemble create --file /path/to/your/mlbox.ini
```

## Quality of Life Improvements

- [Cockpit](https://cockpit-project.org/) for local and remote management
- A collection of well curated monospace fonts
- systemd shutdown timers adjusted to 15 seconds
- [Tailscale](https://tailscale.com/) for VPN
- [Just](https://github.com/casey/just) task runner for post-install automation tasks. Check out [our documentation](https://universal-blue.org/guide/just/) for more information on using and customizing just.
- `fish` and `zsh` available as optional shells, use `just fish` or `just zsh` and follow the prompts to configure them
