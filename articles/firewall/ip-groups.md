---
title: Grupos IP em Firewall Azure
description: Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 6910fda855402d2f99994da1e835560be84ca2dd
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703382"
---
# <a name="ip-groups-in-azure-firewall"></a>Grupos IP em Firewall Azure

Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall das seguintes formas:

- Como endereço de origem nas regras do DNAT
- Como endereço de origem ou destino nas regras da rede
- Como endereço de origem nas regras de aplicação


Um Grupo IP pode ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereço IP.

Os grupos IP podem ser reutilizados em Azure Firewall DNAT, rede e regras de aplicação para várias firewalls em regiões e subscrições em Azure. Os nomes de grupo devem ser únicos. Pode configurar um Grupo IP no portal Azure, Azure CLI ou REST API. Um modelo de amostra é fornecido para ajudá-lo a começar.

> [!NOTE]
> Os grupos IP não estão atualmente disponíveis em ambientes de nuvem nacional de Azure.

## <a name="sample-format"></a>Sample format (Formato de exemplo)

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

Pode ter um máximo de 100 Grupos IP por firewall com um máximo de 5000 endereços IP individuais ou prefixos IP por cada Grupo IP.

## <a name="related-azure-powershell-cmdlets"></a>Relacionados Cmdlets Azure PowerShell

Os seguintes cmdlets Azure PowerShell podem ser usados para criar e gerir grupos IP:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Nova-AzFirewallNetworkrule](/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nova-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatrule](/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Próximos passos

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).