---
title: SSH em Nódos de Cluster Azure Kubernetes Service (AKS)
description: Aprenda a criar uma ligação SSH com os nós de cluster azure Kubernetes Service (AKS) para tarefas de resolução de problemas e manutenção.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593636"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ligue-se com os nós de cluster SSH a Azure Kubernetes Service (AKS) para manutenção ou resolução de problemas

Ao longo do ciclo de vida do seu cluster Azure Kubernetes Service (AKS), poderá ter de aceder a um nó AKS. Este acesso pode ser para manutenção, recolha de registos ou outras operações de resolução de problemas. Pode aceder aos nós AKS utilizando sSH, incluindo os nós do Windows Server (atualmente em pré-visualização no AKS). Também pode [ligar-se aos nós do Windows Server utilizando ligações remotas][aks-windows-rdp]de protocolo de ambiente de trabalho (RDP). Para fins de segurança, os nós aks não estão expostos à internet. Para sSH para os nós AKS, você usa o endereço IP privado.

Este artigo mostra-lhe como criar uma ligação SSH com um nó AKS usando os seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Por padrão, as teclas SSH são obtidas, ou geradas, e depois adicionadas aos nós quando cria um cluster AKS. Este artigo mostra-lhe como especificar diferentes teclas SSH do que as teclas SSH utilizadas quando criou o seu cluster AKS. O artigo também mostra como determinar o endereço IP privado do seu nó e ligar-se ao mesmo usando SSH. Se não precisar especificar uma tecla SSH diferente, então poderá saltar o passo para adicionar a chave pública SSH ao nó.

Este artigo também assume que você tem uma chave SSH. Pode criar uma tecla SSH utilizando [macOS ou Linux][ssh-nix] ou [Windows][ssh-windows]. Se utilizar o PuTTY Gen para criar o par de chaves, guarde o par chave num formato OpenSSH em vez do formato de chave privada padrão PuTTy (ficheiro.ppk).

Também precisa da versão 2.0.64 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Configure clusters AKS baseados em escala de máquina virtual para acesso ssh

Para configurar o conjunto de conjuntos de escala de máquina virtual baseado sipara o acesso sSH, encontre o nome do conjunto de escala de máquina virtual do seu cluster e adicione a sua chave pública SSH a esse conjunto de escala.

Use o comando [az aks mostrar][az-aks-show] para obter o nome de grupo de recursos do seu cluster AKS, em seguida, o comando da [lista az vmss][az-vmss-list] para obter o nome do seu conjunto de escala.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

O exemplo acima atribui o nome do grupo de recursos cluster para o *myAKSCluster* no *myResourceGroup* para *CLUSTER_RESOURCE_GROUP*. O exemplo utiliza *então CLUSTER_RESOURCE_GROUP* para listar o nome do conjunto de escala e atribuí-lo a *SCALE_SET_NAME*.

> [!IMPORTANT]
> Neste momento, só deverá atualizar as suas teclas SSH para os seus clusters AKS baseados em escala de máquina virtual utilizando o Azure CLI.
> 
> Para os nós Linux, as teclas SSH só podem ser adicionadas utilizando o Azure CLI. Se pretender ligar-se a um nó do Windows Server utilizando o SSH, utilize as teclas SSH fornecidas quando criou o cluster AKS e ignore o próximo conjunto de comandos para adicionar a sua chave pública SSH. Ainda vai precisar do endereço IP do nó que deseja resolver problemas, o que é mostrado no comando final desta secção. Em alternativa, pode [ligar-se aos nós do Windows Server utilizando ligações remotas][aks-windows-rdp] de protocolo de ambiente de trabalho (RDP) em vez de utilizar o SSH.

