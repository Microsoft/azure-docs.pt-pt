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
ms.date: 06/08/2020
ms.author: jingwang
ms.openlocfilehash: 4e7828810a069756d1a0cde55ab47915ad11acc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249708"
---
# <a name="monitor-copy-activity"></a>Monitorizar a atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como monitorizar a execução da atividade de cópia na Azure Data Factory. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="monitor-visually"></a>Monitorizar visualmente

Uma vez criado e publicado um oleoduto na Azure Data Factory, pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Pode monitorizar todo o seu oleoduto funciona de forma nativa na experiência do utilizador da Azure Data Factory. Saiba mais sobre a monitorização da Azure Data Factory em geral a partir do [Monitor Visual Azure Data Factory](monitor-visually.md).

Para monitorizar a atividade copy, vá à sua fábrica de dados **Autor & Monitor** UI. No **separador Monitor,** vê uma lista de correções de gasodutos, clique no link de nome do **gasoduto** para aceder à lista de atividades executadas no curso do gasoduto.

![Monitorizar a atividade da cópia](./media/copy-activity-overview/monitor-pipeline-run.png)

A este nível, pode ver links para copiar entrada, saída e erros da atividade (se a atividade copy funcionar falhar), bem como estatísticas como duração/estado. Clicar no botão **Detalhes** (óculos) ao lado do nome da atividade da cópia irá dar-lhe detalhes profundos sobre a execução da sua atividade de cópia. 

![Monitorizar a atividade da cópia](./media/copy-activity-overview/monitor-copy-activity-run.png)

Nesta visão de monitorização gráfica, a Azure Data Factory apresenta-lhe as informações de execução da atividade de cópia, incluindo o volume de leitura/escrita de dados, o número de ficheiros/linhas de dados copiados de fonte para afundar, o rendimento, as configurações aplicadas para o seu cenário de cópia, passos que a atividade da cópia passa com as respetivas durações e detalhes, e muito mais. Consulte [esta tabela](#monitor-programmatically) sobre cada métrica possível e a sua descrição detalhada. 

Em alguns cenários, quando executar uma atividade copy na Data Factory, verá "Dicas de **afinação** de desempenho" no topo da vista de monitorização da atividade da cópia, como mostra o exemplo. As dicas dizem-lhe o estrangulamento identificado pela ADF para a execução de cópias específicas, juntamente com uma sugestão sobre o que mudar para aumentar o rendimento da cópia. Saiba mais sobre [as dicas de afinação de desempenho automático](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Os **detalhes e durações** da execução inferior descrevem os passos-chave que a sua atividade de cópia passa, o que é especialmente útil para resolver problemas no desempenho da cópia. O estrangulamento da sua cópia é o que tem a maior duração. Consulte o [desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md) sobre o que cada fase representa e a orientação detalhada de resolução de problemas.

**Exemplo: Cópia da Amazon S3 para Azure Data Lake Storage Gen2**

![Monitorize detalhes da atividade da cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorar programáticamente

Os detalhes da execução da atividade de cópia e as características de desempenho também são devolvidos na secção **de resultado de execução**da Copy  >  **Output** Activity, que é usada para tornar a visão de monitorização da UI. Segue-se uma lista completa de propriedades que podem ser devolvidas. Verá apenas as propriedades que são aplicáveis ao seu cenário de cópia. Para obter informações sobre como monitorizar a atividade funciona programáticamente em geral, consulte [programáticamente monitorizar uma fábrica de dados Azure](monitor-programmatically.md).

| Nome da propriedade  | Descrição | Unidade de saída |
|:--- |:--- |:--- |
| dadosRead | A quantidade real de dados lidos a partir da fonte. | Valor int64, em bytes |
| dadosCritos | O monte real de dados escritos/comprometidos com a pia. O tamanho pode ser diferente do `dataRead` tamanho, uma vez que relaciona a forma como cada data armazena os dados. | Valor int64, em bytes |
| ficheirosRead | O número de ficheiros lidos a partir da fonte baseada em ficheiros. | Valor int64 (sem unidade) |
| ficheirosCrito | O número de ficheiros escritos/comprometidos com a pia baseada em ficheiros. | Valor int64 (sem unidade) |
| sourcePeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados de origem durante a atividade Copy. | Valor int64 (sem unidade) |
| sinkPeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados do lavatório durante a execução da atividade Copy. | Valor int64 (sem unidade) |
| linhasLei | Número de filas lidas na fonte. Esta métrica não se aplica ao copiar ficheiros como-é sem analisá-los, por exemplo, quando os conjuntos de dados de origem e pia são do tipo de formato binário, ou outro tipo de formato com configurações idênticas. | Valor int64 (sem unidade) |
| linhasCopiado | Número de filas copiadas para afundar. Esta métrica não se aplica ao copiar ficheiros como-é sem analisá-los, por exemplo, quando os conjuntos de dados de origem e pia são do tipo de formato binário, ou outro tipo de formato com configurações idênticas.  | Valor int64 (sem unidade) |
| rowsSkipped | Número de filas incompatíveis que foram ignoradas. Pode permitir que linhas incompatíveis sejam ignoradas definindo `enableSkipIncompatibleRow` para ser verdadeira. | Valor int64 (sem unidade) |
| copyDuration | Duração da cópia executada. | Valor int32, em segundos |
| de transferência de dados | Taxa de transferência de dados. | Número do ponto flutuante, em KBps |
| sourcePeakConnections | Número máximo de ligações simultâneas estabelecidas na loja de dados de origem durante a atividade Copy. | Valor int32 (sem unidade) |
| sinkPeakConnections| Número máximo de ligações simultâneas estabelecidas na loja de dados do lavatório durante a execução da atividade Copy.| Valor int32 (sem unidade) |
| sqlDwPolyBase | Se a PolyBase é utilizada quando os dados são copiados para o SQL Data Warehouse. | Booleano |
| redshiftUnload | Se o UNLOAD é utilizado quando os dados são copiados do Redshift. | Booleano |
| hdfsDistcp | Se o DistCp é utilizado quando os dados são copiados do HDFS. | Booleano |
| efetivamenteIntegrationRuntime | O tempo de integração (IR) ou os tempos de funcionação utilizados para alimentar a atividade funcionam, no formato `<IR name> (<region if it's Azure IR>)` . | Texto (cadeia) |
| utilizadasDataIntegrationUnnits | As unidades eficazes de integração de dados durante a cópia. | Valor int32 |
| usadoParallelCopies | Os paralelos eficazesCopias durante a cópia. | Valor int32 |
| redirectRowPath | Caminho para o log de linhas incompatíveis saltadas no armazenamento de bolhas que configura na `redirectIncompatibleRowSettings` propriedade. Ver [tolerância à falha](copy-activity-overview.md#fault-tolerance). | Texto (cadeia) |
| execuçãoDetails | Mais detalhes sobre as fases que a atividade copy passa e os passos correspondentes, durações, configurações, e assim por diante. Não recomendamos que analise esta secção porque pode mudar. Para melhor entender como o ajuda a compreender e a resolver problemas de desempenho da cópia, consulte a secção [Monitor visualmente.](#monitor-visually) | Matriz |
| perfRecommendation | Copiar dicas de afinação de desempenho. Consulte [as dicas de afinação de desempenho](copy-activity-performance-troubleshooting.md#performance-tuning-tips) para obter detalhes. | Matriz |

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

## <a name="next-steps"></a>Próximos passos
Consulte os outros artigos de Atividade de Cópia:

\-[Visão geral da atividade da cópia](copy-activity-overview.md)

\- [Desempenho da atividade de cópia](copy-activity-performance.md)