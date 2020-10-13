---
title: Introdução ao Azure Advisor
description: Começa com o Conselheiro Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85117867"
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como aceder ao Advisor através do portal Azure, obter recomendações e implementar recomendações.

> [!NOTE]
> O Azure Advisor funciona automaticamente em segundo plano para encontrar recursos recém-criados. Pode levar até 24 horas para fornecer recomendações sobre esses recursos.

## <a name="get-recommendations"></a>Obter recomendações

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, clique em **Advisor.**  Se não vir o Advisor no painel esquerdo, clique em **Todos os serviços**.  No painel de menus de serviço, em **Monitorização e Gestão,** clique em **Advisor**. É apresentado o painel de instrumentos Advisor.

   ![Access Azure Advisor usando o portal Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. O dashboard do Assistente apresentará um resumo das suas recomendações para todas as subscrições selecionadas.  Pode escolher as subscrições para as quais pretende que sejam apresentadas recomendações para utilizar a lista pendente de filtros de subscrições.

1. Para obter recomendações para uma categoria específica, clique num dos separadores: **Fiabilidade,** **Segurança,** **Desempenho**ou **Custo**. 

   ![Painel de instrumentos Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Obtenha detalhes de recomendação e implemente uma solução

Pode selecionar uma recomendação no Advisor para ver detalhes adicionais – como as ações de recomendação e recursos impactados – e implementar a solução para a recomendação.  

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Selecione uma categoria de recomendação para apresentar a lista de recomendações dentro dessa categoria, ou selecione o separador **'Todos'** para visualizar todas as suas recomendações.

1. Clique numa recomendação que pretende rever em detalhe.

1. Reveja as informações sobre a recomendação e os recursos a que a recomendação se aplica.

1. Clique na **Ação Recomendada** para implementar a recomendação.

## <a name="filter-recommendations"></a>Recomendações de filtragem

Pode filtrar recomendações para aprofundar o que é mais importante para si.  Pode filtrar por subscrição, tipo de recurso ou estado de recomendação.  

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Utilize as reduções no painel de instrumentos Do Advisor para filtrar por subscrição, tipo de recurso ou estado de recomendação.

    ![Critérios de filtro de pesquisa orientador](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Adiar ou rejeitar recomendações

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Navegue na recomendação que pretende adiar ou dispensar.

1. Clique na recomendação.

1. Clique **em Adiar**. 

1. Especifique um período de tempo de adiamento ou selecione **Nunca** rejeitar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups"></a>Excluir subscrições ou grupos de recursos

Pode ter grupos de recursos ou subscrições para as quais não pretende receber recomendações do Advisor – como recursos de "teste".  Pode configurar o Advisor apenas para gerar recomendações para subscrições específicas e grupos de recursos.

> [!NOTE]
> Para incluir ou excluir uma subscrição ou grupo de recursos do Advisor, tem de ser um Proprietário de assinatura.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção de incluí-la ou excluí-la é desativada na interface do utilizador.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **Configurar** na barra de ação.

1. Desmarque quaisquer subscrições ou grupos de recursos para os quais não pretende receber recomendações do Advisor.

    ![Exemplo de recursos de configuração de aconselhamento](./media/advisor-get-started/advisor-configure-resources.png)

1. Clique no botão **Aplicar.**

## <a name="configure-low-usage-vm-recommendation"></a>Configure recomendação VM de baixa utilização

Este procedimento configura a regra média de utilização do CPU para a recomendação da máquina virtual de baixo uso.

O Advisor monitoriza o uso da sua máquina virtual durante 7 dias e, em seguida, identifica máquinas virtuais de baixa utilização. As máquinas virtuais são consideradas de baixa utilização se a sua utilização do CPU for de 5% ou menos e a sua utilização em rede for inferior a 2% ou se a carga de trabalho atual puder ser acomodada por um tamanho de máquina virtual menor.

Se quiser ser mais agressivo na identificação de máquinas virtuais de baixo uso, pode ajustar a regra média de utilização do CPU por subscrição.  A regra de utilização da CPU pode ser definida em 5%, 10%, 15%, ou 20%.

> [!NOTE]
> Para ajustar a regra média de utilização do CPU para identificar máquinas virtuais de baixa utilização, tem de ser um *Proprietário*de assinaturas .  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção de incluí-la ou excluí-la será desativada na interface do utilizador. 

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Clique em **Configurar** na barra de ação.

1. Clique no separador **Regras.**

1. Selecione as subscrições para as quais pretende ajustar a regra média de utilização do CPU e, em seguida, clique em **Editar**.

1. Selecione o valor médio de utilização do CPU desejado e clique em **Aplicar**.

1. Clique nas **recomendações de Refresh** para atualizar as recomendações existentes para utilizar a nova regra média de utilização do CPU. 

   ![Exemplo de regras de recomendação de configuração de aconselhamento](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Recomendações de descarregamento

O Advisor permite-lhe fazer o download de um resumo das suas recomendações.  Pode descarregar as suas recomendações como ficheiro PDF ou ficheiro CSV.  O download das suas recomendações permite-lhe partilhar facilmente com os seus colegas ou realizar a sua própria análise em cima dos dados da recomendação.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

1. Clique **em Baixar como CSV** ou **Descarregue como PDF** na barra de ação.

A opção de descarregamento respeita quaisquer filtros que tenha aplicado no painel de instrumentos Advisor.  Se selecionar a opção de descarregamento ao visualizar uma categoria ou recomendação específica de recomendação, o resumo descarregado apenas inclui informações para essa categoria ou recomendação. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Conselheiro, consulte:

- [Introdução ao Assistente do Azure](advisor-overview.md)
- [Recomendações de fiabilidade do conselheiro](advisor-high-availability-recommendations.md)
- [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
- [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
- [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
- [Recomendações de Excelência Operacional Do Consultor](advisor-operational-excellence-recommendations.md)
