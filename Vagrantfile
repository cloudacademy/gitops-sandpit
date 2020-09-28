Vagrant.configure("2") do |config|
  config.vm.box = "cloudacademydevops.k8s"

  config.vm.provision "shell", inline: <<-SHELL
    kubectl create ns flux
    kubectl create ns cloudacademy

    helm repo add fluxcd https://charts.fluxcd.io
    helm repo update

    #NOTE: Fork the cloudacademy/gitops-demo repo
    #then update the following line with your forked version
    FORKED_GITOPS_DEMO_REPO=github.com:cloudacademy/gitops-demo

    helm install flux --set git.url=git@$FORKED_GITOPS_DEMO_REPO --namespace flux fluxcd/flux --version 1.5.0

    echo waiting for flux pods to launch...
    kubectl wait --timeout=300s --for=condition=available deploy flux -n flux
    kubectl wait --timeout=300s --for=condition=available deploy flux-memcached -n flux
    echo flux ready!!
    echo
    echo ============================
    echo copy this SSH key and configure as a new Deploy key in the $FORKED_GITOPS_DEMO_REPO GitHub repo
    kubectl -n flux logs deployment/flux | grep identity.pub | cut -d '"' -f2 | cut -d " " -f1,2
    echo ============================
    echo
    echo login into vagrant box:
    echo vagrant ssh
    echo   kubectl -n flux get all
    echo
    echo finished!
  SHELL
end
