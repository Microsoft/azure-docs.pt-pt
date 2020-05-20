---
title: Planear e gerir custos para a Azure Data Factory
description: Este artigo descreve como pode planear e gerir custos para a Azure Data Factory
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691265"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planear e gerir custos para a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory é um serviço de integração de dados sem servidores e elásticos construído para a escala de nuvem.  Isto significa que não existe um cálculo de tamanho fixo que você precisa planear para a carga máxima; em vez disso, especifica quanto recurso a locar a pedido por operação, o que lhe permite projetar os processos ETL de uma forma muito mais escalável. Além disso, a ADF é faturada num plano baseado no consumo, o que significa que só paga pelo que usa.

Este artigo descreve como pode planear e gerir custos para a Azure Data Factory.

*   Primeiro, no início do projeto ETL, conduz-se a prova de conceito e utiliza-se uma combinação de consumo por pipeline e calculadora de preços para estimar custos.
*   Depois de ter implantado os seus oleodutos para a produção, utiliza as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar as tendências de gastos.
*   Além disso, pode visualizar informações sobre o consumo por gasoduto e por atividade para perceber quais os oleodutos e quais as atividades mais dispendiosas e identificar candidatos para a redução de custos.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Estimar os custos utilizando o consumo de gasodutos e de atividade e a calculadora de preços

Pode utilizar a calculadora de [preços ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) para obter uma estimativa do custo de funcionamento da sua carga de trabalho ETL na Azure Data Factory.  Para utilizar a calculadora, tem de inserir detalhes como o número de execuções de atividade, o número de horas unitárias de integração de dados, o tipo de computação utilizada para o Fluxo de Dados, contagem de núcleos, contagem de casos, duração da execução, e assim por diante.

Uma das perguntas mais frequentes para a calculadora de preços é quais os valores que devem ser usados como inputs.  Durante a fase de prova de conceito, pode realizar ensaios utilizando conjuntos de dados de amostras para compreender o consumo de vários contadores ADF.  Depois, com base no consumo do conjunto de dados da amostra, pode projetar o consumo para o conjunto completo de dados e programação de operacionalização.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não se destinam a implicar preços reais.

Por exemplo, digamos que precisa de mover 1 TB de dados diariamente de AWS S3 para Azure Data Lake Gen2.  Pode executar POC de movimentação de 100 GB de dados para medir a entrada de dados e compreender o consumo de faturação correspondente.

Aqui está um detalhe de execução de amostra gema (a sua quilometragem real variará em função da forma do seu conjunto de dados específico, velocidades de rede, limites de saída na conta S3, limites de ingresso na ADLS Gen2, e outros fatores).

![Execução de cópia S3](media/plan-manage-costs/s3-copy-run-details.png)

Aproveitando a monitorização do [consumo ao nível do gasoduto,](#monitor-consumption-at-pipeline-run-level)pode ver as quantidades correspondentes de consumo de contadores de movimentos de dados:

![Consumo de gasoduto de cópia S3](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Portanto, o número total de diu-horas que leva para mover 1 TB por dia durante todo o mês é:

1.2667 (DIU-hora) * (1 TB / 100 GB) * 30 (dias num mês) = 380 DIU-hora

Agora pode ligar 30 corridas de atividade e 380 DIU-hora na calculadora de preços ADF para obter uma estimativa da sua fatura mensal:

![Calculadora de preços de cópia S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets) para gerir custos e criar alertas que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos.  Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos.  Quando cria um orçamento, pode fazê-lo ao nível da subscrição ou a uma granularidade mais baixa, adicionando filtros adicionais, como o id de recursos e o nome do contador.  Mas não se pode criar orçamentos para oleodutos individuais dentro de uma fábrica.

## <a name="monitor-costs-at-factory-level"></a>Monitorizar os custos a nível da fábrica

À medida que começa a utilizar a Azure Data Factory, pode ver os custos incorridos no painel de análise de [custos](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) no portal Azure.

1. Para visualizar a análise de [custos,](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)abra a janela **Cost Management + Billing,** selecione **a gestão** de custos do menu e, em seguida, selecione Análise de **custos abertos**.
2. A visão padrão mostra os custos acumulados para o mês em curso.  Pode mudar para um intervalo de tempo diferente e uma granularidade diferente, como diariamente ou mensalmente.
3. Para reduzir os custos de um único serviço, como a Azure Data Factory, selecione **Adicionar filtro** e, em seguida, selecione o nome **de serviço**.  Em seguida, escolha a fábrica de **dados Azure v2** da lista.
4. Pode adicionar filtros adicionais para analisar o custo de uma instância de fábrica específica e granularidade específica do medidor ADF.

   ![Análise de custos](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorizar o consumo ao nível do gasoduto

Dependendo dos tipos de atividades que tem no seu pipeline, quantos dados está a mover-se e a transformar, e a complexidade da transformação, executar um pipeline girará diferentes contadores de faturação na Azure Data Factory.

Pode ver a quantidade de consumo para diferentes metros para corridas individuais de gasodutos na experiência do utilizador da Azure Data Factory. Para abrir a experiência de monitorização, selecione o **Monitor & Gerir** o azulejo na lâmina da fábrica de dados do portal [Azure](https://portal.azure.com/). Se já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda. A vista de monitorização padrão é a lista de execuções de gasodutos.

Clicar no botão **Consumo** ao lado do nome do pipeline apresentará uma janela pop-up que lhe mostra o consumo do seu pipeline agregado em todas as atividades dentro do pipeline.

![Consumo de gasodutos](media/plan-manage-costs/pipeline-run-consumption.png)

![Detalhes do consumo de gasodutos](media/plan-manage-costs/pipeline-consumption-details.png)

A vista de consumo de gasoduto mostra-lhe a quantidade consumida por cada medidor ADF para a execução específica do gasoduto, mas não mostra o preço real cobrado, porque o valor cobrado a você depende do tipo de conta Azure que você tem e do tipo de moeda usada.  Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitorizar o consumo a nível de atividade
Uma vez que se compreende o consumo agregado ao nível do gasoduto, há cenários em que é necessário aprofundar e identificar qual é a atividade mais dispendiosa dentro do oleoduto.

Para ver o consumo a nível de atividade, vá à sua fábrica de dados **Autor & Monitor** UI. A partir do separador **Monitor** onde vê uma lista de execução de pipeline, clique no link de nome do **pipeline** para aceder à lista de funcionações de atividade na execução do gasoduto.  Clique no botão **Saída** ao lado do nome da atividade e procure por propriedades **de billableDura** na saída JSON:

Aqui está uma amostra de uma execução de atividade de cópia:

![Saída de cópia](media/plan-manage-costs/copy-output.png)

E aqui está uma amostra de uma execução de atividade de Mapping Data Flow:

![Saída de fluxo de dados](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber mais sobre como funcionam os preços na Azure Data Factory:

- [Página de preços da Fábrica de Dados Azure](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Compreender a Fábrica de Dados Azure através de exemplos](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Calculadora de preços da Fábrica de Dados Azure](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
