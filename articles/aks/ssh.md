---
title: SSH nos nós de cluster do AKS (serviço kubernetes do Azure)
description: Saiba como criar uma conexão SSH com os nós de cluster do AKS (serviço kubernetes do Azure) para tarefas de solução de problemas e manutenção.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/31/2019
ms.author: mlearned
ms.openlocfilehash: d855e7a65b7e1ad24dcfc4fe6a6d5e02f9004bb0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74089548"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Conectar-se com o SSH para os nós de cluster do AKS (serviço kubernetes do Azure) para manutenção ou solução de problemas

Durante todo o ciclo de vida do seu cluster do AKS (serviço kubernetes do Azure), talvez seja necessário acessar um nó do AKS. Esse acesso pode ser para manutenção, coleta de logs ou outras operações de solução de problemas. Você pode acessar nós AKS usando SSH, incluindo nós do Windows Server (atualmente em visualização no AKS). Você também pode [se conectar a nós do Windows Server usando conexões RDP (Remote Desktop Protocol)][aks-windows-rdp]. Para fins de segurança, os nós AKS não são expostos à Internet. Para usar o SSH para os nós AKS, use o endereço IP privado.

Este artigo mostra como criar uma conexão SSH com um nó AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Por padrão, as chaves SSH são obtidas ou geradas e, em seguida, adicionadas aos nós quando você cria um cluster AKS. Este artigo mostra como especificar chaves SSH diferentes das chaves SSH usadas quando você criou o cluster AKS. O artigo também mostra como determinar o endereço IP privado do seu nó e conectar-se a ele usando o SSH. Se você não precisar especificar uma chave SSH diferente, poderá ignorar a etapa para adicionar a chave pública SSH ao nó.

Este artigo também pressupõe que você tenha uma chave SSH. Você pode criar uma chave SSH usando [MacOS ou Linux][ssh-nix] ou [Windows][ssh-windows]. Se você usar gen de geração para criar o par de chaves, salve o par de chaves em um formato OpenSSH em vez do formato de chave privada de geração padrão (arquivo. PPK).

Você também precisa do CLI do Azure versão 2.0.64 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Configurar clusters AKS baseados em conjunto de dimensionamento de máquinas virtuais para acesso SSH

Para configurar o conjunto de dimensionamento de máquinas virtuais baseado para acesso SSH, localize o nome do conjunto de dimensionamento de máquinas virtuais do cluster e adicione sua chave pública SSH a esse conjunto de dimensionamento.

Use o comando [AZ AKs show][az-aks-show] para obter o nome do grupo de recursos do seu cluster AKs e, em seguida, o comando [AZ vmss List][az-vmss-list] para obter o nome do seu conjunto de dimensionamento.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

O exemplo acima atribui o nome do grupo de recursos de cluster para o *myAKSCluster* em *MyResource* Group para *CLUSTER_RESOURCE_GROUP*. Em seguida, o exemplo usa *CLUSTER_RESOURCE_GROUP* para listar o nome do conjunto de dimensionamento e atribuí-lo a *SCALE_SET_NAME*.  

> [!IMPORTANT]
> Neste momento, você só deve atualizar suas chaves SSH para os clusters AKS com base em conjunto de dimensionamento de máquinas virtuais usando o CLI do Azure.
> 
> Para nós do Linux, as chaves SSH só podem ser adicionadas no momento usando o CLI do Azure. Se você quiser se conectar a um nó do Windows Server usando o SSH, use as chaves SSH fornecidas quando você criou o cluster AKS e ignore o próximo conjunto de comandos para adicionar sua chave pública SSH. Você ainda precisará do endereço IP do nó que deseja solucionar problemas, o que é mostrado no comando final desta seção. Como alternativa, você pode [se conectar a nós do Windows Server usando conexões RDP (Remote Desktop Protocol)][aks-windows-rdp] em vez de usar o SSH.

Para adicionar suas chaves SSH aos nós em um conjunto de dimensionamento de máquinas virtuais, use os comandos [AZ vmss Extension Set][az-vmss-extension-set] e [AZ vmss Update-instances][az-vmss-update-instances] .

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

O exemplo acima usa as variáveis *CLUSTER_RESOURCE_GROUP* e *SCALE_SET_NAME* dos comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa. pub* como o local para a chave pública SSH.

> [!NOTE]
> Por padrão, o nome de usuário para os nós AKS é *azureuser*.

