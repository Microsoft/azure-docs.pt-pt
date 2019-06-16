---
title: Implementar e configurar a Firewall do Azure com a CLI do Azure
description: Neste artigo, saiba como implementar e configurar a Firewall do Azure com a CLI do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 06/11/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: b40ac789fbc331e779e85462724e5c8a8e9bce47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083356"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Implementar e configurar a Firewall do Azure com a CLI do Azure

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso a sites da web. Em alternativa, pode querer limitar os endereços IP e portas que podem ser acessadas de saída.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Neste artigo, vai criar uma VNet única simplificada com três sub-redes para facilitar a implementação. Para implementações de produção, uma [modelo hub- and -spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) é recomendado. A firewall está na sua própria VNet. Os servidores de carga de trabalho são nas VNets em modo de peering na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra de aplicação para permitir o acesso ao www.google.com
> * Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
> * Testar a firewall

Se preferir, pode concluir este procedimento com o [portal do Azure](tutorial-firewall-deploy-portal.md) ou [Azure PowerShell](deploy-ps.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-cli"></a>CLI do Azure

Se optar por instalar e utilizar a CLI localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute **az - versão**. Para obter informações sobre a instalação ou atualização, consulte [instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para a implementação.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Criar uma VNet

Esta rede virtual tem três sub-redes.

> [!NOTE]
> O tamanho mínimo da sub-rede AzureFirewallSubnet é /26.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie as máquinas virtuais de ligação e de carga de trabalho, e coloque-as nas sub-redes adequadas.
Quando lhe for pedido, escreva uma palavra-passe para a máquina virtual.

Crie a máquina de virtual de atalhos de Srv.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Crie um NIC para o trabalho de Srv com endereços IP do servidor DNS específicos e nenhum endereço IP público para testar com.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Agora, crie a máquina virtual de carga de trabalho.
Quando lhe for pedido, escreva uma palavra-passe para a máquina virtual.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Implementar a firewall

Implemente agora a firewall na rede virtual.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Criar uma tabela, com a propagação de rotas do BGP desativada

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Crie a rota.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Associar a tabela de rotas à sub-rede

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

A regra de aplicação permite acesso de saída www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

A regra de rede permite acesso de saída para dois endereços IP na porta 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste a firewall para confirmar que funciona conforme esperado.

1. Tenha em atenção o endereço IP privado para o **Srv trabalho** máquina virtual:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Ligar um ambiente de trabalho remoto **Srv atalhos** máquina virtual e o início de sessão. A partir daí, abra uma ligação de ambiente de trabalho remoto para o **Srv trabalho** endereço IP privado e de início de sessão.

3. No **SRV trabalho**, abra uma janela do PowerShell e execute os seguintes comandos:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Os dois comandos deverá devolver respostas, que mostra que as suas consultas DNS estão obtendo através da firewall.

1. Execute os seguintes comandos:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Os pedidos de www.google.com devem ter êxito, e os pedidos de www.microsoft.com devem falhar. Isto demonstra que as regras de firewall estão a funcionar conforme esperado.

Portanto, agora verificar que as regras de firewall estão a funcionar:

* Pode resolver nomes DNS com o servidor DNS externo configurado.
* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os seus recursos de firewall para o próximo tutorial ou se já não for necessário, elimine o **Test-FW-RG** grupo de recursos para eliminar todos os recursos relacionados com a firewall:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
