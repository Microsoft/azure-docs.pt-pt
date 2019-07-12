---
title: RDP para nós de servidor do Windows de cluster do Azure Kubernetes Service (AKS)
description: Saiba como criar uma ligação RDP com o cluster do Azure Kubernetes Service (AKS), nós do servidor Windows para tarefas de manutenção e resolução de problemas.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614569"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Ligar com RDP para o Azure Kubernetes Service (AKS) nós do cluster do Windows Server para manutenção ou de resolução de problemas

Em todo o ciclo de vida do cluster do Azure Kubernetes Service (AKS), terá de aceder a um nó de servidor do Windows de AKS. Este acesso pode ser para manutenção, recolha de registos ou outras operações de resolução de problemas. Pode acessar os nós do AKS Windows Server com o protocolo RDP. Em alternativa, se pretender utilizar o SSH para aceder a nós do AKS Windows Server e que tem acesso para o mesmo par de chaves, que foi utilizado durante a criação do cluster, pode seguir os passos em [SSH em nós de cluster do Azure Kubernetes Service (AKS)][ssh-steps]. Por motivos de segurança, os nós do AKS não são expostos à internet.

Suporte de nó do Windows Server está atualmente em pré-visualização no AKS.

Este artigo mostra-lhe como criar uma ligação RDP com um nó do AKS com os respetivos endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente com um nó do Windows Server. Se precisar de um cluster do AKS, consulte o artigo sobre [criar um cluster do AKS com um contentor do Windows com a CLI do Azure][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Também precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Implementar uma máquina virtual para a mesma sub-rede que o seu cluster

Os nós do Windows Server do cluster do AKS não tem endereços IP acessíveis externamente. Para fazer uma ligação RDP, pode implementar uma máquina virtual com um endereço IP acessível publicamente para a mesma sub-rede que os nós do Windows Server.

O exemplo seguinte cria uma máquina virtual com o nome *myVM* no *myResourceGroup* grupo de recursos.

Em primeiro lugar, obtenha a sub-rede utilizada pelo seu conjunto de nós do Windows Server. Para obter o id de sub-rede, terá do nome da sub-rede. Para obter o nome da sub-rede, terá do nome da vnet. Obtenha o nome da vnet ao consultar o seu cluster para a sua lista de redes. Para consultar o cluster, terá de seu nome. Pode obter todas essas ao executar o seguinte no Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Agora que tem o SUBNET_ID, execute o seguinte comando na mesma janela Azure Cloud Shell para criar a VM:

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

O resultado de exemplo seguinte mostra a VM foi criada com êxito e exibe o endereço IP público da máquina virtual.

```console
13.62.204.18
```

Registe o endereço IP público da máquina virtual. Utilizará este endereço num passo posterior.

## <a name="get-the-node-address"></a>Obtenha o endereço do nó

Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. Se utilizar o Azure Cloud Shell, `kubectl` já está instalado. Para instalar `kubectl` localmente, utilize o [az aks install-cli][az-aks-install-cli] comando:
    
```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para ligar ao seu cluster do Kubernetes, utilize o [az aks get-credentials][az-aks-get-credentials] comando. Este comando transfere credenciais e configura a CLI do Kubernetes para utilizá-los.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Lista o endereço IP de nós do Windows Server com o [kubectl obter][kubectl-get] comando:

```console
kubectl get nodes -o wide
```

Resultado de exemplo a seguir mostra os endereços IP internos de todos os nós do cluster, incluindo os nós do Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registe o endereço IP interno do nó do Windows Server que pretende resolver problemas. Utilizará este endereço num passo posterior.

## <a name="connect-to-the-virtual-machine-and-node"></a>Ligar à máquina virtual e do nó

Ligar para o endereço IP público da máquina virtual que criou anteriormente com um cliente RDP como [ambiente de trabalho remoto][rdp-mac].

![Imagem da ligação à máquina virtual utilizando um cliente RDP](media/rdp/vm-rdp.png)

Depois de se ligar à máquina virtual, ligue-se para o *endereço IP interno* do nó do Windows Server que pretende resolver problemas com um cliente RDP a partir da sua máquina virtual.

![Imagem da ligação para o nó de servidor do Windows usando um cliente RDP](media/rdp/node-rdp.png)

Está agora ligado ao nó do Windows Server.

![Imagem da janela de comandos no nó de servidor do Windows](media/rdp/node-session.png)

Agora, pode executar os comandos de resolução de problemas *cmd* janela. Como nós do servidor Windows usam o Windows Server Core, não existe uma GUI completa ou outras ferramentas de GUI quando se liga a um nó do Windows Server através de RDP.

## <a name="remove-rdp-access"></a>Remover o acesso do RDP

Quando terminar, feche a ligação de RDP para o nó do Windows Server em seguida, saia da sessão RDP à máquina virtual. Depois de sair ambas as sessões RDP, eliminar a máquina virtual com o [az vm eliminar][az-vm-delete] comando:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passos seguintes

Se precisar de dados adicionais de resolução de problemas, pode [ver os registos de nó principal do Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

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
