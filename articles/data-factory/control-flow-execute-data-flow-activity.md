---
title: Atividade do Fluxo de Dados
description: Como executar fluxos de dados de dentro de um oleoduto de fábrica de dados.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/16/2021
ms.openlocfilehash: da8d193d140d96d9742666429ebc85672c71ad4e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567269"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Atividade do Fluxo de Dados na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a atividade do Fluxo de Dados para transformar e mover dados através de fluxos de dados de mapeamento. Se é novo nos fluxos de dados, consulte [a visão geral do Fluxo de Dados de Mapeamento](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

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
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
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

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
fluxo de dados | A referência ao Fluxo de Dados a ser executado | DataFlowReference | Yes
integraçãoRuntime | O ambiente computacional em que o fluxo de dados funciona. Se não for especificado, será utilizado o tempo de funcionamento da integração Azure de resolução automática. | IntegraçãoReferiaruntimeReference | No
compute.coreCount | O número de núcleos usados no aglomerado de faíscas. Só pode ser especificado se for utilizado o tempo de funcionamento da integração Azure de resolução automática | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType | O tipo de computação usada no aglomerado de faíscas. Só pode ser especificado se for utilizado o tempo de funcionamento da integração Azure de resolução automática | "Geral", "ComputeOptimizado", "MemoryOptimed" | No
staging.linkedService | Se estiver a utilizar uma fonte ou pia Azure Synapse Analytics, especifique a conta de armazenamento utilizada para a paragem da PolyBase.<br/><br/>Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Aprenda também as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.<br/> | LinkedServiceReference | Só se o fluxo de dados ler ou escrever para um Azure Synapse Analytics
staging.folderPath | Se estiver a utilizar uma fonte ou pia Azure Synapse Analytics, o caminho da pasta na conta de armazenamento de bolhas utilizada para a paragem da PolyBase | String | Só se o fluxo de dados ler ou escrever para a Azure Synapse Analytics
traceLevel | Definir o nível de registo da execução da atividade do fluxo de dados | Tudo bem, grosseiro, nenhum | No

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "Executar fluxo de dados")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Cálculo do fluxo de dados de tamanho dinâmico no tempo de execução

As propriedades Core Count e Compute Type podem ser definidas de forma dinâmica para se ajustarem ao tamanho dos dados de origem de entrada no tempo de execução. Utilize atividades de pipeline como o Lookup ou Get Metadados para encontrar o tamanho dos dados do conjunto de dados de origem. Em seguida, utilize o Conteúdo Dinâmico do Add nas propriedades da atividade do Fluxo de Dados.

![Fluxo dinâmico de dados](media/data-flow/dyna1.png "Fluxo dinâmico de dados")

