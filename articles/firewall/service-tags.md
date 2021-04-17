---
title: Visão geral das tags de serviço Azure Firewall
description: As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529547"
---
# <a name="azure-firewall-service-tags"></a>Tags de serviço Azure Firewall

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

As tags de serviço Azure Firewall podem ser usadas no campo de destino das regras de rede. Pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Etiquetas de serviço suportadas

Consulte [as etiquetas](../virtual-network/service-tags-overview.md#available-service-tags) de serviço de rede virtual para obter uma lista de tags de serviço que estão disponíveis para utilização nas regras da rede de firewall Azure.

## <a name="configuration"></a>Configuração

O Azure Firewall suporta a configuração de Tags de Serviço através de PowerShell, Azure CLI ou do portal Azure.

### <a name="configure-via-azure-powershell"></a>Configurar através do Azure PowerShell

Neste exemplo, temos primeiro de obter contexto para o nosso exemplo de Azure Firewall anteriormente criado.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Em seguida, temos de criar uma nova Regra.  Para a Fonte ou Destino, pode especificar o valor de texto da Etiqueta de Serviço que pretende alavancar, como mencionado anteriormente neste artigo.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Em seguida, temos de atualizar a variável que contém a nossa definição de Azure Firewall com as novas Regras de Rede que criámos.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Por último, temos de comprometer as alterações da Regra de Rede à instância de Azure Firewall em execução.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as regras do Azure Firewall, consulte [a lógica de processamento de regras do Azure Firewall](rule-processing.md).
