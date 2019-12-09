---
title: Atividade de fluxo de dados no Azure Data Factory
description: Como executar fluxos de dados de dentro de um pipeline de data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: 47126d1cf51f4b27863bb0b11e73cfe5592b8d57
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929887"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Atividade de fluxo de dados no Azure Data Factory

Use a atividade fluxo de dados para transformar e mover dados por meio de fluxos de dados de mapeamento. Se você não estiver familiarizado com fluxos de dados, consulte [visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md)

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

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Flow | A referência ao fluxo de dados que está sendo executado | DataFlowReference | Sim
integrationRuntime | O ambiente de computação no qual o fluxo de dados é executado | IntegrationRuntimeReference | Sim
preparo. linkedService | Se você estiver usando uma fonte ou coletor do SQL DW, a conta de armazenamento usada para preparo do polybase | LinkedServiceReference | Somente se o fluxo de dados lê ou grava em um SQL DW
preparo. folderPath | Se você estiver usando uma fonte ou coletor do SQL DW, o caminho da pasta na conta de armazenamento de BLOBs usada para preparo do polybase | String | Somente se o fluxo de dados lê ou grava em um SQL DW

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "Executar fluxo de dados")

### <a name="data-flow-integration-runtime"></a>Tempo de execução de integração de fluxo de dados

Escolha qual Integration Runtime usar para a execução da atividade de fluxo de dados. Por padrão, Data Factory usará a solução de tempo de execução de integração do Azure com quatro núcleos de trabalho e nenhuma TTL (vida útil). Esse IR tem um tipo de computação de uso geral e é executado na mesma região que a sua fábrica. Você pode criar seus próprios tempos de execução de integração do Azure que definem regiões específicas, tipo de computação, contagens de núcleos e TTL para a execução da atividade de fluxo de dados.

Para execuções de pipeline, o cluster é um cluster de trabalho, que leva vários minutos para ser iniciado antes do início da execução. Se nenhum TTL for especificado, esse tempo de inicialização será necessário em cada execução de pipeline. Se você especificar um TTL, um pool de clusters quente permanecerá ativo durante o tempo especificado após a última execução, resultando em tempos de inicialização mais curtos. Por exemplo, se você tiver um TTL de 60 minutos e executar um fluxo de dados nele uma vez por hora, o pool de clusters permanecerá ativo. Para obter mais informações, consulte [tempo de execução de integração do Azure](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> A seleção de Integration Runtime na atividade de fluxo de dados só se aplica a *execuções disparadas* de seu pipeline. A depuração do pipeline com fluxos de dados é executada no cluster especificado na sessão de depuração.

### <a name="polybase"></a>PolyBase

Se você estiver usando um SQL Data Warehouse do Azure como um coletor ou fonte, deverá escolher um local de preparo para a carga do lote do polybase. O polybase permite o carregamento em lote em massa, em vez de carregar os dados linha por linha. O polybase reduz drasticamente o tempo de carregamento no SQL DW.

## <a name="parameterizing-data-flows"></a>Parametrizando fluxos de dados

### <a name="parameterized-datasets"></a>Conjuntos de valores com parâmetros

Se o fluxo de dados usar conjuntos de dados com parâmetros, defina os valores de parâmetro na guia **configurações** .

![Executar parâmetros de fluxo de dados](media/data-flow/params.png "Parâmetros")

### <a name="parameterized-data-flows"></a>Fluxos de dados com parâmetros

Se o fluxo de dados for parametrizado, defina os valores dinâmicos dos parâmetros de fluxo de dados na guia **parâmetros** . Você pode usar a linguagem de expressão de pipeline do ADF (somente para tipos de cadeia de caracteres) ou a linguagem de expressão de fluxo de dados para atribuir valores de parâmetro dinâmicos ou literais. Para obter mais informações, consulte [parâmetros de fluxo de dados](parameters-data-flow.md).

![Exemplo do parâmetro executar fluxo de dados](media/data-flow/parameter-example.png "Exemplo de parâmetro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Depuração de pipeline da atividade de fluxo de dados

Para executar uma execução de pipeline de depuração com uma atividade de fluxo de dados, você deve alternar o modo de depuração do fluxo de dados por meio do controle deslizante de **depuração do fluxo de dados** na barra superior. O modo de depuração permite executar o fluxo de dados em um cluster do Spark ativo. Para obter mais informações, consulte [modo de depuração](concepts-data-flow-debug-mode.md).

![Botão de depuração](media/data-flow/debugbutton.png "Botão de depuração")

O pipeline de depuração é executado no cluster de depuração ativa, não no ambiente do Integration Runtime especificado nas configurações de atividade do fluxo de dados. Você pode escolher o ambiente de computação de depuração ao iniciar o modo de depuração.

## <a name="monitoring-the-data-flow-activity"></a>Monitorando a atividade de fluxo de dados

A atividade de fluxo de dados tem uma experiência de monitoramento especial, na qual você pode exibir informações sobre particionamento, tempo de estágio e linhagem de dados. Abra o painel Monitoramento por meio do ícone óculos em **ações**. Para obter mais informações, consulte [monitorando fluxos de dados](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Usar resultados de atividade de fluxo de dados em uma atividade subsequente

A atividade de fluxo de dados gera métricas referentes ao número de linhas gravadas em cada coletor e linhas lidas de cada fonte. Esses resultados são retornados na seção `output` do resultado da execução da atividade. As métricas retornadas estão no formato do JSON abaixo.

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

Por exemplo, para chegar ao número de linhas gravadas em um coletor chamado ' sink1 ' em uma atividade denominada ' dataflowActivity ', use `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`.

Para obter o número de linhas lidas de uma fonte denominada ' origem1 ' que foi usada nesse coletor, use `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Se um coletor tiver zero linhas gravadas, ele não aparecerá em métricas. A existência pode ser verificada usando a função `contains`. Por exemplo, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificará se alguma linha foi gravada em sink1.

## <a name="next-steps"></a>Passos seguintes

Consulte atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
