---
title: Nódoa de servidor do Windows RDP para AKS
titleSuffix: Azure Kubernetes Service
description: Saiba como criar uma ligação RDP com os nós do Cluster Windows Server (AKS) do Azure Kubernetes para tarefas de resolução de problemas e manutenção.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 4f221a98adb779d32fc5567b201699672c8b90f5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183282"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Ligue-se com os nós do Serviço de Azure Kubernetes (AKS) do cluster Windows Server para manutenção ou resolução de problemas

Durante todo o ciclo de vida do seu cluster Azure Kubernetes Service (AKS), poderá ter de aceder a um nó AKS Windows Server. Este acesso pode ser para manutenção, recolha de registos ou outras operações de resolução de problemas. Pode aceder aos nós do Servidor AKS Windows utilizando RDP. Em alternativa, se pretender utilizar o SSH para aceder aos nós do Servidor AKS Windows e tiver acesso ao mesmo papel-chave que foi utilizado durante a criação do cluster, pode seguir os passos em [SSH para os nós de cluster Azure Kubernetes Service (AKS).][ssh-steps] Por razões de segurança, os nós AKS não estão expostos à internet.

Este artigo mostra-lhe como criar uma ligação RDP com um nó AKS usando os seus endereços IP privados.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente com um nó do Windows Server. Se precisar de um cluster AKS, consulte o artigo sobre a [criação de um cluster AKS com um recipiente Windows utilizando o Azure CLI][aks-windows-cli]. Precisa do nome de utilizador do administrador do Windows e da palavra-passe para o nó do Servidor do Windows que pretende resolver problemas. Se não os conhecer, pode repor-os seguindo [os Serviços de Ambiente de Trabalho Remoto reset ou a sua palavra-passe de administrador num VM do Windows ](../virtual-machines/troubleshooting/reset-rdp.md). Também precisa de um cliente RDP, como [o Microsoft Remote Desktop.][rdp-mac]

Também precisa da versão Azure CLI 2.0.61 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Implemente uma máquina virtual na mesma sub-rede que o seu cluster

Os nós do Windows Server do seu cluster AKS não têm endereços IP acessíveis externamente. Para escoar uma ligação RDP, pode implantar uma máquina virtual com um endereço IP acessível ao público na mesma sub-rede que os nós do Windows Server.

O exemplo a seguir cria uma máquina virtual chamada *myVM* no grupo de recursos *myResourceGroup.*

Em primeiro lugar, obtenha a sub-rede utilizada pelo seu conjunto de nós do Windows Server. Para obter o id da sub-rede, precisa do nome da sub-rede. Para obter o nome da sub-rede, precisa do nome do vnet. Obtenha o nome vnet consultando o seu cluster para a sua lista de redes. Para consultar o agrupamento, precisa do nome. Você pode obter tudo isto executando o seguinte na Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Agora que tem o SUBNET_ID, erja o seguinte comando na mesma janela Azure Cloud Shell para criar o VM:

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

A saída de exemplo a seguir mostra que o VM foi criado com sucesso e exibe o endereço IP público da máquina virtual.

```console
13.62.204.18
```

Grave o endereço IP público da máquina virtual. Utilizará este endereço mais tarde.

## <a name="allow-access-to-the-virtual-machine"></a>Permitir o acesso à máquina virtual

As subestanets de piscina de nó AKS estão protegidas por padrão com NSGs (Grupos de Segurança de Rede) por padrão. Para ter acesso à máquina virtual, terá de ativar o acesso no NSG.

> [!NOTE]
> Os NSGs são controlados pelo serviço AKS. Qualquer alteração que fizer ao NSG será substituída a qualquer momento pelo avião de controlo.
>

Em primeiro lugar, obtenha o grupo de recursos e o nome nsg do nsg para adicionar a regra a:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Em seguida, crie a regra NSG:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Obtenha o endereço do nó

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o comando [az aks instalar-cli:][az-aks-install-cli]
    
```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Listar o endereço IP interno dos nosmos do Servidor do Windows utilizando o comando [kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

A saída do exemplo segue mostra os endereços IP internos de todos os nós do cluster, incluindo os nós do Servidor do Windows.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Grave o endereço IP interno do nó do Servidor do Windows que deseja resolver problemas. Utilizará este endereço mais tarde.

## <a name="connect-to-the-virtual-machine-and-node"></a>Ligue-se à máquina virtual e ao nó

Ligue-se ao endereço IP público da máquina virtual que criou anteriormente utilizando um cliente RDP, como [o Microsoft Remote Desktop][rdp-mac].

![Imagem de ligação à máquina virtual usando um cliente RDP](media/rdp/vm-rdp.png)

Depois de ter ligado à sua máquina virtual, ligue-se ao *endereço IP interno* do nó do Servidor do Windows que pretende resolver problemas utilizando um cliente RDP dentro da sua máquina virtual.

![Imagem de ligação ao nó do Servidor do Windows utilizando um cliente RDP](media/rdp/node-rdp.png)

Está agora ligado ao nó do Servidor do Windows.

![Imagem da janela cmd no nó do Servidor do Windows](media/rdp/node-session.png)

Pode agora executar quaisquer comandos de resolução de problemas na janela *cmd.* Uma vez que os nós do Windows Server utilizam o Núcleo do Servidor do Windows, não existe uma ferramenta GUI completa ou outras ferramentas GUI quando se liga a um nó do Servidor do Windows sobre RDP.

## <a name="remove-rdp-access"></a>Remover acesso RDP

Quando terminar, saia da ligação RDP ao nó do Servidor do Windows e, em seguida, saia da sessão RDP para a máquina virtual. Depois de sair das duas sessões RDP, elimine a máquina virtual com o comando [az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

E a regra da NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Passos seguintes

Se precisar de dados adicionais de resolução de [problemas, pode ver os registos de nódoseiros mestres de Kubernetes][view-master-logs] ou [o Azure Monitor][azure-monitor-containers].

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