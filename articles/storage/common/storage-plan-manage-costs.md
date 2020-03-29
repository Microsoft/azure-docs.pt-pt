---
title: Planear e gerir custos para o Armazenamento Azure
description: Saiba como planear e gerir os custos para o Armazenamento Azure utilizando a análise de custos no portal Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304527"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planear e gerir custos para o Armazenamento Azure

Este artigo descreve como planeia e gere os custos para o Armazenamento Azure. Em primeiro lugar, usa a calculadora de preços Azure para ajudar a planear os custos de armazenamento antes de adicionar quaisquer recursos. Depois de começar a utilizar recursos de Armazenamento Azure, use funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e monitorizar as tendências de gastos para identificar áreas onde possa querer agir.

Tenha em mente que os custos para o Armazenamento Azure são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir custos para o Armazenamento Azure, você é cobrado para todos os serviços e recursos Azure usados para a sua subscrição Azure, incluindo os serviços de terceiros. Depois de conhecer os custos de gestão do Armazenamento Azure, pode aplicar métodos semelhantes para gerir os custos de todos os serviços Azure utilizados na sua subscrição.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Estimar custos antes de criar uma conta de Armazenamento Azure

Utilize a calculadora de [preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar e começar a transferir dados para uma conta de Armazenamento Azure.

1. Na página da calculadora de [preços Azure,](https://azure.microsoft.com/pricing/calculator/) escolha o azulejo das Contas de **Armazenamento.**

2. Percorra a página e localize a secção de Contas de **Armazenamento** da sua estimativa.

3. Escolha as opções entre as listas de abandono. 

   À medida que modifica o valor destas listas de abandono, a estimativa de custos muda. Esta estimativa aparece no canto superior, bem como no fundo da estimativa. 
    
   ![Monitorizar os custos com painel de análise de custos](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   À medida que altera o valor da lista de abandono do **Tipo,** outras opções que aparecem nesta folha de cálculo mudam também. Utilize os links na secção **Mais Informações** para saber mais sobre o que cada opção significa e como estas opções afetam o preço das operações relacionadas com o armazenamento. 

4. Modifique as opções restantes para ver o seu efeito na sua estimativa.

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerir custos e criar alertas que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições do Azure e grupos de recursos, por isso são úteis como parte de uma estratégia global de monitorização de custos. No entanto, podem ter uma funcionalidade limitada para gerir custos individuais de serviço Azure, como o custo do Armazenamento Azure, porque são projetados para rastrear custos a um nível mais elevado.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar os recursos Azure com o Armazenamento Azure, incorre em custos. Os custos da unidade de utilização de recursos variam por intervalos de tempo (segundos, minutos, horas e dias) ou por utilização da unidade (bytes, megabytes, e assim por diante.) Os custos são incorridos assim que a utilização do Armazenamento Azure começa. Pode ver os custos no painel de análise de [custos](../../cost-management-billing/costs/quick-acm-cost-analysis.md) no portal Azure.

Quando utilizar a análise de custos, pode ver os custos de Armazenamento Azure em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são de dia, atuais e anteriores, e ano. Pode também ver os custos com orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar as tendências de gastos e ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, também pode facilmente ver onde foram ultrapassados.

Para ver os custos de armazenamento do Azure na análise de custos:

1. Assine no [portal Azure.](https://portal.azure.com)

2. Abra a janela **Cost Management + Billing,** selecione Gestão de **Custos** do menu e, em seguida, selecione **análise de custos**. Em seguida, pode alterar a margem de manobra para uma subscrição específica a partir do **dropdown** scope.

   ![Monitorizar os custos com painel de análise de custos](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Para ver apenas os custos para o Armazenamento Azure, selecione **Adicionar filtro** e, em seguida, selecione **o nome de serviço**. Em seguida, escolha o **armazenamento** da lista. 

   Aqui está um exemplo mostrando custos apenas para o Armazenamento Azure:

   ![Monitorizar os custos de armazenamento com painel de análise de custos](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões azure (localizações) e por grupo de recursos também aparecem.  

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre gestão de custos com análise de [custos.](../../cost-management-billing/costs/quick-acm-cost-analysis.md)

Consulte os seguintes artigos para saber mais sobre como os preços funcionam com o Armazenamento Azure:

- [Preços de visão geral do armazenamento azure](https://azure.microsoft.com/pricing/details/storage/)
- [Otimizar os custos do Armazenamento de blobs com a capacidade reservada](../blobs/storage-blob-reserved-capacity.md)