[Aqui está um breve tutorial de vídeo explicando esta técnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Tempo de execução da integração do Fluxo de Dados

Escolha qual o tempo de execução de integração a utilizar para a execução da sua atividade de Fluxo de Dados. Por predefinição, a Data Factory utilizará o tempo de funcionamento da Integração Azure de resolução automática com quatro núcleos de trabalhadores. Este IR tem um tipo de computação de propósito geral e funciona na mesma região que a sua fábrica. Para os oleodutos operacionalizados, recomenda-se vivamente que crie os seus próprios Tempos de Execução de Integração Azure que definem regiões específicas, tipo de cálculo, contagem de núcleos e TTL para a execução da sua atividade de fluxo de dados.

Um tipo mínimo de cálculo de Finalidade Geral (otimizado em computação não é recomendado para grandes cargas de trabalho) com uma configuração 8+8 (16 núcleos v totais) e uma recomendação mínima de 10 minutos para a maioria das cargas de trabalho de produção. Ao definir um pequeno TTL, o Azure IR pode manter um aglomerado quente que não incorrerá nos vários minutos de início para um aglomerado frio. Pode acelerar ainda mais a execução dos seus fluxos de dados selecionando "Reutilização rápida" nas configurações do fluxo de dados Azure IR. Para mais informações, consulte [o tempo de execução da integração do Azure.](concepts-integration-runtime.md)

![Tempo de execução da integração Azure](media/data-flow/ir-new.png "Tempo de execução da integração Azure")

> [!IMPORTANT]
> A seleção de tempo de execução de integração na atividade do Fluxo de Dados aplica-se apenas às *execuções desencadeadas* do seu pipeline. Depurar o seu oleoduto com fluxos de dados corre no cluster especificado na sessão de depuração.

### <a name="polybase"></a>PolyBase

Se estiver a utilizar um Azure Synapse Analytics como pia ou fonte, deve escolher um local de preparação para a carga do seu lote PolyBase. A PolyBase permite o carregamento de lotes a granel em vez de carregar os dados linha a linha. A PolyBase reduz drasticamente o tempo de carga para a Azure Synapse Analytics.

## <a name="logging-level"></a>Nível de registo

Se não necessitar de todas as execuções do pipeline das suas atividades de fluxo de dados para registar totalmente todos os registos de telemetria verbose, pode configurar opcionalmente o seu nível de registo para "Básico" ou "Nenhum". Ao executar os fluxos de dados no modo "Verbose" (padrão), está a solicitar à ADF que faça login totalmente a cada nível de partição durante a transformação dos dados. Esta pode ser uma operação dispendiosa, por isso apenas permitir verbose quando a resolução de problemas pode melhorar o seu fluxo global de dados e desempenho do pipeline. O modo "Básico" só registará durações de transformação enquanto "Nenhum" fornecerá apenas um resumo das durações.

![Nível de registo](media/data-flow/logging.png "Definir nível de registo")

## <a name="sink-properties"></a>Propriedades de pia

A funcionalidade de agrupamento nos fluxos de dados permite-lhe definir a ordem de execução dos seus lavatórios, bem como agrupar pias usando o mesmo número de grupo. Para ajudar a gerir grupos, pode pedir à ADF para executar pias, no mesmo grupo, em paralelo. Também pode definir o grupo de pia para continuar mesmo depois de um dos lavatórios encontrar um erro.

O comportamento padrão dos sumidouros de fluxo de dados é executar cada pia sequencialmente, de forma em série, e falhar o fluxo de dados quando um erro é encontrado na pia. Além disso, todos os lavatórios estão em incumprimento do mesmo grupo, a menos que você entre nas propriedades de fluxo de dados e desecorde diferentes prioridades para os lavatórios.

![Propriedades de pia](media/data-flow/sink-properties.png "Definir propriedades de pia")

## <a name="parameterizing-data-flows"></a>Parametrizar fluxos de dados

### <a name="parameterized-datasets"></a>Conjuntos de dados parametrizados

Se o fluxo de dados utilizar conjuntos de dados parametrizados, defina os valores dos parâmetros no **separador Definições.**

![Executar parâmetros de fluxo de dados](media/data-flow/params.png "Parâmetros")

### <a name="parameterized-data-flows"></a>Fluxos de dados parametrizados

Se o fluxo de dados for parametrizado, desaprote os valores dinâmicos dos parâmetros de fluxo de dados no **separador Parâmetros.** Pode utilizar a linguagem de expressão do gasoduto ADF ou a linguagem de expressão de fluxo de dados para atribuir valores de parâmetros dinâmicos ou literais. Para obter mais informações, consulte [os parâmetros do fluxo de dados.](parameters-data-flow.md)

### <a name="parameterized-compute-properties"></a>Propriedades computacional parametrizadas.

Pode parametrizar a contagem de núcleo ou o tipo de cálculo se utilizar o tempo de execução da Integração Azure de resolução automática e especificar valores para compute.coreCount e compute.computeType.

![Executar exemplo de parâmetro de fluxo de dados](media/data-flow/parameterize-compute.png "Exemplo de parâmetro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Depuragem de gasodutos da atividade do Fluxo de Dados

Para executar um gasoduto de depuração executado com uma atividade de Fluxo de Dados, tem de ligar o modo de depuração do fluxo de dados através do slider **de depurador de fluxo de dados** na barra superior. O modo Debug permite-lhe executar o fluxo de dados contra um cluster de Faíscas ativo. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

![Screenshot que mostra onde está o botão Debug](media/data-flow/debug-button-3.png)

O gasoduto de depuração corre contra o cluster de depuração ativo, não o ambiente de tempo de integração especificado nas definições de atividade do Fluxo de Dados. Pode escolher o ambiente de depuramento ao iniciar o modo de depurar.

## <a name="monitoring-the-data-flow-activity"></a>Monitorização da atividade do Fluxo de Dados

A atividade do Data Flow tem uma experiência de monitorização especial onde pode ver a partição, o tempo de estágio e a informação da linhagem de dados. Abra o painel de monitorização através do ícone de óculos em **Ações**. Para obter mais informações, consulte [Monitoring Data Flows](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>A atividade do Fluxo de Dados resulta numa atividade subsequente

A atividade de fluxo de dados produz métricas relativas ao número de linhas escritas a cada pia e linhas lidas a partir de cada fonte. Estes resultados são devolvidos na `output` secção do resultado da execução da atividade. As métricas devolvidas estão no formato do json abaixo.

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

Por exemplo, para chegar ao número de linhas escritas a um lavatório denominado "sink1" numa atividade denominada "dataflowActivity", `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` utilize.

Para obter o número de linhas lidas de uma fonte chamada "fonte1" que foi usada naquela pia, `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` use.

> [!NOTE]
> Se uma pia tiver zero linhas escritas, não aparecerá em métricas. A existência pode ser verificada utilizando a `contains` função. Por exemplo, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificará se alguma linha foi escrita para afundar1.

## <a name="next-steps"></a>Passos seguintes

Ver atividades de fluxo de controlo suportadas pela Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Executar atividade de gasoduto](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)
