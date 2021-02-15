---
title: Planear e gerir os custos da Azure Data Factory
description: Este artigo descreve como pode planear e gerir custos para a Azure Data Factory
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372897"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planear e gerir os custos da Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory é um serviço de integração de dados sem servidor e elástico construído para a escala de nuvem.  Isto significa que não há um cálculo de tamanho fixo que você precisa planejar para a carga máxima; em vez disso, especifica quanto recurso deve alocar a pedido por operação, o que lhe permite conceber os processos ETL de uma forma muito mais escalável. Além disso, a ADF é cobrada num plano baseado no consumo, o que significa que você só paga pelo que usa.

Este artigo descreve como pode planear e gerir custos para a Azure Data Factory.

*   Em primeiro lugar, no início do projeto ETL, conduz-se a prova de conceito e utiliza-se uma combinação de consumo por gasoduto e calculadora de preços para estimar os custos.
*   Depois de ter implantado os seus oleodutos para a produção, utiliza as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar as tendências de gastos.
*   Além disso, pode ver informações sobre consumo por gasoduto e consumo por atividade para entender quais os gasodutos e quais as atividades mais dispendiosas e identificar candidatos para a redução de custos.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Estimativa dos custos com o pipeline e a calculadora de consumo e de preços do gasoduto e da atividade

Pode utilizar a [calculadora de preços da ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) para obter uma estimativa do custo de funcionamento da sua carga de trabalho ETL na Azure Data Factory.  Para utilizar a calculadora, tem de inserir detalhes como o número de execuções de atividade, o número de horas de integração de dados, o tipo de cálculo utilizado para o Fluxo de Dados, a contagem de núcleos, a contagem de exemplos, a duração da execução, e assim por diante.

Uma das perguntas comummente colocadas para a calculadora de preços é que valores devem ser usados como entradas.  Durante a fase de prova de conceito, você pode realizar ensaios usando conjuntos de dados de amostra para entender o consumo de vários contadores ADF.  Em seguida, com base no consumo para o conjunto de dados da amostra, pode projetar o consumo para o conjunto completo de dados e programa de operacionalização.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não se destinam a implicar preços reais.

Por exemplo, digamos que você precisa mover 1 TB de dados diariamente de AWS S3 para Azure Data Lake Gen2.  Pode executar POC de movimentar 100 GB de dados para medir a produção de dados e compreender o consumo de faturação correspondente.

Aqui está um detalhe de atividade de cópia de amostra (a sua quilometragem real variará com base na forma do seu conjunto de dados específico, velocidades de rede, limites de saída na conta S3, limites de entrada na ADLS Gen2, e outros fatores).

![Execução de cópia S3](media/plan-manage-costs/s3-copy-run-details.png)

Aproveitando a [monitorização do consumo ao nível do gasoduto,](#monitor-consumption-at-pipeline-run-level)pode ver as quantidades correspondentes de consumo de contadores de movimentos de dados:

![S3 copiar consumo de gasoduto](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Portanto, o número total de DIU-horas que leva para mover 1 TB por dia durante todo o mês é:

1.2667 (DIU-hora) * (1 TB / 100 GB) * 30 (dias num mês) = 380 DIU-hora

Agora pode ligar 30 corridas de atividade e 380 DIU-horas na calculadora de preços da ADF para obter uma estimativa da sua conta mensal:

![Calculadora de preços de cópia S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerir custos e criar alertas que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos.  Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos.  Quando cria um orçamento, pode fazê-lo ao nível da subscrição ou a uma granularidade mais baixa, adicionando filtros adicionais, como iD de recursos e nome do medidor.  Mas não se pode criar orçamentos para oleodutos individuais dentro de uma fábrica.

## <a name="monitor-costs-at-factory-level"></a>Monitorizar os custos a nível de fábrica

Ao começar a utilizar a Azure Data Factory, pode ver os custos incorridos no painel de análise de [custos](../cost-management-billing/costs/quick-acm-cost-analysis.md) no portal Azure.

1. Para [visualizar](../cost-management-billing/costs/quick-acm-cost-analysis.md)a análise de custos, abra a janela **Cost Management + Billing,** selecione **a gestão** de custos do menu e, em seguida, selecione **Open cost analysis**.
2. A visão padrão mostra os custos acumulados para o mês em curso.  Pode mudar para um intervalo de tempo diferente e uma granularidade diferente, como diariamente ou mensalmente.
3. Para reduzir os custos para um único serviço, como a Azure Data Factory, selecione **adicionar filtro** e, em seguida, selecione **o nome de Serviço**.  Em seguida, escolha **Azure data factory v2** da lista.
4. Pode adicionar filtros adicionais para analisar o custo para uma instância de fábrica específica e granularidade específica do medidor ADF.

   ![Análise de custos](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorizar o consumo ao nível do gasoduto

Dependendo dos tipos de atividades que tem no seu oleoduto, da quantidade de dados que está a mover e transformar, e da complexidade da transformação, executar um oleoduto girará diferentes contadores de faturação na Azure Data Factory.

Pode ver a quantidade de consumo para diferentes contadores para gasodutos individuais na experiência do utilizador da Azure Data Factory. Para abrir a experiência de monitorização, selecione o **telha Monitor & Gerir** na lâmina de fábrica de dados do [portal Azure](https://portal.azure.com/). Se já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda. A visão de monitorização predefinida é a lista de execuções do gasoduto.

Ao clicar no botão **De consumo** ao lado do nome do gasoduto, apresentará uma janela pop-up que lhe mostra o consumo do seu gasoduto agregado em todas as atividades dentro do oleoduto.

![Consumo de gasoduto](media/plan-manage-costs/pipeline-run-consumption.png)

![Detalhes do consumo de gasodutos](media/plan-manage-costs/pipeline-consumption-details.png)

A vista de consumo de gasoduto mostra-lhe a quantidade consumida por cada medidor ADF para a corrida específica do gasoduto, mas não mostra o preço real cobrado, porque o valor cobrado a si depende do tipo de conta Azure que tem e do tipo de moeda utilizada.  Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitorizar o consumo a nível de atividade
Uma vez que você entenda o consumo agregado ao nível do gasoduto, há cenários em que você precisa aprofundar e identificar qual é a atividade mais dispendiosa dentro do oleoduto.

Para ver o consumo a nível de atividade, vá à sua fábrica de dados **Autor & Monitor** UI. A partir do separador **Monitor** onde vê uma lista de correções de gasodutos, clique no link **de nome** do gasoduto para aceder à lista de atividades executadas no gasoduto.  Clique no botão **Saída** ao lado do nome da atividade e procure a propriedade **billableDuration** na saída JSON:

Aqui está uma amostra de uma atividade de cópia executada:

![Saída de cópia](media/plan-manage-costs/copy-output.png)

E aqui está uma amostra de uma atividade de mapeamento do fluxo de dados:

![Saída do fluxo de dados](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre como os preços funcionam na Azure Data Factory:

- [Página de preços da Fábrica de Dados Azure](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Compreender a Azure Data Factory através de exemplos](./pricing-concepts.md)
- [Calculadora de preços da Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory)