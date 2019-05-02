---
title: Encaminhar o tráfego de rede - CLI do Azure | Documentos da Microsoft
description: Neste artigo, saiba como encaminhar o tráfego de rede com uma tabela de rotas com a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ff5897766bb56b76a34940ecd786773fd844a336
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683105"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Encaminhar o tráfego de rede com uma tabela de rotas com a CLI do Azure

O Azure encaminha automaticamente o tráfego entre todas as sub-redes numa rede virtual, por predefinição. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. A capacidade de criar rotas personalizadas é útil se, por exemplo, pretender encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede (NVA). Neste artigo, vai aprender a:

* Criar uma tabela de rotas
* Criar uma rota
* Criar uma rede virtual com várias sub-redes
* Associar uma tabela de rotas a uma sub-rede
* Criar uma NVA que encaminha o tráfego
* Implementar máquinas virtuais (VMs) em sub-redes diferentes
* Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes de poder criar uma tabela de rotas, crie um grupo de recursos com [criar grupo az](/cli/azure/group) para todos os recursos criados neste artigo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Criar uma tabela de rotas com [criar tabela de rotas do rede az](/cli/azure/network/route-table#az-network-route-table-create). O exemplo seguinte cria uma tabela de rotas com o nome *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Criar uma rota

Criar uma rota na tabela de rotas com [criar rota de tabela de rotas de rede de az](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, tem de criar uma rede virtual e uma sub-rede. Criar uma rede virtual com uma sub-rede com [vnet de rede de az criar](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Criar duas sub-redes adicionais com [criar a sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Associar o *myRouteTablePublic* tabela de rotas para o *público* sub-rede com [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como encaminhamento, proteção com firewalls ou otimização de WAN.

Criar uma NVA na *DMZ* sub-rede com [az vm criar](/cli/azure/vm). Quando cria uma VM, o Azure cria e atribui um endereço IP público à VM, por predefinição. O `--public-ip-address ""` parâmetro instrui o Azure não para criar e atribuir um endereço IP público à VM, uma vez que a VM não tem de estar ligado a partir da internet. Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Não avance para o passo seguinte enquanto o Azure concluir a criação da VM e devolve resultados sobre a VM. 

Para que as interfaces de rede possam reencaminhar o tráfego de rede que lhes é enviado e que não seja destinado para o seu próprio endereço IP, o reencaminhamento de IP tem de estar ativado nas mesmas. Ativar o reencaminhamento de IP para a interface de rede com [atualização de nic de rede de az](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dentro da VM, é necessário que o sistema operativo, ou uma aplicação em execução na mesma, consiga também reencaminhar o tráfego de rede. Ativar o reencaminhamento de IP dentro do sistema de operativo da VM com [conjunto de extensão az vm](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
O comando pode demorar até um minuto para executar.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que possa validar que o tráfego dos *pública* sub-rede é encaminhada para o *privada* através da NVA num passo posterior. 

Criar uma VM no *pública* sub-rede com [az vm criar](/cli/azure/vm). O `--no-wait` parâmetro permite ao Azure a executar o comando em segundo plano, para poder continuar para o próximo comando. Para simplificar este artigo, é utilizada uma palavra-passe. As chaves são normalmente utilizadas em implementações de produção. Se usar chaves, também tem de configurar o reencaminhamento de agentes SSH. Para obter mais informações, consulte a documentação do seu cliente SSH. Substitua `<replace-with-your-password>` no comando seguinte com uma palavra-passe à sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Criar uma VM no *privada* sub-rede.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A criação da VM demora alguns minutos. Depois da VM é criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Anote o **publicIpAddress**. Este endereço é utilizado para aceder à VM a partir da internet num passo posterior.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

Utilize o seguinte comando para criar uma sessão SSH com o *myVmPrivate* VM. Substitua  *\<publicIpAddress >* com o endereço IP público da sua VM. No exemplo acima, é o endereço IP *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando lhe for pedido para uma palavra-passe, introduza a palavra-passe que selecionou no [criar máquinas virtuais](#create-virtual-machines).

Utilize o seguinte comando para instalar o encaminhamento de rastreio no *myVmPrivate* VM:

```bash 
sudo apt-get install traceroute
```

Utilize o seguinte comando para testar o encaminhamento para tráfego de rede para o *myVmPublic* VM a partir do *myVmPrivate* VM.

```bash
traceroute myVmPublic
```

A resposta é semelhante ao seguinte exemplo:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Pode ver que o tráfego é encaminhado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Rotas de padrão do Azure, encaminhar o tráfego diretamente entre sub-redes. 

Utilize o seguinte comando para encaminhar o SSH para o *myVmPublic* VM a partir do *myVmPrivate* VM:

```bash 
ssh azureuser@myVmPublic
```

Utilize o seguinte comando para instalar o encaminhamento de rastreio no *myVmPublic* VM:

```bash 
sudo apt-get install traceroute
```

Utilize o seguinte comando para testar o encaminhamento para tráfego de rede para o *myVmPrivate* VM a partir do *myVmPublic* VM.

```bash
traceroute myVmPrivate
```

A resposta é semelhante ao seguinte exemplo:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez com que o Azure encaminhe o tráfego através da NVA, em vez de diretamente para a sub-rede *Privada*.

Fechar as sessões SSH para ambos os *myVmPublic* e *myVmPrivate* VMs.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [eliminação do grupo de az](/cli/azure/group) para remover o grupo de recursos e todos os recursos nele contidos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou uma tabela de rotas e associou-o a uma sub-rede. Criou uma NVA simples que encaminhou o tráfego de uma sub-rede pública para uma privada. Implementou uma variedade de NVAs pré-configuradas que realizam funções de rede, como proteção com firewall e otimização de WAN, a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa implementar muitos recursos do Azure dentro de uma rede virtual, não é possível implementar recursos de alguns serviços PaaS do Azure nas redes virtuais. Contudo, pode restringir o acesso aos recursos de alguns serviços PaaS do Azure ao tráfego apenas a partir de uma sub-rede de rede virtual. Para saber como, veja [restringir o acesso à rede a recursos de PaaS](tutorial-restrict-network-access-to-resources-cli.md).
