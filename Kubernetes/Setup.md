Install and configure prerequisites

Forwarding IPv4 and letting iptables see bridged traffic :
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

lsmod | grep br_netfilter
lsmod | grep overlay

sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward


Installing CRI-O Container Runtime:

export OS=xUbuntu_22.04
export CRIO_VERSION=1.24

echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$CRIO_VERSION/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION.list

curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION/$OS/Release.key | sudo apt-key add -
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key add -

sudo apt update
sudo apt install cri-o cri-o-runc

sudo systemctl start crio
sudo systemctl enable crio
sudo systemctl status crio


Installing CNI (Container Network Interface) Plugin:
The CNI (Container Network Interface) is required for the CRI-O container runtime. The CNI plugin allows you to set up networking on the container and Pods. You will install the CNI plugin from the official ubuntu repository and set it up with the CRI-O container runtime.

sudo apt install containernetworking-plugins

sudo nano /etc/crio/crio.conf

On the "[crio.network]" section, uncomment the "network_dir" and "plugin_dirs" option. Also, be sure to add the CNI plugin directory "/usr/lib/cni/" to the "plugin_dirs" option.
```
# The crio.network table containers settings pertaining to the management of
# CNI plugins.
[crio.network]

# The default CNI network name to be selected. If not set or "", then
# CRI-O will pick-up the first one found in network_dir.
# cni_default_network = ""

# Path to the directory where CNI configuration files are located.
network_dir = "/etc/cni/net.d/"

# Paths to directories where CNI plugin binaries are located.
plugin_dirs = [
        "/opt/cni/bin/",
        "/usr/lib/cni/",
]
```

rm -f /etc/cni/net.d/100-crio-bridge.conf
sudo curl -fsSLo /etc/cni/net.d/11-crio-ipv4-bridge.conf https://raw.githubusercontent.com/cri-o/cri-o/main/contrib/cni/11-crio-ipv4-bridge.conf

sudo systemctl restart crio
sudo systemctl status crio

sudo apt install cri-tools
crictl version
crictl info

crictl completion > /etc/bash_completion.d/crictl
source ~/.bashrc
crictl TAB

sudo crictl pull nginx
sudo crictl images
