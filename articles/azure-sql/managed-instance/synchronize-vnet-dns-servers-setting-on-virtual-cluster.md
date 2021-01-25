---
title: Sincronizar a configuração de servidores DNS de rede virtual no cluster virtual sql Managed Instance
description: Saiba como sincronizar a configuração de servidores DNS de rede virtual no cluster virtual SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747575"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Sincronizar a configuração de servidores DNS de rede virtual no cluster virtual sql Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica quando e como sincronizar a configuração de servidores DNS de rede virtual no cluster virtual SQL Managed Instance.

## <a name="when-to-synchronize-the-dns-setting"></a>Quando sincronizar a definição de DNS

Existem alguns cenários (por exemplo, correio da base de dados, servidores ligados a outras instâncias do SQL Server na cloud ou no ambiente híbrido) que exigem que os nomes dos anfitriões privados sejam resolvidos no SQL Managed Instance. Neste caso, precisa de configurar um DNS personalizado dentro do Azure. Consulte [configurar um DNS personalizado para Azure SQL Gestd Instance](custom-dns-configure.md) para mais detalhes.

Se esta alteração for implementada após a criação de [um cluster virtual](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) de Série Gerida, terá de sincronizar a definição de servidores DNS no cluster virtual com a configuração de rede virtual.

> [!IMPORTANT]
> Sincronizar a definição de servidores DNS irá afetar todas as Instâncias Geridas hospedadas no cluster virtual.

## <a name="how-to-synchronize-the-dns-setting"></a>Como sincronizar a definição de DNS

### <a name="azure-rbac-permissions-required"></a>Permissões Azure RBAC necessárias

A configuração do servidor DNS sincronizada pelo utilizador terá de ter uma das seguintes funções Azure:

- Função do Proprietário de Assinatura, ou
- Papel de contribuinte de instância gerida, ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Obtenha a rede virtual onde a definição de servidores DNS foi atualizada.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Utilize o comando PowerShell [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) para sincronizar a configuração dos servidores DNS para todos os clusters virtuais na sub-rede.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Obtenha a rede virtual onde a definição de servidores DNS foi atualizada.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Utilize o comando Azure CLI a [az recurso invocação de ação](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) para sincronizar a configuração dos servidores DNS para todos os clusters virtuais na sub-rede.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a configuração de um DNS [personalizado configurar um DNS personalizado para Azure SQL Managed Instance](custom-dns-configure.md).
- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
