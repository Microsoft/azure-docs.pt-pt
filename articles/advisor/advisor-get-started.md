---
title: Introdução ao Assistente do Azure | Documentos da Microsoft
description: Introdução ao Assistente do Azure.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: a7e82fffdd9c865de6040c05ec28bc8bd2dced61
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111400"
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como acessar o Advisor através do portal do Azure, obtenha recomendações e implemente recomendações.

> [!NOTE]
> O Assistente do Azure é executado automaticamente em segundo plano para recursos de localizar recentemente criado. Pode demorar até 24 horas para fornecer recomendações sobre esses recursos.

## <a name="get-recommendations"></a>Obtenha recomendações

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, clique em **Advisor**.  Se não vir o Advisor no painel esquerdo, clique em **todos os serviços**.  No painel de menu de serviço, em **monitorização e gestão**, clique em **Advisor**. O dashboard do assistente é exibido.

   ![Assistente do acesso do Azure no portal do Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. O dashboard do assistente será apresentado um resumo das suas recomendações para todas as subscrições selecionadas.  Pode escolher a lista pendente de filtro de subscrições que pretende que as recomendações a apresentar para utilizar a subscrição.

1. Para obter recomendações para uma categoria específica, clique em um dos separadores: **Elevada disponibilidade**, **Security**, **desempenho**, ou **custo**. 

   ![Dashboard do Assistente do Azure](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Obter os detalhes de recomendação e implementar uma solução

Pode selecionar uma recomendação no Advisor para ver detalhes adicionais – por exemplo, as ações de recomendação e obter recursos afetados – e para implementar a solução para a recomendação.  

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Selecione uma categoria de recomendação para apresentar a lista de recomendações dentro dessa categoria, ou selecione o **todos os** separador para ver todas as suas recomendações.

1. Clique numa recomendação que deseja examinar detalhadamente.

1. Reveja as informações sobre a recomendação e os recursos que a recomendação aplica-se a.

1. Clique nas **ação recomendada** para implementar a recomendação.

## <a name="filter-recommendations"></a>Recomendações de filtro

Pode filtrar recomendações para descer até o que é mais importante para si.  Pode filtrar por subscrição, tipo de recurso ou o estado da recomendação.  

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Utilize as listas pendentes no dashboard do Advisor para filtrar por subscrição, o tipo de recurso ou o estado da recomendação.

    ![Critérios de filtro de pesquisa do Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Adiar ou ignorar recomendações

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Navegue para a recomendação de que pretende adiar ou ignorar.

1. Clique a recomendação.

1. Clique em **adiar**. 

1. Especifique um ' Adiar ' período de tempo ou selecione **Never** para dispensar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups"></a>Excluir subscrições ou grupos de recursos

Poderá ter grupos de recursos ou subscrições para que não pretende receber recomendações do assistente, como recursos de "teste".  Pode configurar o Assistente para gerar apenas recomendações para as subscrições específicas e grupos de recursos.

> [!NOTE]
> Para incluir ou excluir uma subscrição ou grupo de recursos do assistente, tem de ser uma proprietário da subscrição.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção para incluir ou exclui-lo está desativada na interface do usuário.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **configurar** na barra de ação.

1. Desmarcar quaisquer subscrições ou grupos de recursos que não pretende receber recomendações do Assistente para.

    ![O Assistente configurar o exemplo de recursos](./media/advisor-get-started/advisor-configure-resources.png)

1. Clique nas **aplicar** botão.

## <a name="configure-low-usage-vm-recommendation"></a>Configurar a utilização de baixa recomendação de VM

Este procedimento configura a regra de utilização de CPU média para a recomendação de máquina virtual de baixa utilização.

O assistente monitoriza a utilização da máquina virtual durante 14 dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Máquinas virtuais cuja utilização média da CPU é 5 por cento ou menos e utilização de rede é de 7 MB ou menos para quatro ou mais dias são considerados máquinas de virtuais de baixa utilização.

Se gostaria de ser mais agressivo na identificação de máquinas virtuais de baixa utilização, pode ajustar a regra de utilização de CPU média numa base por subscrição.  A regra de utilização média da CPU pode ser definida como 5%, 10%, % de 15 ou 20%.

> [!NOTE]
> Para ajustar a regra de utilização de CPU média para identificar máquinas de virtuais de baixa utilização, tem de ser uma subscrição *proprietário*.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção para incluir ou exclui-lo será desativada na interface do usuário. 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **configurar** na barra de ação.

1. Clique nas **regras** separador.

1. Selecione as subscrições que pretende ajustar a regra de utilização de CPU média para e, em seguida, clique em **editar**.

1. Selecione o valor de utilização de CPU média desejado e clique em **aplicar**.

1. Clique em **atualizar recomendações** para atualizar as suas recomendações existentes para utilizar a nova regra de utilização média da CPU. 

   ![O Assistente configurar o exemplo de regras de recomendação](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Transferir recomendações

O assistente permite-lhe transferir um resumo das suas recomendações.  Pode transferir as suas recomendações como um ficheiro PDF ou um ficheiro CSV.  Transferir as suas recomendações permite-lhe facilmente compartilhar com seus colegas ou realizar sua própria análise sobre os dados de recomendação.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **transferir como CSV** ou **transferir como PDF** na barra de ação.

A opção de download respeita quaisquer filtros que tiver aplicado para o dashboard do assistente.  Se selecionar a opção de transferência enquanto vê uma categoria de recomendação específica ou a recomendação, o resumo transferido só inclui informações para essa categoria ou a recomendação. 

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Advisor, consulte:

- [Introdução ao Assistente do Azure](advisor-overview.md)
- [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
- [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
- [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
- [Recomendações de custos do Assistente](advisor-performance-recommendations.md)
