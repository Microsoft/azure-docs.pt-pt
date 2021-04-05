---
title: Planear e gerir os custos da Base de Dados Azure SQL
description: Saiba como planear e gerir os custos para a Azure SQL Database utilizando a análise de custos no portal Azure.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734635"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planear e gerir os custos da Base de Dados Azure SQL

Este artigo descreve como planeia e gere os custos para a Base de Dados Azure SQL. Em primeiro lugar, usa a calculadora de preços Azure para adicionar recursos Azure, e rever os custos estimados. Depois de ter começado a utilizar os recursos da Base de Dados Azure SQL, utilize funcionalidades de Gestão de Custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. Os custos da Base de Dados Azure SQL são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir os custos para a Base de Dados Azure SQL, você está cobrado para todos os serviços e recursos Azure usados na sua subscrição Azure, incluindo quaisquer serviços de terceiros.


## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. 

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Considerações iniciais de custos da Base de Dados SQL

Ao trabalhar com a Base de Dados Azure SQL, existem várias funcionalidades de poupança de custos a considerar:


### <a name="vcore-or-dtu-purchasing-models"></a>vCore ou modelos de compra DTU 

A Azure SQL Database suporta dois modelos de compra: vCore e DTU. A forma como é cobrado varia entre os modelos de compra, pelo que é importante compreender o modelo que funciona melhor para a sua carga de trabalho ao planear e considerar os custos. Para obter informações sobre os modelos de compra vCore e DTU, consulte [Escolha entre os modelos de compra vCore e DTU.](purchasing-models.md)


### <a name="provisioned-or-serverless"></a>Provisionado ou sem servidor

No modelo de compra vCore, a Base de Dados Azure SQL também suporta dois tipos de níveis de computação: produção a provisionada e sem servidor. A forma como é cobrado por cada nível de cálculo varia, por isso é importante entender o que funciona melhor para a sua carga de trabalho ao planear e considerar custos. Para mais detalhes, consulte a [visão geral do modelo vCore - níveis de cálculo](service-tiers-vcore.md#compute-tiers).

No nível de computação previsto do modelo de compra baseado em vCore, pode trocar as licenças existentes por tarifas com desconto. Para mais informações, consulte [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Conjuntos elásticos

Para ambientes com múltiplas bases de dados que têm exigências de utilização variadas e imprevisíveis, piscinas elásticas podem proporcionar poupanças de custos em comparação com o fornecimento da mesma quantidade de bases de dados individuais. Para mais detalhes, consulte [piscinas elásticas.](elastic-pool-overview.md)

## <a name="estimate-azure-sql-database-costs"></a>Estimativa dos custos da base de dados Azure SQL

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos para diferentes configurações da Base de Dados Azure SQL. As informações e os preços na imagem a seguir são apenas para fins:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Exemplo da calculadora de preços da base de dados Azure SQL":::

Também pode estimar como diferentes opções de Política de Retenção afetam o custo. As informações e os preços na imagem a seguir são apenas para fins:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Exemplo de calculadora de preços da base de dados Azure SQL para armazenamento":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Compreenda o modelo completo de faturação para a Base de Dados Azure SQL

A Base de Dados Azure SQL funciona em infraestrutura Azure que acumula custos juntamente com a Base de Dados Azure SQL quando implementa o novo recurso. É importante entender que infraestruturas adicionais podem acumular custos. Tens de gerir esse custo quando fazes alterações aos recursos implantados. 


A base de dados Azure SQL (com exceção do servidor) é faturada numa taxa previsível e horária. Se a base de dados SQL estiver ativa por menos de uma hora, é faturada por cada hora que a base de dados existe utilizando o nível de serviço mais elevado selecionado, armazenamento a provisionado e IO que se aplicava durante essa hora, independentemente da utilização ou se a base de dados estava ativa há menos de uma hora.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Utilização de Crédito Monetário com Base de Dados Azure SQL

Você pode pagar os encargos da Base de Dados Azure SQL com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

Ao passar pelo processo de criação de uma Base de Dados Azure SQL, pode ver os custos estimados durante a configuração do nível de computação. 

Para aceder a este ecrã, selecione **a base de dados Configure** no separador **Básicos** da página **'Criar SQL Database'.** As informações e os preços na imagem a seguir são apenas para fins:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Exemplo mostrando estimativa de custos no portal Azure":::



Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando se atinge o seu limite de crédito, os recursos que implementou são desativados durante o resto desse período de faturação. Não podes alterar o teu limite de crédito, mas podes removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure.](../../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Monitorizar os custos

Ao começar a utilizar a Base de Dados Azure SQL, pode ver os custos estimados no portal. Utilize as seguintes medidas para rever a estimativa de custos:

1. Inscreva-se no portal Azure e navegue para o grupo de recursos da sua base de dados Azure SQL. Pode localizar o grupo de recursos navegando na sua base de dados e selecione o **grupo de Recursos** na secção Vista **Geral.**
1. No menu, selecione **Análise de Custos**.
1. Ver **os custos acumulados** e definir o gráfico na parte inferior para o **nome de Serviço**. Este gráfico mostra uma estimativa dos custos atuais da Base de Dados SQL. Para reduzir os custos de toda a página para Azure SQL Database, **selecione Adicionar filtro** e, em seguida, selecione **Azure SQL Database**. As informações e os preços na imagem a seguir são apenas para fins:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Exemplo mostrando custos acumulados no portal Azure":::

A partir daqui, você pode explorar os custos por conta própria. Para obter mais informações sobre as diferentes definições de análise de custos, consulte [Começar a analisar os custos](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Criar orçamentos

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Pode criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Outras formas de gerir e reduzir custos para a Base de Dados Azure SQL

O Azure SQL Database também permite aumentar ou reduzir os recursos para controlar os custos com base nas necessidades da sua aplicação. Para mais informações, consulte [os recursos de base de dados de escala Dynamically](scale-resources.md).

Poupe dinheiro comprometendo-se a uma reserva de recursos computacional por um a três anos. Para mais informações, consulte [Economize custos para recursos com capacidade reservada.](reserved-capacity-overview.md)


## <a name="next-steps"></a>Passos seguintes

- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.