---
title: Utilizações comuns da análise de custos no Azure Cost Management
description: Este artigo explica como pode obter resultados para tarefas comuns da análise de custos no Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 901f271ac401cb985e59c434b9e6d7f8db03889f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79203105"
---
# <a name="common-cost-analysis-uses"></a>Utilizações comuns da análise de custos

Os utilizadores do Azure Cost Management procuram frequentemente respostas a perguntas que muitos outros também fazem. Este artigo orienta-o através da obtenção de resultados para tarefas comuns da análise de custos no Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Ver a discriminação dos custos por serviço do Azure

A visualização dos custos por serviço do Azure pode ajudá-lo a compreender melhor as partes mais dispendiosas da sua infraestrutura. Por exemplo, os custos de computação de VMs podem ser baixos. No entanto, pode acumular custos de rede significativos devido à quantidade de informações emitidas pelas VMs. Compreender os principais impulsionadores dos custos dos serviços Azure é essencial para que possa ajustar a utilização do serviço, conforme necessário.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Custo por serviço** e, em seguida, agrupe por **Escalão de serviço**.
1. Altere a vista para **Tabela**.

![Discriminação dos custos por serviço do Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Ver a discriminação dos custos por recurso do Azure

Os serviços são criados com recursos do Azure. A análise dos custos com base nos recursos pode ajudá-lo a identificar rapidamente os principais contribuidores dos custos. Se um serviço tiver recursos demasiado dispendiosos, considere fazer alterações para reduzir os custos.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Custo por recurso**.
1. Altere a vista para **Tabela**.

![Ver a discriminação dos custos por recurso do Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Ver a discriminação dos custos por dimensões selecionadas

As dimensões permitem-lhe organizar os custos com base em vários valores de metadados que aparecem nos custos. Por exemplo, pode agrupar os custos por localização.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione o filtro **Agrupar por**.  
    ![Selecionar um item Agrupar por](./media/cost-analysis-common-uses/group-by.png)
1. Opcionalmente, guarde a vista para utilização posterior.
1. Clique num gráfico circular abaixo do gráfico para ver dados mais detalhados.  
    ![Ver a discriminação dos custos por dimensões selecionadas](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Ver os custos por dia ou por mês

A consulta dos custos diários e mensais poderá ajudá-lo a compreender melhor se existe um período da semana ou do ano no qual os custos são mais elevados. Se tiver mais tráfego de clientes num período de férias, verifica um aumento correspondente nos custos do Azure? A sexta-feira é mais dispendiosa do que a segunda-feira?

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Defina a**Granularidade** como **Mensal** ou **Diária**.

![Ver os custos por dia](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Ver os custos para uma etiqueta específica

Muitos utilizadores do Azure aplicam etiquetas aos recursos, como um centro de custos ou um ambiente de desenvolvimento (produção e teste), para melhor categorizar os custos. As etiquetas são apresentadas como uma dimensão na análise de custos. Pode utilizar a dimensão para obter informações sobre as categorizações de identificação personalizadas.

O suporte de etiquetas aplica-se à utilização comunicadas *depois* de a etiqueta ter sido aplicada ao recurso. As etiquetas não são aplicadas retroativamente para rollups de custos.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Agrupar por** para a etiqueta.

![Ver os custos de uma etiqueta específica](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Transferir os detalhes de utilização

O ficheiro de relatório dos detalhes de utilização, no formato CSV, proporciona uma discriminação de todas as taxas acumuladas numa fatura. Pode utilizar o relatório para o comparar e compreender melhor a fatura. Cada custo faturado na fatura corresponde aos custos discriminados no relatório de utilização.

1. No portal do Azure, aceda ao separador **Utilização e Custos** de uma conta de faturação ou subscrição. Por exemplo: **Cost Management + Faturação** > **Faturação** > **Utilização + custos**.
1. Selecione o item de linha que deseja transferir e, em seguida, clique no símbolo de transferência.  
    ![Transferir a utilização e os custos](./media/cost-analysis-common-uses/download1.png)
1.  Selecione o ficheiro de utilização a transferir.  
    ![Escolher o ficheiro de utilização a transferir](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Ver a discriminação mensal dos custos do EA

A inscrição EA acumula os custos de toda a organização. Compreender a forma como os custos são acumulados e faturados ao longo do tempo ajudará a abordar os intervenientes corretos para garantir que os custos são geridos de forma responsável.

Só são mostrados os custos da inscrição ativa. Se tiver transferido uma inscrição (inativa) para uma nova (ativa), os custos da inscrição anterior não são apresentados no Cost Management.


1. No portal do Azure, aceda a **Cost Management + Faturação** > **Descrição geral**.
1. Clique em **Discriminação** para o mês atual e veja a discriminação da sua alocação monetária.  
    ![Descrição geral dos custos do EA – resumo da discriminação](./media/cost-analysis-common-uses/breakdown1.png)
1.  Clique no separador **Utilização e Custos** e veja a discriminação do mês anterior no intervalo de tempo escolhido.  
    ![Separador Utilização e custos](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Ver o custo mensal da inscrição por período

Utilize uma vista de gráfico dos custos mensais da sua inscrição para compreender as tendências dos custos e os valores faturados para um determinado período.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione a sua inscrição e defina o período da inscrição.
1. Defina a granularidade como mensal e, em seguida, defina a vista como **Coluna (empilhada)** .

Pode agrupar e filtrar os dados para obter uma análise mais detalhada.

![Custo mensal da inscrição por período](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Ver os sustos acumulados da inscrição EA

Veja os custos acumulados líquidos ao longo do tempo para compreender as despesas gerais da sua organização num dado período.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione a sua inscrição e, em seguida, veja os seus custos acumulados atuais.

![Custos acumulados da inscrição](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Passos seguintes
- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](quick-acm-cost-analysis.md).
- Leia a [Documentação do Cost Management](../index.yml).
