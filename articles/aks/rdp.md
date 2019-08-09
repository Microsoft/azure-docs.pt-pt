---
title: RDP nos nós de cluster do Windows Server do AKS (serviço de kubernetes do Azure)
description: Saiba como criar uma conexão RDP com os nós do Windows Server do cluster do serviço de kubernetes do Azure (AKS) para tarefas de solução de problemas e manutenção.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "67614569"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Conectar-se ao RDP para os nós do Windows Server do cluster do AKS (serviço de kubernetes do Azure) para manutenção ou solução de problemas

Durante todo o ciclo de vida do seu cluster AKS (serviço kubernetes do Azure), talvez seja necessário acessar um nó AKS do Windows Server. Esse acesso pode ser para manutenção, coleta de logs ou outras operações de solução de problemas. Você pode acessar os nós AKS do Windows Server usando o RDP. Como alternativa, se você quiser usar o SSH para acessar os nós do Windows Server AKS e tiver acesso ao mesmo par de chaves que foi usado durante a criação do cluster, poderá seguir as etapas no [SSH nos nós de cluster do AKS (serviço kubernetes do Azure)][ssh-steps]. Para fins de segurança, os nós AKS não são expostos à Internet.

No momento, o suporte a nós do Windows Server está em versão prévia no AKS.

Este artigo mostra como criar uma conexão RDP com um nó AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente com um nó do Windows Server. Se você precisar de um cluster AKS, consulte o artigo sobre como [criar um cluster AKs com um contêiner do Windows usando o CLI do Azure][aks-windows-cli]. Você precisa do nome de usuário e senha do administrador do Windows para o nó do Windows Server que você deseja solucionar problemas. Você também precisa de um cliente RDP, como [área de trabalho remota da Microsoft][rdp-mac].

Você também precisa do CLI do Azure versão 2.0.61 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Implantar uma máquina virtual na mesma sub-rede que o cluster

Os nós do Windows Server do cluster AKS não têm endereços IP acessíveis externamente. Para fazer uma conexão RDP, você pode implantar uma máquina virtual com um endereço IP acessível publicamente para a mesma sub-rede que os nós do Windows Server.

O exemplo a seguir cria uma máquina virtual chamada *myVM* no grupo de recursos MyResource Group.

Primeiro, obtenha a sub-rede usada pelo pool de nós do Windows Server. Para obter a ID de sub-rede, você precisa do nome da sub-rede. Para obter o nome da sub-rede, você precisa do nome da vnet. Obtenha o nome da vnet consultando seu cluster para sua lista de redes. Para consultar o cluster, você precisa de seu nome. Você pode obter tudo isso executando o seguinte no Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Agora que você tem o SUBNET_ID, execute o seguinte comando na mesma janela Azure Cloud Shell para criar a VM:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

A saída de exemplo a seguir mostra que a VM foi criada com êxito e exibe o endereço IP público da máquina virtual.

```console
13.62.204.18
```

Registre o endereço IP público da máquina virtual. Você usará esse endereço em uma etapa posterior.

## <a name="get-the-node-address"></a>Obter o endereço do nó

Para gerenciar um cluster kubernetes, você usa [kubectl][kubectl], o cliente de linha de comando kubernetes. Se você usar o Azure cloud Shell `kubectl` , o já estará instalado. Para instalar `kubectl` localmente, use o comando [AZ AKs install-CLI][az-aks-install-cli] :
    
```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` o para se conectar ao cluster do kubernetes, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Esse comando baixa as credenciais e configura a CLI do kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Liste o endereço IP interno dos nós do Windows Server usando o comando [kubectl Get][kubectl-get] :

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós no cluster, incluindo os nós do Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registre o endereço IP interno do nó do Windows Server que você deseja solucionar problemas. Você usará esse endereço em uma etapa posterior.

## <a name="connect-to-the-virtual-machine-and-node"></a>Conectar-se à máquina virtual e ao nó

Conecte-se ao endereço IP público da máquina virtual que você criou anteriormente usando um cliente RDP, como [área de trabalho remota da Microsoft][rdp-mac].

![Imagem de conexão à máquina virtual usando um cliente RDP](media/rdp/vm-rdp.png)

Depois de se conectar à sua máquina virtual, conecte-se ao *endereço IP interno* do nó do Windows Server que você deseja solucionar problemas usando um cliente RDP de dentro de sua máquina virtual.

![Imagem de conexão com o nó do Windows Server usando um cliente RDP](media/rdp/node-rdp.png)

Agora você está conectado ao seu nó do Windows Server.

![Imagem da janela cmd no nó do Windows Server](media/rdp/node-session.png)

Agora você pode executar qualquer comando de solução de problemas na janela *cmd* . Como os nós do Windows Server usam o Windows Server Core, não há uma GUI completa ou outras ferramentas de GUI quando você se conecta a um nó do Windows Server por RDP.

## <a name="remove-rdp-access"></a>Remover acesso RDP

Quando terminar, saia da conexão RDP para o nó do Windows Server e saia da sessão RDP para a máquina virtual. Depois de sair das duas sessões RDP, exclua a máquina virtual com o comando [AZ VM Delete][az-vm-delete] :

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais dados de solução de problemas, você poderá [exibir os logs do nó mestre do kubernetes][view-master-logs] ou [Azure monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
