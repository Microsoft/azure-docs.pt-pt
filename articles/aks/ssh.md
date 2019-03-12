---
title: SSH para nós de cluster do Azure Kubernetes Service (AKS)
description: Saiba como criar uma ligação SSH connosco de cluster do Azure Kubernetes Service (AKS) para tarefas de manutenção e resolução de problemas.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538804"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ligar com SSH para o Azure Kubernetes Service (AKS) nós de cluster para manutenção ou de resolução de problemas

Em todo o ciclo de vida do cluster do Azure Kubernetes Service (AKS), terá de aceder a um nó AKS. Este acesso pode ser para manutenção, recolha de registos ou outras operações de resolução de problemas. Os nós do AKS são VMs do Linux, para que pode acessá-los através de SSH. Por motivos de segurança, os nós do AKS não são expostos à internet.

Este artigo mostra-lhe como criar uma ligação SSH com um nó do AKS com os respetivos endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Adicionar a chave SSH pública

Por predefinição, as chaves SSH são geradas quando criar um cluster do AKS. Se não especificou suas próprias chaves SSH quando criou o seu cluster do AKS, adicione as chaves SSH públicas para os nós do AKS.

Para adicionar a chave SSH para um nó do AKS, conclua os seguintes passos:

1. Obter o nome do grupo de recursos para os seus recursos de cluster do AKS usando [show do az aks][az-aks-show]. Fornece seu próprio grupo de recursos de núcleos e o nome de cluster do AKS:

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Listar as VMs a através do grupo de recursos do cluster AKS a [lista de VMS de az] [ az-vm-list] comando. Estas VMs são os nós do AKS:

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    O resultado de exemplo seguinte mostra os nós do AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Para adicionar as chaves SSH para o nó, utilize o [atualização do utilizador az vm] [ az-vm-user-update] comando. Forneça o nome do grupo de recursos e, em seguida, um de nós do AKS que obteve no passo anterior. Por predefinição, é o nome de utilizador para os nós do AKS *azureuser*. Forneça o local do seu próprio SSH pública localização da chave, como *~/.ssh/id_rsa.pub*, ou cole o conteúdo da sua chave pública SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Obter o endereço do nó AKS

Os nós do AKS não sejam publicamente expostos à internet. Para encaminhar o SSH para os nós do AKS, utilize o endereço IP privado. O próximo passo, vai criar um pod de auxiliar no cluster do AKS que lhe permite SSH para este endereço IP privado do nó.

Ver o endereço IP privado de um cluster do AKS nó com o [az vm lista-ip-addresses] [ az-vm-list-ip-addresses] comando. Fornecer seu próprio nome de grupo do recurso do AKS cluster obtido no anterior [az-aks-show] [ az-aks-show] passo:

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

O resultado de exemplo seguinte mostra os endereços IP privados de nós do AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Criar a ligação de SSH

Para criar uma ligação SSH a um nó do AKS, é possível executar um pod de auxiliar no cluster do AKS. Este pod auxiliar fornece acesso SSH para o cluster e, em seguida, adicional acesso de nó SSH. Para criar e utilizar esta pod auxiliar, conclua os seguintes passos:

1. Executar um `debian` contentor de imagens e anexar uma sessão de terminal a ele. Este contentor pode ser utilizado para criar uma sessão SSH com qualquer nó do cluster do AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. A imagem base do Debian não inclui os componentes SSH. Depois da sessão de terminal é ligada para o contentor, instalar um cliente SSH utilizando `apt-get` da seguinte forma:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Numa nova janela do terminal, não ligado ao seu contentor, lista os pods no seu cluster do AKS com o [kubectl obter pods] [ kubectl-get] comando. O pod criado no passo anterior inicia com o nome *aks-ssh*, conforme mostrado no exemplo a seguir:

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

## <a name="next-steps"></a>Passos Seguintes

Se precisar de dados adicionais de resolução de problemas, pode [ver os registos kubelet] [ view-kubelet-logs] ou [ver os registos de nó principal do Kubernetes][view-master-logs].

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
