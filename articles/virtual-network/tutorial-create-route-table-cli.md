---
title: Tráfego da rede de rotas - Azure CLI [ Microsoft Docs
description: Neste artigo, aprenda a encaminhar o tráfego da rede com uma tabela de rotas utilizando o Azure CLI.
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
ms.openlocfilehash: 5fa94b93e081ab6334c39b848068f50682f5f1f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235059"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Tráfego de rede de rotas com uma tabela de rotas utilizando o Azure CLI

O Azure encaminha automaticamente o tráfego entre todas as sub-redes numa rede virtual, por predefinição. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. A capacidade de criar rotas personalizadas é útil se, por exemplo, pretender encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede (NVA). Neste artigo, vai aprender a:

* Criar uma tabela de rotas
* Criar uma rota
* Criar uma rede virtual com várias sub-redes
* Associar uma tabela de rotas a uma sub-rede
* Criar uma NVA que encaminha o tráfego
* Implementar máquinas virtuais (VMs) em sub-redes diferentes
* Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes de criar uma tabela de rotas, crie um grupo de recursos com grupo [AZ criar](/cli/azure/group) para todos os recursos criados neste artigo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma tabela de rotas com a criação de tabela [de rotas da rede Az](/cli/azure/network/route-table#az-network-route-table-create). O exemplo seguinte cria uma tabela de rotas chamada *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Criar uma rota

Crie uma rota na tabela de rotas com a [rota da rota da rede Az criar](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

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

Antes de poder associar uma tabela de rotas a uma subnet, tem de criar uma rede virtual e uma sub-rede. Criar uma rede virtual com uma subnet com a [z network vnet criar](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Crie duas subredes adicionais com a [rede az vnet criação](/cli/azure/network/vnet/subnet).

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

Associe a tabela de rota *sição myRouteTablePublic* à subnet *pública* com a atualização de subnet da [rede az](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como encaminhamento, proteção com firewalls ou otimização de WAN.

Crie um NVA na subnet *DMZ* com [a z vm criar](/cli/azure/vm). Quando cria um VM, o Azure cria e atribui um endereço IP público ao VM, por padrão. O `--public-ip-address ""` parâmetro instrui o Azure a não criar e atribuir um endereço IP público ao VM, uma vez que o VM não precisa de ser ligado a partir da internet. Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Não continue até ao passo seguinte até que o Azure termine de criar o VM e desfaça a saída sobre o VM. 

Para que as interfaces de rede possam reencaminhar o tráfego de rede que lhes é enviado e que não seja destinado para o seu próprio endereço IP, o reencaminhamento de IP tem de estar ativado nas mesmas. Ativar o reencaminhamento IP para a interface de rede com [a atualização nic](/cli/azure/network/nic)da rede Az .

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dentro da VM, é necessário que o sistema operativo, ou uma aplicação em execução na mesma, consiga também reencaminhar o tráfego de rede. Ativar o encaminhamento ip dentro do sistema operativo do VM com um conjunto de [extensão az vm:](/cli/azure/vm/extension)

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

O comando pode demorar até um minuto a ser executado.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie dois VMs na rede virtual para que possa validar que o tráfego da subnet *Pública* é encaminhado para a subnet *privada* através da NVA em um passo posterior. 

Crie um VM na subnet *pública* com [az vm criar](/cli/azure/vm). O `--no-wait` parâmetro permite ao Azure executar o comando em segundo plano para que possa continuar até ao comando seguinte. Para simplificar este artigo, é utilizada uma palavra-passe. As chaves são normalmente utilizadas em implantações de produção. Se utilizar as teclas, também deve configurar o reencaminhamento do agente SSH. Para mais informações, consulte a documentação para o seu cliente SSH. Substitua `<replace-with-your-password>` no seguinte comando uma palavra-passe à sua escolha.

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

Crie um VM na subnet *privada.*

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

A criação da VM demora alguns minutos. Após a criação do VM, o Azure CLI mostra informações semelhantes ao seguinte exemplo: 

```output
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

Anote o **publicIpAddress**. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

Utilize o seguinte comando para criar uma sessão SSH com o *myVmPrivate* VM. Substitua * \<publicamente>do IpAddress* pelo endereço IP público do seu VM. No exemplo acima, o endereço IP é *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Quando solicitado uma senha, introduza a palavra-passe selecionada em [Criar máquinas virtuais](#create-virtual-machines).

Utilize o seguinte comando para instalar a rota de vestígios no *myVmPrivate* VM:

```bash
sudo apt-get install traceroute
```

Utilize o seguinte comando para testar o encaminhamento para o tráfego de rede para o *myVmPublic* VM do *myVmPrivate* VM.

```bash
traceroute myVmPublic
```

A resposta é semelhante ao seguinte exemplo:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Pode ver que o tráfego é encaminhado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. As rotas padrão do Azure, o tráfego de rotas diretamente entre as subredes. 

Utilize o seguinte comando para SSH para o *myVmPublic* VM do *myVmPrivate* VM:

```bash
ssh azureuser@myVmPublic
```

Utilize o seguinte comando para instalar a rota de vestígios no *myVmPublic* VM:

```bash
sudo apt-get install traceroute
```

Utilize o seguinte comando para testar o encaminhamento para o tráfego de rede para o *myVmPrivate* VM do *myVmPublic* VM.

```bash
traceroute myVmPrivate
```

A resposta é semelhante ao seguinte exemplo:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez com que o Azure encaminhe o tráfego através da NVA, em vez de diretamente para a sub-rede *Privada*.

Feche as sessões de SSH tanto para os *myVmPublic* como para os *myVmPrivate* VMs.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [grupo Az eliminar](/cli/azure/group) para remover o grupo de recursos e todos os recursos que contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma mesa de rota e associou-a a uma subnet. Criou uma NVA simples que encaminhou o tráfego de uma sub-rede pública para uma privada. Implementou uma variedade de NVAs pré-configuradas que realizam funções de rede, como proteção com firewall e otimização de WAN, a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa implementar muitos recursos do Azure dentro de uma rede virtual, não é possível implementar recursos de alguns serviços PaaS do Azure nas redes virtuais. Contudo, pode restringir o acesso aos recursos de alguns serviços PaaS do Azure ao tráfego apenas a partir de uma sub-rede de rede virtual. Para saber como, consulte [Restringir o acesso da rede aos recursos paas.](tutorial-restrict-network-access-to-resources-cli.md)
