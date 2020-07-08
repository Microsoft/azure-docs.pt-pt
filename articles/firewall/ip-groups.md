---
title: Grupos IP em Firewall Azure
description: Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: 7bfa1ae5bd0f2ffe92fb37494f9fe589e1b2040e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565549"
---
# <a name="ip-groups-in-azure-firewall"></a>Grupos IP em Firewall Azure

Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall das seguintes formas:

- Como endereço de origem nas regras do DNAT
- Como endereço de origem ou destino nas regras da rede
- Como endereço de origem nas regras de aplicação


Um Grupo IP pode ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereço IP.

Os grupos IP podem ser reutilizados em Azure Firewall DNAT, rede e regras de aplicação para várias firewalls em regiões e subscrições em Azure. Os nomes de grupo devem ser únicos. Pode configurar um Grupo IP no portal Azure, Azure CLI ou REST API. Um modelo de amostra é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Formato de amostra

Os seguintes exemplos de formato de endereço IPv4 são válidos para serem utilizados em grupos IP:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Intervalo de endereços: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um Grupo IP

Um Grupo IP pode ser criado usando o portal Azure, Azure CLI ou REST API. Para obter mais informações, consulte [Criar um Grupo IP](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procurar grupos IP
1. Na barra de pesquisa do portal Azure, escreva **grupos IP** e selecione-o. Pode ver a lista dos Grupos IP ou pode selecionar **Adicionar** para criar um novo Grupo IP.
2. Selecione um Grupo IP para abrir a página geral. Pode editar, adicionar ou eliminar endereços IP ou grupos IP.

   ![Visão geral dos grupos IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gerir um Grupo IP

Pode ver todos os endereços IP no Grupo IP e as regras ou recursos que lhe estão associados. Para eliminar um Grupo IP, tem primeiro de dissociar o Grupo IP do recurso que o utiliza.

1. Para visualizar ou editar os endereços IP, selecione **endereços IP** em **Definições** no painel esquerdo.
2. Para adicionar um único ou vários endereços IP(es), **selecione Adicionar endereços IP**. Isto abre a página **Drag ou Browse** para um upload, ou pode introduzir o endereço manualmente.
3.    Selecionando as elipses **(...)** para o direito de editar ou eliminar endereços IP. Para editar ou eliminar vários endereços IP, selecione as caixas e selecione **Editar** ou **Eliminar** na parte superior.
4. Finalmente, pode exportar o ficheiro no formato de ficheiro CSV.

> [!NOTE]
> Se eliminar todos os endereços IP de um Grupo IP enquanto este ainda estiver a ser utilizado numa regra, essa regra é ignorada.


## <a name="use-an-ip-group"></a>Utilize um Grupo IP

Pode agora selecionar **o Grupo IP** como tipo de **fonte** ou **tipo destino** para o endereço IP(es) quando criar as regras de ADN, aplicação ou rede do Azure Firewall.

![Grupos IP em Firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Disponibilidade de região

Os grupos IP estão disponíveis em todas as regiões de nuvem pública.

## <a name="ip-address-limits"></a>Limites de endereço IP

Para 50 Grupos IP ou menos, você pode ter um máximo de 5000 endereços IP individuais cada instância de firewall. Para 51 a 100 Grupos IP, você pode ter 500 endereço IP individuais cada instância de firewall.

### <a name="examples"></a>Exemplos

#### <a name="example-1-supported"></a>Exemplo 1: suportado

|Grupos IP  |Endereços IP  |Anotações  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regra1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Regra1|
|IPGroup3     |1|1.2.3.4|Regra1|
|     |**Total 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Exemplo 2: suportado

|Grupos IP  |Endereços IP  |Anotações  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regra1|
|IPGroup2     |4096|11.0.0.0/20|Regra1|
|     |**Total 8192**|         |         |

#### <a name="example-3-not-supported"></a>Exemplo 3: não suportado

|Grupos IP  |Endereços IP  |Anotações  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Regra1|
|     |**Total 8192**|||

#### <a name="example-4-supported"></a>Exemplo 4: suportado

|Grupos IP  |Endereços IP  |Anotações  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regra1|
|IPGroup2     |4096|11.0.0.0/20|Regra2|
|     |**Total 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Relacionados Cmdlets Azure PowerShell

Os seguintes cmdlets Azure PowerShell podem ser usados para criar e gerir grupos IP:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Nova-AzFirewallNetworkrule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nova-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatrule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Próximos passos

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).