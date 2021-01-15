---
title: Tráfego de rede de filtros - Azure CLI / Microsoft Docs
description: Neste artigo, aprende-se a filtrar o tráfego de rede para uma sub-rede, com um grupo de segurança de rede, utilizando o CLI Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2cabecfb2a70e6d325129335c066bb35aeb02ba
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221906"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrar o tráfego da rede com um grupo de segurança de rede usando o Azure CLI

Pode filtrar o tráfego de rede de entrada e de saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Os grupos de segurança de rede contêm regras de segurança que filtram o tráfego de rede por endereço IP, porta e protocolo. As regras de segurança são aplicadas a recursos implementados numa sub-rede. Neste artigo, vai aprender a:

* Crie um grupo de segurança de rede e regras de segurança.
* Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
* Implementar máquinas virtuais (VMs) numa sub-rede
* Testar os filtros de tráfego

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Os grupos de segurança de rede contêm regras de segurança. As regras de segurança especificam uma origem e um destino. As origens e destinos podem ser grupos de segurança de aplicações.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicações

Primeiro criar um grupo de recursos para todos os recursos criados neste artigo com [a criação do grupo AZ.](/cli/azure/group) O exemplo seguinte cria um grupo de recursos na localização *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Criar um grupo de segurança de aplicações com [a rede az asg criar](/cli/azure/network/asg). Os grupo de segurança de aplicações permitem-lhe agrupar servidores com requisitos de filtragem de portas semelhante. O exemplo seguinte cria dois grupos de segurança de aplicações.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [a criação de nsg de rede Az](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Criar regras de segurança

Criar uma regra de segurança com [a regra az rede nsg criar](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra que permite o tráfego de entrada da Internet para o grupo de segurança de aplicações *myWebServers* através das portas 80 e 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

O exemplo a seguir cria uma regra que permite a entrada de tráfego da Internet para o grupo de segurança de aplicações *myMgmtServers* sobre a porta 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

Neste artigo, o SSH (porta 22) está exposto à internet para o *myAsgMgmtServers* VM. Para ambientes de produção, em vez de expor a porta 22 à internet, recomenda-se que se conecte aos recursos da Azure que pretende gerir utilizando uma ligação [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou rede [privada.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual com o nome *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Adicione uma sub-rede a uma rede virtual com a criação da [sub-rede de rede Az.](/cli/azure/network/vnet/subnet) O exemplo seguinte adiciona uma sub-rede com o nome *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* à mesma:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual, para que possa confirmar a filtragem de tráfego num passo posterior. 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM que vai funcionar como servidor Web. A `--asgs myAsgWebServers` opção faz com que o Azure faça da interface de rede que cria para o VM um membro do grupo de segurança da aplicação *myAsgWebServers.*

A `--nsg ""` opção é especificada para impedir que o Azure crie um grupo de segurança de rede predefinido para a interface de rede que o Azure cria quando cria o VM. Para simplificar este artigo, é utilizada uma palavra-passe. As chaves são normalmente utilizadas em implantações de produção. Se utilizar as teclas, também deve configurar o encaminhamento do agente SSH para os restantes passos. Para mais informações, consulte a documentação para o seu cliente SSH. Substitua `<replace-with-your-password>` no seguinte comando por uma palavra-passe à sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A criação da VM demora alguns minutos. Após a criação do VM, a saída semelhante ao seguinte exemplo é devolvida: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.  Crie uma VM que vai funcionar como servidor de gestão:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A criação da VM demora alguns minutos. Após a criação do VM, note o **publicIpAddress** na saída devolvida. Este endereço é utilizado para aceder ao VM no passo seguinte. Não continue para o passo seguinte enquanto o Azure não concluir a criação da VM.

## <a name="test-traffic-filters"></a>Testar os filtros de tráfego

Utilize o comando que se segue para criar uma sessão de SSH com o *myVmMgmt* VM. *\<publicIpAddress>* Substitua-o pelo endereço IP público do seu VM. No exemplo acima, o endereço IP é *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando solicitado para uma palavra-passe, introduza a palavra-passe que introduziu no [Create VMs](#create-virtual-machines).

A ligação é bem sucedida, uma vez que a porta 22 é permitida a entrada da Internet para o *myAsgMgmtServers* grupo de segurança de aplicação em que se encontra a interface de rede ligada ao *myVmMgmt* VM.

Utilize o seguinte comando para SSH para o *myVmWeb* VM do *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

A ligação é bem-sucedida, porque uma regra de segurança predefinida em cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP dentro de uma rede virtual. Não é possível sSH para o *myVmWeb* VM da Internet porque a regra de segurança para os *myAsgWebServers* não permite a entrada da porta 22 a partir da Internet.

Utilize os seguintes comandos para instalar o servidor web nginx no *myVmWeb* VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

O *myVmWeb* VM é permitido sair para a Internet para recuperar nginx porque uma regra de segurança padrão permite todo o tráfego de saída para a Internet. Saia da sessão *myVmWeb* SSH, o que o deixa à `username@myVmMgmt:~$` solicitação do *myVmMgmt* VM. Para recuperar o ecrã de boas-vindas nginx do *myVmWeb* VM, insira o seguinte comando:

```bash
curl myVmWeb
```

Logout do *myVmMgmt* VM. Para confirmar que pode aceder ao servidor web *myVmWeb* a partir de fora do Azure, insira `curl <publicIpAddress>` a partir do seu próprio computador. A ligação tem sucesso, uma vez que a porta 80 é permitida a entrada da Internet para o grupo de segurança da aplicação *myAsgWebServers* em que se encontra a interface de rede anexada ao *myVmWeb* VM.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [o grupo AZ para remover](/cli/azure/group) o grupo de recursos e todos os recursos que contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um grupo de segurança de rede e associou-o a uma sub-rede de rede virtual. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](./network-security-groups-overview.md) e [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede).

O Azure encaminha o tráfego entre sub-redes por predefinição. Em alternativa, pode optar por encaminhar o tráfego entre sub-redes através de uma VM que funcione, por exemplo, como uma firewall. Para saber como, consulte [Criar uma tabela de rotas.](tutorial-create-route-table-cli.md)