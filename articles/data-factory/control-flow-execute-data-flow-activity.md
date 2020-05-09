---
title: Atividade de Fluxo de Dados
description: Como executar os fluxos de dados de dentro de um oleoduto de fábrica de dados.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 04/30/2020
ms.openlocfilehash: a2e80b9320509144456663672ac5ae03f522459a
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735390"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Atividade de Fluxo de Dados na Fábrica de Dados azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a atividade data Flow para transformar e mover dados através de fluxos de dados de mapeamento. Se você é novo em fluxos de dados, consulte [mapeiação do fluxo](concepts-data-flow-overview.md) de dados

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
fluxo de dados | A referência ao Fluxo de Dados que está a ser executada | Referência de fluxo de dados | Sim
integraçãoTempo de corrida | O ambiente computacional em que o fluxo de dados funciona. Se não for especificado, será utilizado o tempo de execução de integração Azure de resolução automática. Apenas são apoiados os tempos de integração da auto-resolução da região. | IntegraçãoRuntimeReference | No
compute.coreCount | O número de núcleos usados no aglomerado de faíscas. Só pode ser especificado se o tempo de execução de integração azure de resolução automática for utilizado | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType | O tipo de computação usada no aglomerado de faíscas. Só pode ser especificado se o tempo de execução de integração azure de resolução automática for utilizado | "Geral", "ComputeOptimized", "MemoryOptimized" | No
staging.linkedService | Se estiver a utilizar uma fonte ou pia SQL DW, a conta de armazenamento utilizada para a encenação da PolyBase | Referência de Serviços Linked | Só se o fluxo de dados ler ou escrever para um DW SQL
staging.folderPath | Se estiver a utilizar uma fonte ou pia SQL DW, o caminho da pasta na conta de armazenamento blob utilizada para a encenação da PolyBase | String | Só se o fluxo de dados ler ou escrever para um DW SQL

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "Executar fluxo de dados")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Calculação de fluxo de dados de tamanho dinâmico no tempo de execução

As propriedades do Core Count e do Compute Type podem ser definidas de forma dinâmica para se ajustarem ao tamanho dos dados de origem que chegam no prazo de execução. Utilize atividades de pipeline como Lookup ou Get Metadata para encontrar o tamanho dos dados do conjunto de dados de origem. Em seguida, use Adicionar Conteúdo Dinâmico nas propriedades da atividade do Fluxo de Dados.

![Fluxo dinâmico de dados](media/data-flow/dyna1.png "Fluxo dinâmico de dados")

