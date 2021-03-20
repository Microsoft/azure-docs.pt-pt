---
title: Planear e gerir os custos do Armazenamento de blobs do Azure
description: Saiba como planear e gerir os custos para o armazenamento da Azure Blob utilizando a análise de custos no portal Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0bad4637f13bbcf02047416499e4f82fdc53eb4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98601315"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planear e gerir os custos do Armazenamento de blobs do Azure

Este artigo ajuda-o a planear e gerir os custos para o armazenamento da Azure Blob. Em primeiro lugar, calcule os custos utilizando a calculadora de preços Azure. Depois de criar a sua conta de armazenamento, otimize a conta para que pague apenas pelo que precisa. Utilize funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e monitorizar as tendências de gastos para identificar áreas onde possa querer agir.

Tenha em mente que os custos para o armazenamento blob são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como estimar e gerir os custos para o armazenamento blob, você está cobrado para todos os serviços e recursos Azure usados para a sua subscrição Azure, incluindo os serviços de terceiros. Depois de estar familiarizado com os custos de gestão do armazenamento blob, pode aplicar métodos semelhantes para gerir custos para todos os serviços Azure utilizados na sua subscrição.

## <a name="estimate-costs"></a>Cálculo de custos

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar e comece a transferir dados para uma conta de Armazenamento Azure.

1. Na página da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) escolha o azulejo **das Contas de Armazenamento.**

2. Percorra a página e localize a secção **de Contas** de Armazenamento da sua estimativa.

3. Escolha opções nas listas de espera. 

   Ao modificar o valor destas listas de entrega, a estimativa de custos muda. Esta estimativa aparece no canto superior, bem como na parte inferior da estimativa.

   ![Screenshot mostrando a sua estimativa](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   À medida que altera o valor da lista de drop-down **tipo,** outras opções que aparecem nesta folha de cálculo também mudam. Utilize os links na secção **Mais Informações** para saber mais sobre o que cada opção significa e como estas opções afetam o preço das operações relacionadas com o armazenamento. 

4. Modifique as opções restantes para ver o seu efeito na sua estimativa.

   > [!NOTE]
   > Você pode pagar os custos de armazenamento Azure Blob com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="optimize-costs"></a>Otimizar custos

Considere usar estas opções para reduzir custos. 

- Capacidade de armazenamento de reserva

- Organizar dados em níveis de acesso

- Mover automaticamente dados entre os níveis de acesso

Esta secção cobre cada opção com mais detalhes. 

#### <a name="reserve-storage-capacity"></a>Capacidade de armazenamento de reserva

Você pode economizar dinheiro em custos de armazenamento para dados blob com capacidade reservada Azure Storage. A capacidade reservada para o Azure Storage oferece-lhe um desconto na capacidade de blobs de blocos e para dados da Azure Data Lake Storage Gen2 em contas de armazenamento padrão quando se compromete a uma reserva por um ano ou três anos. Uma reserva proporciona uma capacidade de armazenamento fixa para o termo da reserva. A capacidade reservada para o Azure Storage pode reduzir significativamente os seus custos de capacidade para blobs de blocos e dados da Azure Data Lake Storage Gen2. 

Para saber mais, consulte [os custos da Otimização para armazenamento Blob com capacidade reservada.](../blobs/storage-blob-reserved-capacity.md)

#### <a name="organize-data-into-access-tiers"></a>Organizar dados em níveis de acesso

Pode reduzir os custos colocando dados blob nos níveis de acesso mais rentáveis. Escolha entre três níveis que são projetados para otimizar os seus custos em torno do uso de dados. Por exemplo, o nível *quente* tem um custo de armazenamento mais elevado, mas menor custo de acesso. Portanto, se planeia aceder frequentemente aos dados, o nível quente pode ser a escolha mais rentável. Se planeia aceder aos dados com menos frequência, o nível *de frio* ou *arquivo* pode fazer mais sentido porque aumenta o custo de acesso aos dados, reduzindo ao mesmo tempo o custo de armazenamento de dados.    

Para saber mais, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e arquivados.](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)

#### <a name="automatically-move-data-between-access-tiers"></a>Mover automaticamente dados entre os níveis de acesso

Use políticas de gestão do ciclo de vida para mover periodicamente dados entre níveis para economizar mais dinheiro. Estas políticas podem mover dados usando regras que especifique. Por exemplo, pode criar uma regra que move as bolhas para o nível de arquivo se essa bolha não for modificada em 90 dias. Ao criar políticas que ajustem o nível de acesso dos seus dados, pode projetar as opções de armazenamento menos dispendiosas para as suas necessidades.

Para saber mais, consulte [Gerir o ciclo de vida de armazenamento Azure Blob](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar alertas que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. No entanto, podem ter uma funcionalidade limitada para gerir os custos individuais do serviço Azure, como o custo do Azure Storage, porque são projetados para controlar os custos a um nível mais elevado.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar os recursos da Azure com o Azure Storage, incorre em custos. Os custos da unidade de utilização do recurso variam em intervalos de tempo (segundos, minutos, horas e dias) ou por utilização unitária (bytes, megabytes, e assim por diante.) Os custos são incorridos assim que a utilização do Azure Storage começa. Pode ver os custos no painel de análise de [custos](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) no portal Azure.

Quando utiliza a análise de custos, pode ver os custos de Armazenamento Azure em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Também é possível ver os custos com os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, também pode facilmente ver onde excederam.

>[!NOTE]
> A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Para ver os custos de armazenamento da Azure na análise de custos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Abra a janela **De Gestão de Custos + Faturação,** selecione **a gestão** de custos do menu e, em seguida, selecione **análise de custos**. Em seguida, pode alterar o âmbito de uma subscrição específica a partir do **dropdown** scope.

   ![Screenshot mostrando o âmbito](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Para visualizar apenas os custos para o Armazenamento Azure, **selecione adicionar filtro** e, em seguida, selecione o nome de **Serviço**. Em seguida, escolha **o armazenamento** da lista. 

   Aqui está um exemplo mostrando custos para apenas Azure Storage:

   ![Screenshot mostrando filtro por armazenamento](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões de Azure (localizações) e do grupo de recursos também aparecem. Também pode adicionar outros filtros (Por exemplo: um filtro para ver os custos de contas de armazenamento específicas).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como os preços funcionam com o Azure Storage. Consulte os [preços gerais do armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).
- [Otimize os custos de armazenamento blob com capacidade reservada.](../blobs/storage-blob-reserved-capacity.md)
- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.