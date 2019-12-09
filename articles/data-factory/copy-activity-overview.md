---
title: Atividade de cópia no Azure Data Factory
description: Saiba mais sobre a atividade de cópia no Azure Data Factory. Você pode usá-lo para copiar dados de um armazenamento de dados de origem com suporte para um armazenamento de dados de coletor com suporte.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 40bddaab6db5e7ed777ec55ca469a9e2d1c35c98
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927553"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

No Azure Data Factory, você pode usar a atividade de cópia para copiar dados entre repositórios de dados localizados localmente e na nuvem. Depois de copiar os dados, você pode usar outras atividades para transformá-los e analisá-los ainda mais. Você também pode usar a atividade de cópia para publicar os resultados da transformação e da análise para business intelligence (BI) e o consumo de aplicativos.

![A função da atividade de cópia](media/copy-activity-overview/copy-activity.png)

A atividade de cópia é executada em um [tempo de execução de integração](concepts-integration-runtime.md). Você pode usar diferentes tipos de tempos de execução de integração para diferentes cenários de cópia de dados:

* Ao copiar dados entre dois armazenamentos de dados publicamente acessíveis pela Internet de qualquer IP, você pode usar o tempo de execução de integração do Azure para a atividade de cópia. Esse tempo de execução de integração é seguro, confiável, escalonável e [globalmente disponível](concepts-integration-runtime.md#integration-runtime-location).
* Quando você está copiando dados de e para armazenamentos de dados que estão localizados localmente ou em uma rede com controle de acesso (por exemplo, uma rede virtual do Azure), você precisa configurar um tempo de execução de integração auto-hospedado.

Um tempo de execução de integração precisa ser associado a cada armazenamento de dados de origem e de coletor. Para obter informações sobre como a atividade de cópia determina qual tempo de execução de integração usar, consulte [determinando qual ir usar](concepts-integration-runtime.md#determining-which-ir-to-use).

Para copiar dados de uma origem para um coletor, o serviço que executa a atividade de cópia executa estas etapas:

1. Lê os dados de um arquivo de dados de origem.
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e assim por diante. Ele executa essas operações com base na configuração do conjunto de dados de entrada, no conjunto de informações de saída e na atividade de cópia.
3. Escreve dados para o arquivo de dados de sink/destino.

![Descrição geral da atividade de cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formatos e arquivos de dados suportados

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados

Você pode usar a atividade de cópia para copiar arquivos como estão entre dois armazenamentos de dados baseados em arquivo. Nesse caso, os dados são copiados com eficiência sem nenhuma serialização ou desserialização.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Por exemplo, você pode executar as seguintes atividades de cópia:

* Copie dados de um banco de dado SQL Server local e grave os dados em Azure Data Lake Storage Gen2 no formato parquet.
* Copiar arquivos no formato de texto (CSV) de um sistema de arquivos local e gravar no armazenamento de BLOBs do Azure no formato Avro.
* Copie arquivos compactados de um sistema de arquivos local, descompacte-os e grave-os em Azure Data Lake Storage Gen2.
* Copie dados no formato de texto compactado gzip (CSV) do armazenamento de BLOBs do Azure e grave-os no banco de dados SQL do Azure.
* Muitas outras atividades que exigem serialização/desserialização ou compactação/descompactação.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que habilita a atividade de cópia está disponível globalmente nas regiões e geografias listados em [locais de tempo de execução de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Consulte [produtos por região](https://azure.microsoft.com/regions/#services) para verificar a disponibilidade de data Factory e a movimentação de dados em uma região específica.

## <a name="configuration"></a>Configuração

Para usar a atividade de cópia no Azure Data Factory, você precisa:

1. **Crie serviços vinculados para o armazenamento de dados de origem e o armazenamento de dados do coletor.** Consulte a seção "Propriedades do serviço vinculado" do artigo do conector para obter informações de configuração e propriedades com suporte. Você pode encontrar a lista de conectores com suporte na seção [armazenamentos de dados e formatos com suporte](#supported-data-stores-and-formats) deste artigo.
2. **Crie conjuntos de valores para a origem e o coletor.** Consulte as seções "Propriedades do conjunto de dados" dos artigos do conector de origem e do coletor para obter informações de configuração e propriedades com suporte.
3. **Crie um pipeline com a atividade de cópia.** A secção seguinte fornece um exemplo.

### <a name="syntax"></a>Sintaxe

O modelo a seguir de uma atividade de cópia contém uma lista completa de propriedades com suporte. Especifique os que se adequam ao seu cenário.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Necessário? |
|:--- |:--- |:--- |
| tipo | Para uma atividade de cópia, defina como `Copy` | Sim |
| inputs | Especifique o conjunto de dados que você criou que aponta para a origem. A atividade de cópia dá suporte a apenas uma única entrada. | Sim |
| outputs | Especifique o conjunto de dados que você criou que aponta para o coletor. A atividade de cópia dá suporte a apenas uma única saída. | Sim |
| typeProperties | Especifique as propriedades para configurar a atividade de cópia. | Sim |
| source | Especifique o tipo de origem da cópia e as propriedades correspondentes para recuperar dados.<br/><br/>Para obter mais informações, consulte a seção "Propriedades da atividade de cópia" no artigo do conector listado em [formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| sink | Especifique o tipo de coletor de cópia e as propriedades correspondentes para gravar dados.<br/><br/>Para obter mais informações, consulte a seção "Propriedades da atividade de cópia" no artigo do conector listado em [formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| translator | Especifica mapeamentos de colunas explícita de origem para o sink. Essa propriedade se aplica quando o comportamento de cópia padrão não atende às suas necessidades.<br/><br/>Para obter mais informações, consulte [mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md). | Não |
| dataIntegrationUnits | Especifique uma medida que represente a quantidade de energia que o [tempo de execução de integração do Azure](concepts-integration-runtime.md) usa para a cópia de dados. Essas unidades eram anteriormente conhecidas como DMU (unidades de movimentação de dados de nuvem). <br/><br/>Para obter mais informações, consulte [unidades de integração de dados](copy-activity-performance.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que você deseja que a atividade de cópia use ao ler dados da origem e gravar dados no coletor.<br/><br/>Para obter mais informações, consulte [cópia paralela](copy-activity-performance.md#parallel-copy). | Não |
| enableStaging<br/>stagingSettings | Especifique se deseja preparar os dados provisórios no armazenamento de BLOBs em vez de copiar dados diretamente da origem para o coletor.<br/><br/>Para obter informações sobre cenários úteis e detalhes de configuração, consulte [cópia em etapas](copy-activity-performance.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como manipular linhas incompatíveis ao copiar dados da origem para o coletor.<br/><br/>Para obter mais informações, consulte [tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitorização

Você pode monitorar a execução da atividade de cópia na interface do usuário do monitor de Azure Data Factory **autor &** ou programaticamente.

### <a name="monitor-visually"></a>Monitorizar visualmente

Para monitorar visualmente a execução da atividade de cópia, vá para o data factory e vá para **criar & monitor**. Na guia **Monitor** , você vê uma lista de execuções de pipeline com um botão **Exibir atividade executar** na coluna **ações** :

![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Selecione **Exibir execuções de atividade** para ver a lista de atividades na execução do pipeline. Na coluna **ações** , você vê links para a entrada da atividade de cópia, saída, erros (se a execução da atividade de cópia falhar) e detalhes:

![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Selecione o botão **detalhes** na coluna **ações** para ver os detalhes de execução da atividade de cópia e as características de desempenho. Você vê informações como volume/número de linhas/número de arquivos de dados copiados da origem para o coletor, taxa de transferência, etapas que a atividade de cópia passa com as durações correspondentes e as configurações usadas para seu cenário de cópia.

>[!TIP]
>Em alguns cenários, você também verá **dicas de ajuste de desempenho** na parte superior da página de monitoramento de cópia. Essas dicas informam sobre afunilamentos identificados e fornecem informações sobre o que mudar para impulsionar a produtividade da cópia. Para obter um exemplo, consulte a seção [desempenho e ajuste](#performance-and-tuning) deste artigo.

**Exemplo: copiar do Amazon S3 para Azure Data Lake Store**
![monitorar detalhes da execução da atividade](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemplo: copiar do banco de dados SQL do Azure para o Azure SQL data warehouse com cópia em etapas**
![monitorar detalhes da execução da atividade](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorizar de forma programática

Os detalhes de execução da atividade de cópia e as características de desempenho também são retornados na seção **resultado da execução da atividade de cópia** > **saída** . A seguir está uma lista completa de propriedades que podem ser retornadas. Você verá apenas as propriedades que são aplicáveis ao seu cenário de cópia. Para obter informações sobre como monitorar as execuções de atividade, consulte [monitorar uma execução de pipeline](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome da propriedade  | Descrição | Unidade |
|:--- |:--- |:--- |
| dataRead | Quantidade de dados lidos da origem. | Valor Int64, em bytes |
| dataWritten | Quantidade de dados gravados no coletor. | Valor Int64, em bytes |
| filesRead | Número de arquivos copiados durante a cópia do armazenamento de arquivos. | Valor Int64 (nenhuma unidade) |
| filesWritten | Número de arquivos copiados durante a cópia para o armazenamento de arquivos. | Valor Int64 (nenhuma unidade) |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados de origem durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| sinkPeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| rowsRead | Número de linhas lidas da origem (não aplicável à cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsCopied | Número de linhas copiadas para o coletor (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que foram ignoradas. Você pode habilitar linhas incompatíveis para serem ignoradas definindo `enableSkipIncompatibleRow` como true. | Valor Int64 (nenhuma unidade) |
| copyDuration | Duração da execução da cópia. | Valor Int32, em segundos |
| Taxa de transferência | Taxa de transferência de dados. | Número de ponto flutuante, em KBps |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados de origem durante a execução da atividade de cópia. | Valor Int32 (nenhuma unidade) |
| sinkPeakConnections| Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a execução da atividade de cópia.| Valor Int32 (nenhuma unidade) |
| sqlDwPolyBase | Se o polybase é usado quando os dados são copiados para SQL Data Warehouse. | Booleano |
| redshiftUnload | Se o UNLOAD é usado quando os dados são copiados do redshift. | Booleano |
| hdfsDistcp | Se DistCp é usado quando os dados são copiados do HDFS. | Booleano |
| effectiveIntegrationRuntime | O tempo de execução de integração (IR) ou tempos de execução usados para ativar a atividade de execução, no formato `<IR name> (<region if it's Azure IR>)`. | Texto (cadeia) |
| usedDataIntegrationUnits | As unidades de integração de dados em vigor durante a cópia. | Valor Int32 |
| usedParallelCopies | O parallelCopies em vigor durante a cópia. | Valor Int32 |
| redirectRowPath | Caminho para o log de linhas incompatíveis ignoradas no armazenamento de BLOBs que você configurou na propriedade `redirectIncompatibleRowSettings`. Consulte [tolerância a falhas](#fault-tolerance) posteriormente neste artigo. | Texto (cadeia) |
| executionDetails | Mais detalhes sobre os estágios pelos quais a atividade de cópia passa e as etapas correspondentes, durações, configurações e assim por diante. Não recomendamos que você analise esta seção porque ela pode ser alterada.<br/><br/>Data Factory também relata as durações detalhadas (em segundos) gastas em vários estágios em `detailedDurations`. As durações dessas etapas são exclusivas. Somente as durações que se aplicam à execução da atividade de cópia fornecida são exibidas:<br/>**Duração do enfileiramento** (`queuingDuration`): a quantidade de tempo antes que a atividade de cópia realmente seja iniciada no Integration Runtime. Se você usar um IR auto-hospedado e esse valor for grande, verifique a capacidade e o uso do IR e aumente ou reduza de acordo com sua carga de trabalho. <br/>**Duração da cópia prévia do script** (`preCopyScriptDuration`): o tempo decorrido entre quando a atividade de cópia começa no ir e quando a atividade de cópia termina de executar o script de pré-cópia no repositório de dados do coletor. Aplica-se quando você configura o script de pré-cópia. <br/>**Tempo até o primeiro byte** (`timeToFirstByte`): o tempo decorrido entre o fim da etapa anterior e a hora em que o ir recebe o primeiro byte do armazenamento de dados de origem. Aplica-se a fontes não baseadas em arquivo. Se esse valor for grande, verifique e otimize a consulta ou o servidor.<br/>**Duração da transferência** (`transferDuration`): o tempo decorrido entre o fim da etapa anterior e a hora em que o ir transfere todos os dados da origem para o coletor. | Array |
| perfRecommendation | Copiar dicas de ajuste de desempenho. Consulte [desempenho e ajuste](#performance-and-tuning) para obter detalhes. | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Esquema e o mapeamento de tipo de dados

Consulte [mapeamento de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para obter informações sobre como a atividade de cópia mapeia os dados de origem para o coletor.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade de cópia interrompe a cópia de dados e retorna uma falha quando as linhas de dados de origem são incompatíveis com as linhas de dados do coletor. Para que a cópia seja realizada com sucesso, você pode configurar a atividade de cópia para ignorar e registrar em log as linhas incompatíveis e copiar apenas os dados compatíveis. Consulte [tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md) para obter detalhes.

## <a name="performance-and-tuning"></a>Desempenho e otimização

O [guia desempenho e escalabilidade da atividade de cópia](copy-activity-performance.md) descreve os principais fatores que afetam o desempenho da movimentação de dados por meio da atividade de cópia no Azure data Factory. Ele também lista os valores de desempenho observados durante o teste e discute como otimizar o desempenho da atividade de cópia.

Em alguns cenários, ao executar uma atividade de cópia no Data Factory, você verá **dicas de ajuste de desempenho** na parte superior da [página de monitoramento da atividade de cópia](#monitor-visually), conforme mostrado no exemplo a seguir. As dicas informam o afunilamento identificado para a execução de cópia fornecida. Eles também fornecem informações sobre o que mudar para impulsionar a produtividade da cópia. As dicas de ajuste de desempenho atualmente fornecem sugestões como o uso do polybase ao copiar dados para o Azure SQL Data Warehouse, aumentando Azure Cosmos DB de RUs ou do banco de dados SQL do Azure quando o recurso no lado do armazenamento é o afunilamento e removendo cópias de preparo desnecessárias.

**Exemplo: copiar para o banco de dados SQL do Azure, com uma dica de ajuste de desempenho**

Neste exemplo, durante uma execução de cópia, Data Factory rastreia uma alta utilização de DTU no banco de dados SQL do coletor do Azure. Essa condição diminui a velocidade das operações de gravação. A sugestão é aumentar as DTUs na camada do banco de dados SQL do Azure:

![Copiar monitoramento com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Cópia incremental
Data Factory permite copiar incrementalmente dados Delta de um armazenamento de dados de origem para um armazenamento de dados de coletor. Para obter detalhes, consulte [tutorial: copiar dados incrementalmente](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes inícios rápidos, tutoriais e amostras:

- [Copiar dados de um local para outro na mesma conta de armazenamento de BLOBs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](tutorial-copy-data-dot-net.md)
- [Copiar dados de um SQL Server local para o Azure](tutorial-hybrid-copy-powershell.md)