[Aqui está um breve tutorial de vídeo explicando esta técnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Tempo de execução da integração do Fluxo de Dados

Escolha qual o Tempo de Execução de Integração a utilizar para a execução da sua atividade de Fluxo de Dados. Por padrão, a Data Factory utilizará o tempo de execução de integração azure de resolução automática com quatro núcleos de trabalhadores e sem tempo para viver (TTL). Este IR tem um tipo de computação de propósito geral e funciona na mesma região que a sua fábrica. Pode criar os seus próprios RunTimes de Integração Azure que definem regiões específicas, tipo de computação, contagens de núcleo e TTL para a execução da sua atividade de fluxo de dados. Neste momento, apenas os tempos de integração da auto-resolução da região são suportados na atividade de fluxo de dados.

Para execuções em gasodutos, o cluster é um aglomerado de trabalho, que leva vários minutos para começar antes da execução começar. Se não for especificado nenhum TTL, este tempo de arranque é necessário em todas as condutas de gasodutos. Se especificar um TTL, um cluster quente permanecerá ativo durante o tempo especificado após a última execução, resultando em tempos de arranque mais curtos. Por exemplo, se tiver um TTL de 60 minutos e executar um fluxo de dados nele uma vez por hora, o cluster pool permanecerá ativo. Para mais informações, consulte o tempo de [execução da integração do Azure.](concepts-integration-runtime.md)

![Tempo de execução da integração azure](media/data-flow/ir-new.png "Tempo de execução da integração azure")

> [!IMPORTANT]
> A seleção de tempo de integração na atividade do Fluxo de Dados aplica-se apenas às *execuções desencadeadas* do seu pipeline. Depurando o seu pipeline com fluxos de dados no cluster especificado na sessão de depuração.

### <a name="polybase"></a>PolyBase

Se estiver a utilizar um Armazém de Dados Azure SQL como pia ou fonte, tem de escolher um local de paragem para a sua carga de lote PolyBase. A PolyBase permite o carregamento do lote a granel em vez de carregar os dados de linha a linha. A PolyBase reduz drasticamente o tempo de carga no DW SQL.

## <a name="parameterizing-data-flows"></a>Parametrização fluxos de dados

### <a name="parameterized-datasets"></a>Conjuntos de dados parametrizados

Se o fluxo de dados utilizar conjuntos de dados parametrizados, defina os valores do parâmetro no separador **Definições.**

![Executar parâmetros de fluxo de dados](media/data-flow/params.png "Parâmetros")

### <a name="parameterized-data-flows"></a>Fluxos de dados parametrizados

Se o seu fluxo de dados estiver parametrómetro, defina os valores dinâmicos dos parâmetros de fluxo de dados no separador **Parâmetros.** Pode utilizar a linguagem de expressão do gasoduto ADF ou a linguagem de expressão do fluxo de dados para atribuir valores de parâmetros dinâmicos ou literais. Para mais informações, consulte [Parâmetros de Fluxo](parameters-data-flow.md)de Dados .

### <a name="parameterized-compute-properties"></a>Propriedades computacionais parametrizadas.

Pode parametrizar a contagem de núcleos ou o tipo de cálculo se utilizar o tempo de execução de integração azure de resolução automática e especificar valores para compute.coreCount e compute.computeType.

![Execute o exemplo do parâmetro de fluxo de dados](media/data-flow/parameterize-compute.png "Exemplo de parâmetro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Depuração do gasoduto da atividade do Fluxo de Dados

Para executar um pipeline de depuração executado com uma atividade de Fluxo de Dados, deve ligar o modo de depuração de fluxo de dados através do slider **Debug Debug** de fluxo de dados na barra superior. O modo dedepura permite-lhe executar o fluxo de dados contra um cluster Spark ativo. Para mais informações, consulte [debug mode](concepts-data-flow-debug-mode.md).

![Botão de depuração](media/data-flow/debugbutton.png "Botão de depuração")

O gasoduto de depuração é contra o cluster de depuração ativo, não o ambiente de funcionamento de integração especificado nas definições de atividade do Fluxo de Dados. Pode escolher o ambiente de depuração quando iniciar o modo de depuração.

## <a name="monitoring-the-data-flow-activity"></a>Monitorização da atividade do Fluxo de Dados

A atividade data Flow tem uma experiência especial de monitorização onde pode ver informações de partição, tempo de palco e linhagem de dados. Abra o painel de monitorização através do ícone dos óculos em **Ações**. Para mais informações, consulte [Monitorização de Fluxos de Dados](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Utilizar a atividade do Fluxo de Dados resulta numa atividade subsequente

As métricas da atividade de fluxo de dados em relação ao número de linhas escritas em cada pia e linhas lidas de cada fonte. Estes resultados são `output` devolvidos na secção do resultado da execução da atividade. As métricas devolvidas estão no formato abaixo do json.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Por exemplo, para chegar ao número de linhas escritas a um lavatório chamado 'sink1' numa atividade denominada "dataflowActivity", utilize. `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`

Para obter o número de linhas lidas de uma fonte chamada 'source1' que foi usada naquele lavatório, use `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Se uma pia tiver zero linhas escritas, não aparecerá em métricas. A existência pode ser `contains` verificada utilizando a função. Por exemplo, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificará se alguma linha foi escrita para afundar1.

## <a name="next-steps"></a>Passos seguintes

Consulte as atividades de fluxo de controlo suportadas pela Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Executar a Atividade do Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)
