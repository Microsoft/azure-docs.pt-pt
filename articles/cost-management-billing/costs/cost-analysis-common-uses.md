---
title: Uso de análise de custo comum no gerenciamento de custos do Azure
description: Este artigo explica como você pode obter resultados para tarefas comuns de análise de custo no gerenciamento de custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988516"
---
# <a name="common-cost-analysis-uses"></a>Usos comuns de análise de custo

Os usuários do gerenciamento de custos do Azure muitas vezes desejam respostas a perguntas que muitos outros perguntam. Este artigo explica como obter resultados para tarefas comuns de análise de custo no gerenciamento de custos.

## <a name="view-cost-breakdown-by-azure-service"></a>Exibir detalhamento de custo pelo serviço do Azure

A exibição de custos por um serviço do Azure pode ajudá-lo a entender melhor as partes de sua infraestrutura que custam mais. Por exemplo, os custos de computação da VM podem ser pequenos. Ainda assim, você pode acumular custos de rede significativos devido à quantidade de informações emitidas por meio das VMs. Entender os principais drivers de custo de seus serviços do Azure é essencial para que você possa ajustar o uso do serviço, conforme necessário.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione **custo por serviço** e, em seguida, agrupe por **camada de serviço**.
1. Altere a exibição para **tabela**.

![Detalhamento de custo pelo serviço do Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Exibir detalhamento de custo por recurso do Azure

Seus serviços são criados com recursos do Azure. A revisão dos custos com base nos recursos pode ajudá-lo a identificar rapidamente seus principais colaboradores de custo. Se um serviço tiver recursos que são muito caros, considere fazer alterações para reduzir os custos.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione **custo por recurso**.
1. Altere a exibição para **tabela**.

![Exibir detalhamento de custo por recurso do Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Exibir detalhamento de custo por dimensões selecionadas

As dimensões permitem que você Organize seus custos com base em vários valores de metadados mostrados em seus encargos. Por exemplo, você pode agrupar seus custos por local.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione o filtro **Agrupar por** .  
    ![selecionar um grupo por item](./media/cost-analysis-common-uses/group-by.png)
1. Opcionalmente, você salva a exibição para uso posterior.
1. Clique em um gráfico de pizza abaixo do gráfico para exibir dados mais detalhados.  
    ![Exibir detalhamento de custo por dimensões selecionadas](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Exibir custos por dia ou por mês

Observar os custos diários e mensais pode ajudá-lo a entender melhor se há uma hora da semana ou do ano em que os custos são maiores. Se você tiver mais tráfego de cliente em um período de Natal, isso levará a um aumento correspondente nos custos do Azure? A sexta-feira é um dia mais dispendioso do que segunda?

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Defina a **granularidade** como **mensal** ou **diária**.

![Exibir custos por dia](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Exibir os custos de uma marca específica

Muitos usuários do Azure aplicam marcas a seus recursos, como um centro de custo ou um ambiente de desenvolvimento (produção e teste) para categorizar melhor os encargos. As marcas aparecem como uma dimensão na análise de custo. Você pode usar a dimensão para obter informações sobre suas categorizações de marcação personalizadas.

O suporte para marcas se aplica ao uso relatado *depois* que a marca foi aplicada ao recurso. As marcas não são aplicadas retroativamente para rollups de custo.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione **Agrupar por** para sua marca.

![Exibir os custos de uma marca específica](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Baixar os detalhes de uso

O arquivo de relatório de detalhes de uso, no formato CSV, fornece uma divisão de todos os encargos acumulados em direção a uma fatura. Você pode usar o relatório para compará-lo e entender melhor sua fatura. Cada encargo cobrado em sua fatura corresponde aos encargos desfeitos no relatório de uso.

1. Na portal do Azure, navegue até a guia **uso e encargos** de uma assinatura ou conta de cobrança. Por exemplo: **Gerenciamento de custos + cobrança** > **cobrança** > **uso + encargos**.
1. Selecione o item de linha para o qual baixar e clique no símbolo de download.  
    ![Baixar o uso e os encargos](./media/cost-analysis-common-uses/download1.png)
1.  Selecione o arquivo de uso para baixar.  
    ![Escolher um arquivo de uso para baixar](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Exibir divisão de custo mensal de EA

Seu registro EA acumula custos para toda a organização. Entender como os custos são acumulados e faturados ao longo do tempo ajuda a envolver os participantes apropriados para garantir que os custos sejam gerenciados com responsabilidade.

1. Na portal do Azure, navegue até **Gerenciamento de custos + cobrança** > **visão geral**.
1. Clique em **divisão** para o mês atual e exiba sua gravação de compromisso monetário para baixo.  
    Visão geral dos custos do EA ![-Resumo da divisão](./media/cost-analysis-common-uses/breakdown1.png)
1.  Clique na guia **uso e encargos** e exiba a divisão do mês anterior no período de tempo escolhido.  
    ![guia uso e encargos](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Exibir o custo mensal do registro por termo

Use uma exibição gráfica dos custos mensais do seu registro para entender as tendências de custo e os valores faturados para um determinado período.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione seu registro e defina o termo de registro.
1. Defina a granularidade como mensal e, em seguida, defina a exibição como **coluna (empilhada)** .

Você pode agrupar e filtrar seus dados para uma análise mais detalhada.

![Custo de registro mensal por termo](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Exibir custos acumulados de registro EA

Exiba os encargos líquidos acumulados ao longo do tempo para entender as despesas gerais da sua organização por um determinado período.

1. Na portal do Azure, navegue até análise de custo para seu escopo. Por exemplo: **Gerenciamento de custos + cobrança** > **gerenciamento de custos** > **análise de custo**.
1. Selecione seu registro e exiba os custos acumulados atuais.

![Custos acumulados no registro](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Passos seguintes
- Se ainda não tiver concluído o primeira guia de introdução do Cost Management, leia-a em [começar a analisar os custos](quick-acm-cost-analysis.md).
- Leia a [documentação do gerenciamento de custos](../index.yml).
