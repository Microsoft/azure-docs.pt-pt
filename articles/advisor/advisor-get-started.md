---
title: Introdução ao Azure Advisor
description: Começa com o Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259633"
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como aceder ao Advisor através do portal Azure, obter recomendações e implementar recomendações.

> [!NOTE]
> O Azure Advisor corre automaticamente em segundo plano para encontrar recursos recém-criados. Pode levar até 24 horas para fornecer recomendações sobre esses recursos.

## <a name="get-recommendations"></a>Obter recomendações

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, clique em **Advisor**.  Se não vir o Advisor no painel esquerdo, clique em **Todos os serviços.**  No painel de menude serviço, em **Monitorização e Gestão,** clique em **Advisor**. O painel advisor é apresentado.

   ![Access Azure Advisor utilizando o portal Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. O dashboard do Assistente apresentará um resumo das suas recomendações para todas as subscrições selecionadas.  Pode escolher as subscrições para as quais pretende que sejam apresentadas recomendações para utilizar a lista pendente de filtros de subscrições.

1. Para obter recomendações para uma categoria específica, clique num dos separadores: **Alta Disponibilidade,** **Segurança,** **Desempenho**ou **Custo**. 

   ![Painel de instrumentos Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Obtenha detalhes de recomendação e implemente uma solução

Pode selecionar uma recomendação no Advisor para visualizar detalhes adicionais – como as ações de recomendação e recursos impactados – e implementar a solução para a recomendação.  

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Selecione uma categoria de recomendação para apresentar a lista de recomendações dentro dessa categoria ou selecione o separador **All** para ver todas as suas recomendações.

1. Clique numa recomendação que pretende rever em detalhe.

1. Reveja as informações sobre a recomendação e os recursos a que a recomendação se aplica.

1. Clique na **Ação Recomendada** para implementar a recomendação.

## <a name="filter-recommendations"></a>Recomendações de filtro

Pode filtrar recomendações para aprofundar o que é mais importante para si.  Pode filtrar por subscrição, tipo de recurso ou estado de recomendação.  

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Utilize as desistências no painel advisor para filtrar por subscrição, tipo de recurso ou estado de recomendação.

    ![Critérios de filtro de pesquisa de aconselhamento](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Adiar ou rejeitar recomendações

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Navegue para a recomendação que pretende adiar ou rejeitar.

1. Clique na recomendação.

1. Clique **em Adiar**. 

1. Especifique um período de tempo de adiamento ou selecione **Nunca** rejeitar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups"></a>Excluir subscrições ou grupos de recursos

Pode ter grupos de recursos ou subscrições para as quais não deseja receber recomendações do Advisor – como recursos de "teste".  Pode configurar o Advisor para gerar apenas recomendações para subscrições específicas e grupos de recursos.

> [!NOTE]
> Para incluir ou excluir um grupo de subscrição ou recursos do Advisor, deve ser um Proprietário de subscrição.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção de incluí-la ou excluí-la é desativada na interface do utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Clique em **Configurar** na barra de ação.

1. Desmarque quaisquer subscrições ou grupos de recursos para os quais não queira receber recomendações do Advisor.

    ![Exemplo de recursos de configuração de conselheiros](./media/advisor-get-started/advisor-configure-resources.png)

1. Clique no botão **Aplicar.**

## <a name="configure-low-usage-vm-recommendation"></a>Configurar recomendação vm de baixa utilização

Este procedimento configura a regra média de utilização do CPU para a recomendação de máquina virtual de baixo uso.

O Advisor monitoriza o uso da sua máquina virtual durante 7 dias e, em seguida, identifica máquinas virtuais de baixa utilização. As máquinas virtuais são consideradas de baixa utilização se a sua utilização de CPU for de 5% ou menos e a sua utilização da rede for inferior a 2% ou se a carga de trabalho atual puder ser acomodada por um tamanho de máquina virtual menor.

Se quiser ser mais agressivo na identificação de máquinas virtuais de baixo uso, pode ajustar a regra média de utilização do CPU numa base de subscrição.  A regra de utilização do CPU pode ser fixada em 5%, 10%, 15%, ou 20%.

> [!NOTE]
> Para ajustar a regra média de utilização do CPU para identificar máquinas virtuais de baixa utilização, deve ser um *Proprietário*de subscrição .  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção de incluí-la ou excluí-la será desativada na interface do utilizador. 

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Clique em **Configurar** na barra de ação.

1. Clique no separador **Regras.**

1. Selecione as subscrições que deseja ajustar a regra média de utilização do CPU e, em seguida, clique em **Editar**.

1. Selecione o valor médio de utilização do CPU desejado e clique **em Aplicar**.

1. Clique nas **recomendações da Refresh** para atualizar as suas recomendações existentes para utilizar a nova regra média de utilização do CPU. 

   ![Exemplo de regras de recomendação de configuração de conselheiros](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Descarregue recomendações

O Advisor permite-lhe fazer o download de um resumo das suas recomendações.  Pode descarregar as suas recomendações como ficheiro PDF ou ficheiro CSV.  O download das suas recomendações permite-lhe partilhar facilmente com os seus colegas ou realizar a sua própria análise para além dos dados de recomendação.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

1. Clique em **Baixar como CSV** ou **baixar como PDF** na barra de ação.

A opção de descarregamento respeita quaisquer filtros que tenha aplicado ao painel advisor.  Se selecionar a opção de descarregamento ao visualizar uma categoria ou recomendação específica, o resumo descarregado apenas inclui informações para essa categoria ou recomendação. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Advisor, consulte:

- [Introdução ao Consultor Azure](advisor-overview.md)
- [Recomendações de Alta Disponibilidade Do Conselheiro](advisor-high-availability-recommendations.md)
- [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
- [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
- [Recomendações de Custos Consultivos](advisor-performance-recommendations.md)
