---
title: Filtrar o tráfego de rede - CLI do Azure | Documentos da Microsoft
description: Neste artigo, irá aprender a filtrar o tráfego de rede a uma sub-rede, com um grupo de segurança de rede, com a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2c24634a42fd420eae204437418b82479869bbe5
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525549"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrar o tráfego de rede com um grupo de segurança de rede com a CLI do Azure

Pode filtrar o tráfego de rede de entrada e de saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Os grupos de segurança de rede contêm regras de segurança que filtram o tráfego de rede por endereço IP, porta e protocolo. As regras de segurança são aplicadas a recursos implementados numa sub-rede. Neste artigo, vai aprender a:

* Crie um grupo de segurança de rede e regras de segurança.
* Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
* Implementar máquinas virtuais (VMs) numa sub-rede
* Testar os filtros de tráfego

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a que está a executar a CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Os grupos de segurança de rede contêm regras de segurança. As regras de segurança especificam uma origem e um destino. As origens e destinos podem ser grupos de segurança de aplicações.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicações

Primeiro, crie um grupo de recursos para todos os recursos criados neste artigo com [criar grupo az](/cli/azure/group). O exemplo seguinte cria um grupo de recursos na localização *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie um grupo de segurança de aplicações com [az rede asg criar](/cli/azure/network/asg). Os grupo de segurança de aplicações permitem-lhe agrupar servidores com requisitos de filtragem de portas semelhante. O exemplo seguinte cria dois grupos de segurança de aplicações.

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

Crie um grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Criar regras de segurança

Crie uma regra de segurança com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra que permite o tráfego de entrada da Internet para o grupo de segurança de aplicações *myWebServers* através das portas 80 e 443:

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

O exemplo seguinte cria uma regra que permita o tráfego de entrada da Internet para o *myMgmtServers* grupo de segurança de aplicações através da porta 22:

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

Neste artigo, o SSH (porta 22) é exposto à Internet para o *myAsgMgmtServers* VM. Para ambientes de produção, em vez de expor a porta 22 para a internet, recomenda-se que se ligar aos recursos do Azure que pretende gerir com um [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ligação de rede.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual com o nome *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Adicionar uma sub-rede a uma rede virtual com [criar a sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). O exemplo seguinte adiciona uma sub-rede com o nome *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* à mesma:

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

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM que vai funcionar como servidor Web. O `--asgs myAsgWebServers` opção faz com que o Azure tornar a interface de rede, ele cria para a VM que um membro do *myAsgWebServers* grupo de segurança de aplicações.

O `--nsg ""` opção for especificada para impedir a criação de um grupo de segurança de rede predefinido para a interface de rede, o Azure cria quando este cria a VM do Azure. Para simplificar este artigo, é utilizada uma palavra-passe. As chaves são normalmente utilizadas em implementações de produção. Se usar chaves, também tem de configurar para os restantes passos de reencaminhamento de agentes SSH. Para obter mais informações, consulte a documentação do seu cliente SSH. Substitua `<replace-with-your-password>` no comando seguinte com uma palavra-passe à sua escolha.

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

A criação da VM demora alguns minutos. Depois da VM é criada, é devolvido o resultado semelhante ao seguinte exemplo: 

```azurecli 
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

Anote o **publicIpAddress**. Este endereço é utilizado para aceder à VM a partir da internet num passo posterior.  Crie uma VM que vai funcionar como servidor de gestão:

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

A criação da VM demora alguns minutos. Depois da VM é criada, tenha em atenção a **publicIpAddress** no resultado retornado. Este endereço é utilizado para aceder à VM no próximo passo. Não continue para o passo seguinte enquanto o Azure não concluir a criação da VM.

## <a name="test-traffic-filters"></a>Testar os filtros de tráfego

Utilize o comando seguinte para criar uma sessão SSH com o *myVmMgmt* VM. Substitua  *\<publicIpAddress >* com o endereço IP público da sua VM. No exemplo acima, é o endereço IP *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando lhe for pedido para uma palavra-passe, introduza a palavra-passe que introduziu no [criar VMs](#create-virtual-machines).

A ligação for bem-sucedida, porque a porta 22 é permitida a entrada da Internet para o *myAsgMgmtServers* grupo de segurança de aplicações que a interface de rede ligada para o *myVmMgmt* a VM está na.

Utilize o seguinte comando para encaminhar o SSH para o *myVmWeb* VM a partir do *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

A ligação é bem-sucedida, porque uma regra de segurança predefinida em cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP dentro de uma rede virtual. Não é possível encaminhar o SSH para o *myVmWeb* VM a partir da Internet porque a regra de segurança para o *myAsgWebServers* não permite a porta 22 de entrada da Internet.

Utilize os seguintes comandos para instalar o servidor web nginx no *myVmWeb* VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

O *myVmWeb* VM saída à Internet para obter o nginx porque uma regra de segurança predefinida permite que todo o tráfego de saída à Internet é permitida. Saída a *myVmWeb* sessão SSH, que mantém a na `username@myVmMgmt:~$` linha de comandos da *myVmMgmt* VM. Para obter o ecrã de boas-vindas do nginx dos *myVmWeb* VM, introduza o seguinte comando:

```bash
curl myVmWeb
```

Termine a sessão dos *myVmMgmt* VM. Para confirmar se consegue aceder a *myVmWeb* web server fora do Azure, introduza `curl <publicIpAddress>` do seu próprio computador. A ligação for bem-sucedida, porque a porta 80 é permitida a entrada da Internet para o *myAsgWebServers* grupo de segurança de aplicações que a interface de rede ligada para o *myVmWeb* a VM está na.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [eliminação do grupo de az](/cli/azure/group) para remover o grupo de recursos e todos os recursos nele contidos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um grupo de segurança de rede e associou-o a uma sub-rede de rede virtual. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](security-overview.md) e [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede).

O Azure encaminha o tráfego entre sub-redes por predefinição. Em alternativa, pode optar por encaminhar o tráfego entre sub-redes através de uma VM que funcione, por exemplo, como uma firewall. Para saber como, veja [criar uma tabela de rotas](tutorial-create-route-table-cli.md).
