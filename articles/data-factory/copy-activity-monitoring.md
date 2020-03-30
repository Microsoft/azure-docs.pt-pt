---
title: Monitorizar a atividade de cópia
description: Saiba como monitorizar a execução da atividade de cópia na Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79125761"
---
# <a name="monitor-copy-activity"></a>Monitorizar a atividade de cópia

Este artigo descreve como monitorizar a execução da atividade de cópia na Azure Data Factory. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="monitor-visually"></a>Monitorizar visualmente

Depois de ter criado e publicado um oleoduto na Azure Data Factory, pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Pode monitorizar todas as suas operações de pipeline de forma nativa na experiência do utilizador da Azure Data Factory. Conheça a monitorização da Azure Data Factory em geral a partir do [monitor Visualde Ie Data Factory](monitor-visually.md).

Para monitorizar a execução da atividade do Copy, dirija-se à sua fábrica de dados **Autor & Monitor** UI. No separador **Monitor,** vê uma lista de execução de pipeline, clique no link de nome do **pipeline** para aceder à lista de funcionações de atividade na execução do gasoduto.

![Executar a atividade de cópia de monitor](./media/copy-activity-overview/monitor-pipeline-run.png)

A este nível, pode ver links para copiar entrada de atividade, saída e erros (se a execução da atividade do Copy falhar), bem como estatísticas como duração/estado. Clicar no botão **Detalhes** (óculos) ao lado do nome da atividade da cópia irá dar-lhe detalhes profundos sobre a execução da sua atividade de cópia. 

![Executar a atividade de cópia de monitor](./media/copy-activity-overview/monitor-copy-activity-run.png)

Nesta vista de monitorização gráfica, a Azure Data Factory apresenta-lhe as informações de execução da atividade de cópia, incluindo o volume de leitura/escrita de dados, o número de ficheiros/linhas de dados copiados de origem a afundar, a produção, as configurações aplicadas para o seu cenário de cópia, passos que a atividade da cópia passa com as durações e detalhes correspondentes, e muito mais. Consulte [esta tabela](#monitor-programmatically) sobre cada métrica possível e a sua descrição detalhada. 

Em alguns cenários, quando executa uma atividade de Cópia na Fábrica de Dados, verá "Dicas de **afinação** de desempenho" no topo da visão de monitorização da atividade de cópia, como mostra o exemplo. As dicas dizem-lhe o estrangulamento identificado pela ADF para a execução de cópia específica, juntamente com a sugestão sobre o que mudar para aumentar a entrada de cópia. Saiba mais sobre as dicas de [afinação](copy-activity-performance-troubleshooting.md#performance-tuning-tips)do desempenho automático.

Os **detalhes e durações de execução inferiores** descrevem os passos-chave que a sua atividade de cópia passa, o que é especialmente útil para resolver o desempenho da cópia. O estrangulamento da sua execução de cópia é aquele com maior duração. Consulte o desempenho da atividade da [cópia troubleshoot](copy-activity-performance-troubleshooting.md) sobre o que cada etapa representa e a orientação detalhada para resolução de problemas.

**Exemplo: Cópia da Amazon S3 para Azure Data Lake Storage Gen2**

![Monitorizar detalhes de execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorize programáticamente

Os detalhes de execução da atividade de cópia e as características de desempenho também são devolvidos na secção de saída do resultado > da**execução** **da Atividade de Cópia,** que é usada para renderizar a visão de monitorização da UI. Segue-se uma lista completa de propriedades que podem ser devolvidas. Verá apenas as propriedades aplicáveis ao seu cenário de cópia. Para obter informações sobre como monitorizar a atividade funciona programáticamente em geral, consulte [Programáticamente monitorizar uma fábrica](monitor-programmatically.md)de dados Azure .

| Nome da propriedade  | Descrição | Unidade na saída |
|:--- |:--- |:--- |
| dadosLer | A quantidade real de dados lidos pela fonte. | Valor Int64, em bytes |
| dadosEscrito | O monte real de dados escritos/comprometidos com a pia. O tamanho pode `dataRead` ser diferente do tamanho, uma vez que diz respeito à forma como cada loja de dados armazena os dados. | Valor Int64, em bytes |
| arquivosLer | O número de ficheiros lidos pela fonte baseada em ficheiros. | Valor Int64 (sem unidade) |
| ficheirosEscrito | O número de ficheiros escritos/comprometidos com a pia baseada em ficheiros. | Valor Int64 (sem unidade) |
| sourcePeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados de origem durante a execução da atividade do Copy. | Valor Int64 (sem unidade) |
| sinkPeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados do lavatório durante a execução da atividade copy. | Valor Int64 (sem unidade) |
| linhasLer | Número de linhas lidas a partir da fonte (não aplicável para cópia binária). | Valor Int64 (sem unidade) |
| linhasCosCos | Número de linhas copiadas para afundar (não aplicável para cópia binária). | Valor Int64 (sem unidade) |
| linhas Skipped | Número de filas incompatíveis que foram ignoradas. Pode permitir que linhas incompatíveis sejam `enableSkipIncompatibleRow` ignoradas, fixando-se como verdadeiras. | Valor Int64 (sem unidade) |
| cópiaDura | Duração da execução da cópia. | Valor int32, em segundos |
| de transferência de dados | Taxa de transferência de dados. | Número de ponto flutuante, em KBps |
| sourcePeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados de origem durante a execução da atividade do Copy. | Valor Int32 (sem unidade) |
| sinkPeakConnections| Número máximo de ligações simultâneas estabelecidas na loja de dados do lavatório durante a execução da atividade copy.| Valor Int32 (sem unidade) |
| sqlDwPolyBase | Se a PolyBase é utilizada quando os dados são copiados para o Armazém de Dados SQL. | Booleano |
| redshiftDescarregar | Se o UNLOAD é utilizado quando os dados são copiados da Redshift. | Booleano |
| hdfsDistcp | Se o DistCp é utilizado quando os dados são copiados do HDFS. | Booleano |
| eficazIntegraçãoTempo | O tempo de execução de integração (IR) ou `<IR name> (<region if it's Azure IR>)`os tempos de execução utilizados para alimentar o execução da atividade, no formato . | Texto (corda) |
| unidades de integração de datas usados | As unidades eficazes de integração de dados durante a cópia. | Valor Int32 |
| cópias paralelas usadas | O paralelo eficazCópias durante a cópia. | Valor Int32 |
| redirecionamentoRowPath | Caminho para o registo de linhas incompatíveis ignoradas no armazenamento `redirectIncompatibleRowSettings` blob que configura na propriedade. Ver [Tolerância à falha](copy-activity-overview.md#fault-tolerance). | Texto (corda) |
| execuçãoDetalhes | Mais detalhes sobre os estágios que a atividade da Cópia passa e os passos correspondentes, durações, configurações, e assim por diante. Não recomendamos que analise esta secção porque pode mudar. Para entender melhor como o ajuda a compreender e a resolver o desempenho da cópia, consulte a secção [Monitor visualmente.](#monitor-visually) | Matriz |
| perfRecomendação | Copiar dicas de afinação de desempenho. Consulte as dicas de [afinação](copy-activity-performance-troubleshooting.md#performance-tuning-tips) do desempenho para obter mais detalhes. | Matriz |

**Exemplo:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos da Atividade cópia:

\- [Descrição geral da atividade de cópia](copy-activity-overview.md)

\-[Copiar desempenho da atividade](copy-activity-performance.md)