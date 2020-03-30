---
title: Grupos IP em Firewall Azure
description: Os grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Firewall Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444488"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Grupos IP (pré-visualização) em Firewall Azure

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras da Firewall Azure das seguintes formas:

- Como endereço de origem nas regras do ADN
- Como fonte ou endereço de destino nas regras da rede
- Como endereço de origem nas regras de aplicação


Um Grupo IP pode ter um único endereço IP, vários endereços IP, ou uma ou mais gamas de endereços IP.

Os grupos IP podem ser reutilizados em DNAT, rede e aplicação de Firewall Azure. Os nomes de grupo devem ser únicos. Pode configurar um Grupo IP no portal Azure, Azure CLI ou REST API. Um modelo de amostra é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Formato de amostra

Os seguintes exemplos de formato de endereço IPv4 são válidos para utilização em grupos IP:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Intervalo de endereços: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um Grupo IP

Um Grupo IP pode ser criado utilizando o portal Azure, Azure CLI ou REST API. Para mais informações, consulte [Criar um Grupo IP (pré-visualização)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Navegue por grupos IP
1. Na barra de pesquisa do portal Azure, digite **grupos IP** e selecione-o. Pode ver a lista dos Grupos IP, ou pode selecionar **Adicionar** para criar um novo Grupo IP.
2. Selecione um Grupo IP para abrir a página de visão geral. Pode editar, adicionar ou eliminar endereços IP ou grupos IP.

   ![Visão geral dos grupos IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gerir um Grupo IP

Pode ver todos os endereços IP do Grupo IP e as regras ou recursos que lhe estão associados. Para eliminar um Grupo IP, primeiro deve dissociar o Grupo IP do recurso que o utiliza.

1. Para visualizar ou editar os endereços IP, selecione **endereços IP** em **Definições** no painel esquerdo.
2. Para adicionar um endereço IP único ou múltiplo, **selecione Adicionar Endereços IP**. Isto abre a página **Drag ou Browse** para um upload, ou pode introduzir o endereço manualmente.
3.  Selecionando as elipses **(...**) ao direito de editar ou excluir endereços IP. Para editar ou eliminar vários endereços IP, selecione as caixas e selecione **Editar** ou **Excluir** na parte superior.
4. Finalmente, pode exportar o ficheiro no formato de ficheiro CSV.

> [!NOTE]
> Se eliminar todos os endereços IP de um grupo IP enquanto ainda estiver a ser utilizado numa regra, essa regra é ignorada.


## <a name="use-an-ip-group"></a>Use um Grupo IP

Agora pode selecionar o **GRUPO IP** como **tipo fonte** ou tipo de **destino** para o endereço IP(es) quando criar regras de DNAT, aplicação ou rede Azure Firewall.

> [!NOTE]
> Os grupos IP não são apoiados na Política de Firewall. Atualmente, é apenas suportado com as regras tradicionais de firewall.

![Grupos IP em Firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Disponibilidade de região

Os grupos IP estão atualmente disponíveis nas seguintes regiões:

- E.U.A. Oeste
- E.U.A.Oeste 2
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Central
- E.U.A. Centro-Norte
- E.U.A. Centro-Oeste
- E.U.A. Centro-Sul
- Canadá Central
- Europa do Norte
- Europa ocidental
- França Central
- Sul do Reino Unido
- Leste da Austrália
- Austrália Central
- Austrália Sudeste

## <a name="related-azure-powershell-cmdlets"></a>Cmdlets de PowerShell Azure relacionados

Os seguintes cmdlets Azure PowerShell podem ser usados para criar e gerir grupos IP:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remover-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).