Minikube

Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
minikube start --vm-driver "hyperv" --hyperv-virtual-switch "VM-External-Switch"
minikube start

minikube.exe status
kubectl.exe cluster-info
