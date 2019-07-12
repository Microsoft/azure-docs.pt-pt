---
title: SSH para nós de cluster do Azure Kubernetes Service (AKS)
description: Saiba como criar uma ligação SSH connosco de cluster do Azure Kubernetes Service (AKS) para tarefas de manutenção e resolução de problemas.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614487"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ligar com SSH para o Azure Kubernetes Service (AKS) nós de cluster para manutenção ou de resolução de problemas

Em todo o ciclo de vida do cluster do Azure Kubernetes Service (AKS), terá de aceder a um nó AKS. Este acesso pode ser para manutenção, recolha de registos ou outras operações de resolução de problemas. Pode acessar nós do AKS com o SSH, incluindo a nós do Windows Server (atualmente em pré-visualização no AKS). Também pode [ligar a nós do Windows Server através de ligações de protocolo de ambiente de trabalho remoto (RDP)][aks-windows-rdp]. Por motivos de segurança, os nós do AKS não são expostos à internet.

Este artigo mostra-lhe como criar uma ligação SSH com um nó do AKS com os respetivos endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Também precisa da versão 2.0.64 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Adicionar a chave SSH pública

Por predefinição, as chaves SSH são obtidas, ou geradas, em seguida, adicionadas para nós, quando cria um cluster do AKS. Se tiver de especificar chaves SSH diferentes que são utilizados quando criou o seu cluster do AKS, adicione a chave SSH pública para os nós do AKS do Linux. Se for necessário, pode criar um através do chaves de SSH [macOS ou Linux][ssh-nix] or [Windows][ssh-windows]. Se utilizar o PuTTY fins para criar o par de chaves, guarde o par de chaves num OpenSSH formato em vez do padrão de formato de chave privada (ficheiro. ppk) PuTTy.

