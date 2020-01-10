---
title: Introdução ao Azure Advisor
description: Introdução ao Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443144"
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como acessar o Advisor por meio do portal do Azure, obter recomendações e implementar recomendações.

> [!NOTE]
> O Azure Advisor é executado automaticamente em segundo plano para encontrar recursos recém-criados. Pode levar até 24 horas para fornecer recomendações sobre esses recursos.

## <a name="get-recommendations"></a>Obter recomendações

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No painel esquerdo, clique em **supervisor**.  Se você não vir o Advisor no painel esquerdo, clique em **todos os serviços**.  No painel de menu serviço, em **monitoramento e gerenciamento**, clique em **supervisor**. O painel do Advisor é exibido.

   ![Acessar o Azure Advisor usando o portal do Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. O dashboard do Assistente apresentará um resumo das suas recomendações para todas as subscrições selecionadas.  Pode escolher as subscrições para as quais pretende que sejam apresentadas recomendações para utilizar a lista pendente de filtros de subscrições.

1. Para obter recomendações para uma categoria específica, clique em uma das guias: **alta disponibilidade**, **segurança**, **desempenho**ou **custo**. 

   ![Painel do Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Obter detalhes de recomendação e implementar uma solução

Você pode selecionar uma recomendação no Advisor para exibir detalhes adicionais – como as ações de recomendação e os recursos afetados – e implementar a solução para a recomendação.  

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Selecione uma categoria de recomendação para exibir a lista de recomendações dentro dessa categoria ou selecione a guia **tudo** para exibir todas as suas recomendações.

1. Clique em uma recomendação que você deseja examinar detalhadamente.

1. Examine as informações sobre a recomendação e os recursos aos quais a recomendação se aplica.

1. Clique na **ação recomendada** para implementar a recomendação.

## <a name="filter-recommendations"></a>Filtrar recomendações

Você pode filtrar as recomendações para fazer uma busca detalhada do que é mais importante para você.  Você pode filtrar por assinatura, tipo de recurso ou status de recomendação.  

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Use os menus suspensos no painel do Advisor para filtrar por assinatura, tipo de recurso ou status de recomendação.

    ![Pesquisa do Advisor-critérios de filtro](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Adiar ou ignorar recomendações

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Navegue até a recomendação que você deseja adiar ou ignorar.

1. Clique na recomendação.

1. Clique em **adiar**. 

1. Especifique um período de tempo adiado ou selecione **nunca** para ignorar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups"></a>Excluir assinaturas ou grupos de recursos

Você pode ter grupos de recursos ou assinaturas para os quais não deseja receber recomendações do Advisor – como recursos de ' teste '.  Você pode configurar o Advisor para gerar apenas recomendações para assinaturas e grupos de recursos específicos.

> [!NOTE]
> Para incluir ou excluir uma assinatura ou grupo de recursos do Advisor, você deve ser um proprietário de assinatura.  Se você não tiver as permissões necessárias para uma assinatura ou grupo de recursos, a opção para incluí-la ou excluí-la será desabilitada na interface do usuário.

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **Configurar** na barra de ação.

1. Desmarque quaisquer assinaturas ou grupos de recursos para os quais você não deseja receber recomendações do Advisor.

    ![Exemplo de configuração de recursos do Advisor](./media/advisor-get-started/advisor-configure-resources.png)

1. Clique no botão **aplicar** .

## <a name="configure-low-usage-vm-recommendation"></a>Configurar a recomendação de VM de uso baixo

Este procedimento configura a regra de utilização média da CPU para a recomendação de máquina virtual de baixo uso.

O Advisor monitora o uso de máquinas virtuais por 7 dias e identifica máquinas virtuais de baixa utilização. As máquinas virtuais são consideradas de baixa utilização se a utilização da CPU for de 5% ou menos e sua utilização de rede for menor que 2% ou se a carga de trabalho atual puder ser acomodada por um tamanho de máquina virtual menor.

Se você quiser ser mais agressivo na identificação de máquinas virtuais de baixo uso, poderá ajustar a regra de utilização média da CPU por assinatura.  A regra de utilização da CPU pode ser definida como 5%, 10%, 15% ou 20%.

> [!NOTE]
> Para ajustar a regra de utilização média da CPU para identificar máquinas virtuais de baixo uso, você deve ser um *proprietário*de assinatura.  Se você não tiver as permissões necessárias para uma assinatura ou grupo de recursos, a opção para incluí-la ou excluí-la será desabilitada na interface do usuário. 

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **Configurar** na barra de ação.

1. Clique na guia **regras** .

1. Selecione as assinaturas para as quais você deseja ajustar a regra de utilização média da CPU e clique em **Editar**.

1. Selecione o valor de utilização média da CPU desejado e clique em **aplicar**.

1. Clique em **Atualizar recomendações** para atualizar suas recomendações existentes para usar a nova regra média de utilização da CPU. 

   ![Exemplo de regras de recomendação para configurar o Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Recomendações de download

O Advisor permite que você baixe um resumo das suas recomendações.  Você pode baixar suas recomendações como um arquivo PDF ou um arquivo CSV.  Baixar suas recomendações permite que você compartilhe facilmente com seus colegas ou realize sua própria análise sobre os dados de recomendação.

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **baixar como CSV** ou **baixar como PDF** na barra de ação.

A opção de download respeita todos os filtros aplicados ao painel do Advisor.  Se você selecionar a opção de download ao exibir uma determinada categoria ou recomendação de recomendação, o resumo baixado só incluirá informações para essa categoria ou recomendação. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Advisor, consulte:

- [Introdução ao Azure Advisor](advisor-overview.md)
- [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
- [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
- [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
- [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