Depois de adicionar a chave pública SSH ao conjunto de dimensionamento, você pode usar o SSH em uma máquina virtual do nó nesse conjunto de dimensionamento usando seu endereço IP. Exiba os endereços IP privados dos nós do cluster AKS usando o [comando kubectl Get][kubectl-get].

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós no cluster, incluindo um nó do Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registre o endereço IP interno do nó que você deseja solucionar problemas.

Para acessar seu nó usando o SSH, siga as etapas em [criar a conexão SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Configurar clusters AKS baseados em conjunto de disponibilidade de máquina virtual para acesso SSH

Para configurar o cluster AKS baseado em conjunto de disponibilidade de máquina virtual para acesso SSH, localize o nome do nó do Linux do cluster e adicione sua chave pública SSH a esse nó.

Use o comando [AZ AKs show][az-aks-show] para obter o nome do grupo de recursos do cluster do AKs e, em seguida, o comando [AZ VM List][az-vm-list] para listar o nome da máquina virtual do nó Linux do cluster.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima atribui o nome do grupo de recursos de cluster para o *myAKSCluster* em *MyResource* Group para *CLUSTER_RESOURCE_GROUP*. Em seguida, o exemplo usa *CLUSTER_RESOURCE_GROUP* para listar o nome da máquina virtual. A saída de exemplo mostra o nome da máquina virtual: 

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Para adicionar suas chaves SSH ao nó, use o comando [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

O exemplo acima usa a variável *CLUSTER_RESOURCE_GROUP* e o nome da máquina virtual do nó de comandos anteriores. O exemplo acima também usa *~/.ssh/id_rsa. pub* como o local para a chave pública SSH. Você também pode usar o conteúdo da sua chave pública SSH em vez de especificar um caminho.

> [!NOTE]
> Por padrão, o nome de usuário para os nós AKS é *azureuser*.

Depois de adicionar a chave pública SSH à máquina virtual do nó, você pode usar SSH nessa máquina virtual usando seu endereço IP. Exiba o endereço IP privado de um nó de cluster AKS usando o comando [AZ VM List-IP-addresses][az-vm-list-ip-addresses] .

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

O exemplo acima usa a variável *CLUSTER_RESOURCE_GROUP* definida nos comandos anteriores. A saída de exemplo a seguir mostra os endereços IP privados dos nós AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Criar a conexão SSH

Para criar uma conexão SSH com um nó AKS, você executa um pod auxiliar em seu cluster AKS. Esse Pod auxiliar fornece acesso SSH ao cluster e, em seguida, acesso ao nó SSH adicional. Para criar e usar esse Pod auxiliar, conclua as seguintes etapas:

1. Execute uma `debian` imagem de contêiner e anexe uma sessão de terminal a ela. Esse contêiner pode ser usado para criar uma sessão SSH com qualquer nó no cluster AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se você usar nós do Windows Server (atualmente em visualização no AKS), adicione um seletor de nó ao comando para agendar o contêiner Debian em um nó do Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Depois que a sessão de terminal estiver conectada ao contêiner, instale um cliente SSH usando `apt-get`:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Abra uma nova janela de terminal, não conectada ao seu contêiner, liste o pods em seu cluster AKS usando o comando [kubectl Get pods][kubectl-get] . O Pod criado na etapa anterior começa com o nome *AKs-SSH*, conforme mostrado no exemplo a seguir:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Em uma etapa anterior, você adicionou sua chave SSH pública para o nó AKS que você queria solucionar problemas. Agora, copie sua chave SSH privada para o Pod auxiliar. Essa chave privada é usada para criar o SSH no nó AKS.

    Forneça seu próprio nome de Pod *AKs-SSH* obtido na etapa anterior. Se necessário, altere *~/.ssh/id_rsa* para o local da sua chave SSH privada:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Retorne à sessão de terminal para seu contêiner, atualize as permissões na chave SSH privada `id_rsa` copiada para que ela seja somente leitura do usuário:

    ```console
    chmod 0600 id_rsa
    ```

1. Crie uma conexão SSH para o nó AKS. Novamente, o nome de usuário padrão para nós AKS é *azureuser*. Aceite o prompt para continuar com a conexão, pois a chave SSH é confiável pela primeira vez. Em seguida, você será fornecido com o prompt do bash do seu nó AKS:

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

Quando terminar, `exit` a sessão SSH e, em seguida, `exit` a sessão de contêiner interativa. Quando essa sessão de contêiner é fechada, o Pod usado para acesso SSH do cluster AKS é excluído.

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais dados de solução de problemas, você poderá [exibir os logs do kubelet][view-kubelet-logs] ou [exibir os logs do nó mestre do kubernetes][view-master-logs].

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