> [!NOTE]
> Pode de chaves SSH atualmente só ser adicionada para nós do Linux com a CLI do Azure. Se usar nós do servidor Windows, utilize as chaves SSH que indicou quando criou o cluster do AKS e avance para o passo no [como obter o endereço do nó AKS](#get-the-aks-node-address). Ou, [ligar a nós do Windows Server através de ligações de protocolo de ambiente de trabalho remoto (RDP)][aks-windows-rdp].

Os passos para obter o endereço IP privado de nós do AKS é diferente com base no tipo de cluster do AKS executar:

* Para a maior parte dos clusters do AKS, siga os passos para [obter o endereço IP para os clusters do AKS regulares](#add-ssh-keys-to-regular-aks-clusters).
* Se utilizar quaisquer funcionalidades de pré-visualização no AKS que utilizam conjuntos de dimensionamento de máquinas virtuais, como vários conjuntos de nós ou o suporte para contentores do Windows Server, [siga os passos para dimensionar de máquina virtual com base no conjunto de clusters AKS](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Adicionar chaves SSH para regulares clusters do AKS

Para adicionar a chave SSH para um nó de AKS do Linux, execute os seguintes passos:

1. Obter o nome do grupo de recursos para os seus recursos de cluster do AKS usando [show do az aks][az-aks-show]. O nome do cluster é atribuído à variável com o nome *CLUSTER_RESOURCE_GROUP*. Substitua *myResourceGroup* com o nome do grupo de recursos onde está localizado, o Cluster do AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Listar as VMs a através do grupo de recursos do cluster AKS a [lista de VMS de az][az-vm-list] comando. Estas VMs são os nós do AKS:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    O resultado de exemplo seguinte mostra os nós do AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Para adicionar as chaves SSH para o nó, utilize o [atualização do utilizador az vm][az-vm-user-update] comando. Forneça o nome do grupo de recursos e, em seguida, um de nós do AKS que obteve no passo anterior. Por predefinição, é o nome de utilizador para os nós do AKS *azureuser*. Forneça o local do seu próprio SSH pública localização da chave, como *~/.ssh/id_rsa.pub*, ou cole o conteúdo da sua chave pública SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Adicionar chaves SSH para dimensionar de máquina virtual com base no conjunto de clusters AKS

Para adicionar a chave SSH para um nó de Linux AKS que faz parte de um conjunto de dimensionamento de máquinas virtuais, conclua os seguintes passos:

1. Obter o nome do grupo de recursos para os seus recursos de cluster do AKS usando [show do az aks][az-aks-show]. O nome do cluster é atribuído à variável com o nome *CLUSTER_RESOURCE_GROUP*. Substitua *myResourceGroup* com o nome do grupo de recursos onde está localizado, o Cluster do AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Em seguida, obter o conjunto de dimensionamento para o seu cluster do AKS com o [lista de vmss az][az-vmss-list] comando. O nome de conjunto de dimensionamento de máquina virtual é atribuído à variável com o nome *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Para adicionar as chaves SSH para os nós num conjunto de dimensionamento de máquina virtual, utilize o [conjunto de extensão az vmss][az-vmss-extension-set] comando. O grupo de recursos de cluster e o nome do conjunto de dimensionamento de máquina virtual são fornecidos nos comandos anteriores. Por predefinição, é o nome de utilizador para os nós do AKS *azureuser*. Se for necessário, atualize a localização do seu próprio SSH pública localização da chave, como *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Aplicam-se a chave SSH para os nós usando o [az vmss update-instances][az-vmss-update-instances] comando:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Obter o endereço do nó AKS

Os nós do AKS não sejam publicamente expostos à internet. Para encaminhar o SSH para os nós do AKS, utilize o endereço IP privado. O próximo passo, vai criar um pod de auxiliar no cluster do AKS que lhe permite SSH para este endereço IP privado do nó. Os passos para obter o endereço IP privado de nós do AKS é diferente com base no tipo de cluster do AKS executar:

* Para a maior parte dos clusters do AKS, siga os passos para [obter o endereço IP para os clusters do AKS regulares](#ssh-to-regular-aks-clusters).
* Se utilizar quaisquer funcionalidades de pré-visualização no AKS que utilizam conjuntos de dimensionamento de máquinas virtuais, como vários conjuntos de nós ou o suporte para contentores do Windows Server, [siga os passos para dimensionar de máquina virtual com base no conjunto de clusters AKS](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>SSH para regulares clusters do AKS

Ver o endereço IP privado de um cluster do AKS nó com o [az vm lista-ip-addresses][az-vm-list-ip-addresses] command. Provide your own AKS cluster resource group name obtained in a previous [az-aks-show][az-aks-show] passo:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O resultado de exemplo seguinte mostra os endereços IP privados de nós do AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH para dimensionar de máquina virtual com base no conjunto de clusters AKS

Lista o endereço IP de nós com o [kubectl obter comando][kubectl-get]:

```console
kubectl get nodes -o wide
```

Resultado de exemplo a seguir mostra os endereços IP internos de todos os nós do cluster, incluindo um nó do Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

O endereço IP interno do nó que pretende resolver problemas do registo. Utilizará este endereço num passo posterior.

## <a name="create-the-ssh-connection"></a>Criar a ligação de SSH

Para criar uma ligação SSH a um nó do AKS, é possível executar um pod de auxiliar no cluster do AKS. Este pod auxiliar fornece acesso SSH para o cluster e, em seguida, adicional acesso de nó SSH. Para criar e utilizar esta pod auxiliar, conclua os seguintes passos:

1. Executar um `debian` contentor de imagens e anexar uma sessão de terminal a ele. Este contentor pode ser utilizado para criar uma sessão SSH com qualquer nó do cluster do AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se utilizar nós de servidor do Windows (atualmente em pré-visualização no AKS), adicione um Seletor de nó para o comando para agendar o contentor Debian num nó de Linux da seguinte forma:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. A imagem base do Debian não inclui os componentes SSH. Depois da sessão de terminal é ligada para o contentor, instalar um cliente SSH utilizando `apt-get` da seguinte forma:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Numa nova janela do terminal, não ligado ao seu contentor, lista os pods no seu cluster do AKS com o [kubectl obter pods][kubectl-get] comando. O pod criado no passo anterior inicia com o nome *aks-ssh*, conforme mostrado no exemplo a seguir:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. No primeiro passo deste artigo, adicionou a chave pública de SSH do nó AKS. Agora, copie a chave SSH privada para o pod. Esta chave privada é utilizada para criar o SSH para os nós do AKS.

    Fornecer seu próprio *aks-ssh* nome de pod obtido no passo anterior. Se for necessário, altere *~/.ssh/id_rsa* para a localização da chave privada SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Novamente na sessão do terminal para o seu contentor, atualizar as permissões no copiado `id_rsa` da chave SSH privada para que ele é o utilizador só de leitura:

    ```console
    chmod 0600 id_rsa
    ```

1. Agora, crie uma ligação SSH ao nó do AKS. Novamente, é o nome de utilizador predefinido para nós do AKS *azureuser*. Aceite o pedido para continuar com a ligação, como a chave SSH é a primeira fidedigno. , Em seguida, é fornecidos com a linha de comandos bash do nó do AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Remover o acesso SSH

Quando terminar, `exit` a sessão SSH e, em seguida, `exit` a sessão interativa do contentor. Quando fecha-se esta sessão de contentor, o pod utilizado para acesso SSH do AKS cluster é eliminado.

## <a name="next-steps"></a>Passos seguintes

Se precisar de dados adicionais de resolução de problemas, pode [ver os registos kubelet][view-kubelet-logs] or [view the Kubernetes master node logs][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
