---
title: Grupos IP na política de firewall de Azure
description: Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras de política do Azure Firewall.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500857"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Grupos IP na política de Firewall Azure

Os grupos IP permitem-lhe agrupar e gerir endereços IP para a política do Azure Firewall das seguintes formas:

- Como tipo de fonte nas regras do DNAT
- Como fonte ou tipo de destino nas regras de rede
- Como tipo de fonte nas regras de aplicação


Um Grupo IP pode ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereço IP.

Os grupos IP podem ser reutilizados em Azure Firewall DNAT, rede e regras de aplicação para várias firewalls em regiões e subscrições em Azure. Os nomes de grupo devem ser únicos. Pode configurar um Grupo IP no portal Azure, Azure CLI ou REST API. Um modelo de amostra é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Sample format (Formato de exemplo)

Os seguintes exemplos de formato de endereço IPv4 são válidos para serem utilizados em grupos IP:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Intervalo de endereços: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um Grupo IP

Um Grupo IP pode ser criado usando o portal Azure, Azure CLI ou REST API. Para obter mais informações, consulte [Criar um Grupo IP](../firewall/create-ip-group.md).

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

Pode agora selecionar **o Grupo IP** como tipo de **Fonte** ou **tipo destino** para o endereço IP(es) quando criar uma política com regras de ADN, aplicação ou rede.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Grupos IP em Firewall":::

## <a name="ip-address-limits"></a>Limites de endereço IP

Pode ter um máximo de 100 Grupos IP por firewall com um máximo de 5000 endereços IP individuais ou prefixos IP por cada Grupo IP.

## <a name="related-azure-powershell-cmdlets"></a>Relacionados Cmdlets Azure PowerShell

Os seguintes cmdlets Azure PowerShell podem ser usados para criar e gerir grupos IP:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallPolicyNetworkrule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [New-AzFirewallPolicyNatrule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Proteja o seu WAN virtual usando o Azure Firewall Manager](secure-cloud-network.md)