Para adicionar as suas teclas SSH aos nós num conjunto de escala de máquina virtual, utilize o conjunto de [extensão az vmss][az-vmss-extension-set] e [comandos az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

O exemplo acima utiliza as *variáveis CLUSTER_RESOURCE_GROUP* e *SCALE_SET_NAME* dos comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa.pub* como o local para a sua chave pública SSH.

> [!NOTE]
> Por padrão, o nome de utilizador dos nós AKS é *azureuser*.

Depois de adicionar a sua chave pública SSH ao conjunto de escala, pode sSH numa máquina virtual de nó nessa escala definida utilizando o seu endereço IP. Veja os endereços IP privados dos nós do cluster AKS utilizando o [comando do kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

A saída de exemplo seguinte mostra os endereços IP internos de todos os nós do cluster, incluindo um nó do Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Grave o endereço IP interno do nó que deseja resolver.

Para aceder ao nó utilizando SSH, siga os passos na [Create the SSH .](#create-the-ssh-connection)

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Configure clusters AKS baseados em conjunto de disponibilidade de máquinas virtuais para acesso ssh

Para configurar o seu cluster AKS baseado em conjunto de disponibilidade de máquinavirtual para acesso sSH, encontre o nome do nó Linux do seu cluster e adicione a sua chave pública SSH a esse nó.

Use o comando [az aks para][az-aks-show] obter o nome de grupo de recursos do seu cluster AKS, em seguida, o comando da [lista az vm][az-vm-list] para listar o nome da máquina virtual do nó Linux do seu cluster.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima atribui o nome do grupo de recursos cluster para o *myAKSCluster* no *myResourceGroup* para *CLUSTER_RESOURCE_GROUP*. O exemplo usa então *CLUSTER_RESOURCE_GROUP* para listar o nome da máquina virtual. A saída de exemplo mostra o nome da máquina virtual:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Para adicionar as suas teclas SSH ao nó, utilize o comando de atualização do [utilizador az vm.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

O exemplo acima usa a variável *CLUSTER_RESOURCE_GROUP* e o nome da máquina virtual nó de comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa.pub* como o local para a sua chave pública SSH. Também pode utilizar o conteúdo da sua chave pública SSH em vez de especificar um caminho.

> [!NOTE]
> Por padrão, o nome de utilizador dos nós AKS é *azureuser*.

Depois de adicionar a sua chave pública SSH à máquina virtual do nó, pode sSH naquela máquina virtual utilizando o seu endereço IP. Veja o endereço IP privado de um nó de cluster AKS utilizando o comando [az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima usa a *variável CLUSTER_RESOURCE_GROUP* definida nos comandos anteriores. A saída de exemplo a seguir mostra os endereços IP privados dos nódeos AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Criar a ligação SSH

Para criar uma ligação SSH a um nó AKS, você executa uma cápsula de ajuda no seu cluster AKS. Este casulo de ajuda fornece-lhe acesso SSH ao cluster e, em seguida, acesso adicional ao nó SSH. Para criar e utilizar esta cápsula de ajuda, complete os seguintes passos:

1. Execute uma imagem de recipiente `debian` e prenda-lhe uma sessão terminal. Este recipiente pode ser utilizado para criar uma sessão sSH com qualquer nó no cluster AKS:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se utilizar os nós do Windows Server (atualmente em pré-visualização no AKS), adicione um seletor de nó ao comando para agendar o recipiente Debian num nó Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Uma vez que a sessão terminal esteja ligada ao recipiente, instale um cliente SSH utilizando `apt-get`:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Abra uma nova janela de terminal, não ligada ao seu recipiente, copie a sua chave SSH privada para a cápsula de ajuda. Esta chave privada é usada para criar o SSH no nó AKS. 

   Se necessário, altere *~/.ssh/id_rsa* para a localização da sua chave SSH privada:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Volte à sessão terminal para o seu recipiente, atualize as permissões na chave SSH privada copiada `id_rsa` para que seja apenas leitura do utilizador:

    ```console
    chmod 0600 id_rsa
    ```

1. Crie uma ligação SSH ao seu nó AKS. Mais uma vez, o nome de utilizador padrão para nós AKS é *azureuser*. Aceite a solicitação para continuar com a ligação, uma vez que a chave SSH é fidedigna pela primeira vez. Em seguida, é-lhe fornecido o golpe do seu nó AKS:

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

## <a name="remove-ssh-access"></a>Remover o acesso sSH

Quando terminar, `exit` a sessão sSH e, em seguida, `exit` a sessão de contentores interativos. Quando esta sessão de contentores fechar, a cápsula utilizada para o acesso sSH do cluster AKS é eliminada.

## <a name="next-steps"></a>Passos seguintes

Se precisar de dados adicionais de resolução de problemas, pode ver os registos de [kubelet][view-kubelet-logs] ou [ver os registos de nó mestre kubernetes][view-master-logs].

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
