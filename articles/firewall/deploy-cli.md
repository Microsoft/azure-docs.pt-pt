---
title: Implementar e configurar firewall Azure usando Azure CLI
description: Neste artigo, aprende a implantar e configurar a Firewall Azure utilizando o Azure CLI.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 6993bd10caf2f7e489de8074e311f75710b83d82
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659442"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Implementar e configurar firewall Azure usando Azure CLI

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso a sites web. Ou, pode querer limitar os endereços IP de saída e portas que podem ser acedidos.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede. O FQDN também pode [incluir instâncias SQL](sql-fqdn-filtering.md).
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Para este artigo, cria-se um VNet único simplificado com três sub-redes para fácil implementação. Para implantações de produção, recomenda-se um [modelo de hub e spoke.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) A firewall está no seu próprio VNet. Os servidores de carga de trabalho estão em VNets espreitados na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste artigo, vai aprender a:

* Configurar um ambiente de rede de teste
* Implementar uma firewall
* Criar uma rota predefinida
* Configure uma regra de aplicação para permitir o acesso a www.google.com
* Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
* Testar a firewall

Se preferir, pode concluir este procedimento utilizando o [portal Azure](tutorial-firewall-deploy-portal.md) ou [a Azure PowerShell](deploy-ps.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para a implantação.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Criar uma VNet

Esta rede virtual tem três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
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
Quando solicitado, digite uma palavra-passe para a máquina virtual.

Crie a máquina virtual Srv-Jump.

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



Crie um NIC para Srv-Work com endereços IP específicos do servidor DNS e nenhum endereço IP público para testar.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Agora crie a máquina virtual de carga de trabalho.
Quando solicitado, digite uma palavra-passe para a máquina virtual.

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

Agora, insi(implantado a firewall na rede virtual.

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

Criar uma tabela, com propagação de rota BGP desativada

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Criar a rota.

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

A regra da aplicação permite o acesso de saída a www.google.com.

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

A regra da rede permite o acesso de saída a dois endereços IP na porta 53 (DNS).

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

Agora, teste a firewall para confirmar que funciona como esperado.

1. Note o endereço IP privado para a máquina virtual **Srv-Work:**

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Ligue um ambiente de trabalho remoto à máquina virtual **Srv-Jump** e inscreva-se. A partir daí, abra uma ligação de ambiente de trabalho remoto ao endereço IP privado **Srv-Work** e inscreva-se.

3. No **SRV-Work,** abra uma janela PowerShell e execute os seguintes comandos:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Ambos os comandos devem responder, mostrando que as suas consultas de DNS estão a passar pela firewall.

1. Execute os seguintes comandos:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Os `www.google.com` pedidos devem ter sucesso, e os `www.microsoft.com` pedidos devem falhar. Isto demonstra que as suas regras de firewall estão a funcionar como esperado.

Então agora verificaste que as regras da firewall estão a funcionar:

* Pode resolver nomes DNS com o servidor DNS externo configurado.
* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os seus recursos de firewall para o próximo tutorial, ou se já não for necessário, eliminar o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com firewall:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Próximos passos

* [Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)