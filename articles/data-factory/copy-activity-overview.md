---
title: Atividade de cópia na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba mais sobre a atividade de cópia na fábrica de dados do Azure que pode utilizar para copiar dados de um arquivo de dados de origem suportada para um arquivo de dados de sink suportados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a8265496c475566ec7a87a19eab6d975838e9da4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966393"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia numa fábrica de dados do Azure

## <a name="overview"></a>Descrição geral

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

No Azure Data Factory, pode utilizar a atividade de cópia para copiar dados entre dados arquivos localizados no local e na cloud. Depois que os dados são copiados, eles podem ser mais transformados e analisados usando outras atividades. Também pode utilizar a atividade de cópia para publicar os resultados da análise de business intelligence (BI) e consumo de aplicativo e de transformação.

![Função de atividade de cópia](media/copy-activity-overview/copy-activity.png)

Atividade de cópia é executada num [Integration Runtime](concepts-integration-runtime.md). Para diferentes cenários de cópia de dados, diferentes tipos de Integration Runtime podem ser aproveitados:

* Ao copiar dados entre armazenamentos de dados que podem ser acessados publicamente por meio da Internet de qualquer IPs, a atividade de cópia pode ser habilitada pelo **Azure Integration Runtime**, que é seguro, confiável, escalonável e [disponível globalmente](concepts-integration-runtime.md#integration-runtime-location).
* Quando copiar dados de/para arquivos de dados localizados no local ou numa rede com o controlo de acesso (por exemplo, rede Virtual do Azure), terá de configurar uma **integrada de Runtime autoalojado** para capacitar a cópia de dados.

Runtime de integração tem de estar associado a cada arquivo de dados de origem e sink. Conheça os detalhes sobre como atividade de cópia [determina o runtime de integração para utilizar](concepts-integration-runtime.md#determining-which-ir-to-use).

Atividade de cópia executa as seguintes fases para copiar dados de uma origem para um sink. O serviço que alimenta atividade de cópia:

1. Lê os dados de um arquivo de dados de origem.
2. Executa a serialização/desserialização, compactação/descompactação, mapeamento de colunas, etc. Ele faz essas operações com base nas configurações do conjunto de dados de entrada, o conjunto de dados de saída e a atividade de cópia.
3. Escreve dados para o arquivo de dados de sink/destino.

![Descrição Geral da Atividade de Cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formatos e arquivos de dados suportados

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados

Pode usar a atividade de cópia para **copiar ficheiros como-é** entre dois arquivos de dados de ficheiros, na qual os caso os dados é copiado com eficiência sem qualquer serialização/desserialização.

A atividade de cópia também dá suporte à leitura e gravação em arquivos em formatos especificados: **Text, JSON, Avro, Orc e parquet**, e compactando e descompactando arquivos com os seguintes codecs: **Gzip, deflate, BZIP2 e ZipDeflate**. Ver [formatos de ficheiro e de compressão suportados](supported-file-formats-and-compression-codecs.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copie dados no local SQL Server e grave em Azure Data Lake Storage Gen2 no formato parquet.
* Copiar arquivos no formato de texto (CSV) de sistema de ficheiros no local e escrita em BLOBs do Azure no formato Avro.
* Copie arquivos compactados do sistema de arquivos local e descompacte, em seguida, para Azure Data Lake Storage Gen2.
* Copiar dados em formato compactado texto (CSV) de GZip do Blob do Azure e escrever para a base de dados do Azure SQL.
* E muitos outros casos com necessidade de serialização/desserialização ou compactação/descompactação.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que alimenta atividade de cópia está disponível globalmente nas regiões e localizações geográficas listados na [localizações de Runtime de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Ver [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade do Data Factory e o movimento de dados numa região.

## <a name="configuration"></a>Configuração

Para utilizar a atividade de cópia no Azure Data Factory, tem de:

1. **Crie serviços ligados para o arquivo de dados de origem e de arquivo de dados de sink.** Consulte "Propriedades do serviço ligado" secção o artigo de conector sobre como configurar e as propriedades suportadas. Pode encontrar a lista de conector suportados no [arquivos de dados e formatos suportados](#supported-data-stores-and-formats) secção.
2. **Crie conjuntos de dados de origem e sink.** Consulte a seção "Propriedades do conjunto de notícias" do conector de origem e do coletor sobre como configurar e suas propriedades com suporte.
3. **Crie um pipeline com atividade de cópia.** A secção seguinte fornece um exemplo.

### <a name="syntax"></a>Sintaxe

O modelo seguinte de uma atividade de cópia contém uma lista completa de propriedades suportadas. Especifique os que se adequam ao seu cenário.

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

### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type de uma atividade de cópia deve ser definida como: **Copy** | Sim |
| inputs | Especifique o conjunto de dados que criou que aponta para a origem de dados. Atividade de cópia suporta apenas uma única entrada. | Sim |
| outputs | Especifique o conjunto de dados que criou que aponta para os dados de sink. Atividade de cópia suporta apenas uma única saída. | Sim |
| typeProperties | Um grupo de propriedades para configurar a atividade de cópia. | Sim |
| source | Especifique o tipo de origem de cópia e as propriedades correspondentes sobre como obter dados.<br/><br/>Conheça os detalhes da secção "Propriedades da atividade copiar" no artigo de conector listado na [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| sink | Especifique o tipo de sink de cópia e as propriedades correspondentes sobre como escrever dados.<br/><br/>Conheça os detalhes da secção "Propriedades da atividade copiar" no artigo de conector listado na [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| translator | Especifica mapeamentos de colunas explícita de origem para o sink. Aplica-se de que quando o comportamento de cópia de padrão não é possível satisfazer suas necessidades.<br/><br/>Conheça os detalhes da [mapeamento do tipo de esquema e os dados](copy-activity-schema-and-type-mapping.md). | Não |
| dataIntegrationUnits | Especifique o powerfulness de [Runtime de integração do Azure](concepts-integration-runtime.md) para capacitar a cópia de dados. Anteriormente conhecido como na cloud a unidades de movimento de dados (DMU). <br/><br/>Conheça os detalhes da [unidades de integração de dados](copy-activity-performance.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que pretende que a atividade de cópia para utilizar durante a leitura de dados de origem e de escrita de dados de sink.<br/><br/>Conheça os detalhes da [cópia em paralelo](copy-activity-performance.md#parallel-copy). | Não |
| enableStaging<br/>stagingSettings | Opte por preparar os dados provisórios em um armazenamento de BLOB em vez de copiar dados diretamente da origem para o coletor.<br/><br/>Conheça os cenários útil e detalhes de configuração do [cópia faseada](copy-activity-performance.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como pretende lidar com linhas incompatíveis durante a cópia de dados de origem para o sink.<br/><br/>Conheça os detalhes da [tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitorização

Pode monitorizar a atividade de cópia em execução no Azure Data Factory "Criar e monitorizar" IU ou programaticamente. Em seguida, pode comparar o desempenho e a configuração do seu cenário para a atividade de cópia [referência de desempenho](copy-activity-performance.md#performance-reference) de testes internos.

### <a name="monitor-visually"></a>Monitorizar visualmente

Para monitorizar visualmente a execução de atividade de cópia, vá para a fábrica de dados -> **criar e monitorizar** -> **separador Monitor**, pode ver uma lista de pipeline é executado com uma ligação "Ver execuções de atividades" no  **Ações** coluna.

![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Clique para ver a lista de atividades nesta execução de pipeline. Na **ações** coluna, tem ligações para a entrada da atividade de cópia, saída, erros (se falhar a execução de atividade de cópia) e detalhes.

![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Clique em de "**detalhes**" ligação sob **ações** para ver os detalhes da execução da atividade de cópia e características de desempenho. Mostra-lhe informações incluindo volume/linhas/ficheiros de dados copiados da origem para o sink, débito, passos, ele passa por com duração correspondente e usado configurações para o seu cenário de cópia.

>[!TIP]
>Para alguns cenários, você também verá "**dicas de ajuste de desempenho**" na parte superior da página de monitoramento de cópia, que informa o afunilamento identificado e orienta você sobre o que mudar para aumentar a produtividade da cópia, consulte um exemplo com detalhes [aqui](#performance-and-tuning).

**Exemplo: copiar do Amazon S3 para o Azure Data Lake Store**
![detalhes da execução da atividade do Monitor](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemplo: cópia faseada de cópia da base de dados do Azure SQL para utilizar o Azure SQL Data Warehouse**
![detalhes da execução da atividade do Monitor](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorizar de forma programática

Os detalhes de execução da atividade de cópia e as características de desempenho também são retornados na seção resultado da execução da atividade de cópia-> saída. Segue-se uma lista exaustiva; apenas os que aplicável ao seu cenário de cópia serão exibido. Saiba como monitorizar a execução a partir da atividade [guia de introdução secção monitorização](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome da propriedade  | Descrição | Unidade |
|:--- |:--- |:--- |
| dataRead | Tamanho dos dados de leitura de origem | Valor Int64 **bytes** |
| dataWritten | Tamanho dos dados escrito para o sink | Valor Int64 **bytes** |
| filesRead | Número de ficheiros que está a ser copiados quando se copiam dados a partir do armazenamento de ficheiros. | Valor Int64 (nenhuma unidade) |
| filesWritten | Número de ficheiros que está a ser copiado ao copiar dados para armazenamento de ficheiros. | Valor Int64 (nenhuma unidade) |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o repositório de dados de origem durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| sinkPeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| rowsRead | Número de linhas sendo lidas da origem (não aplicável à cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsCopied | Número de linhas sendo copiadas para o coletor (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que está a ser ignorada. Pode ativar a funcionalidade por conjunto "enableSkipIncompatibleRow" como true. | Valor Int64 (nenhuma unidade) |
| copyDuration | A duração da cópia. | Valor de Int32 em segundos |
| throughput | Taxa na qual os dados são transferidos. | No número de vírgula flutuante **KB/s** |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados de origem durante a cópia. | Valor Int32 |
| sinkPeakConnections| Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a cópia.| Valor Int32 |
| sqlDwPolyBase | Se o PolyBase é utilizado quando se copiam dados para o SQL Data Warehouse. | Booleano |
| redshiftUnload | Se descarregar é utilizado quando se copiam dados a partir do Redshift. | Booleano |
| hdfsDistcp | Se DistCp é utilizado quando se copiam dados do HDFS. | Booleano |
| effectiveIntegrationRuntime | Mostrar que Runtime(s) de integração é utilizada para capacitar a atividade executar, no formato de `<IR name> (<region if it's Azure IR>)`. | Texto (cadeia) |
| usedDataIntegrationUnits | As unidades de integração de dados em vigor durante a cópia. | Valor Int32 |
| usedParallelCopies | O parallelCopies em vigor durante a cópia. | Valor Int32 |
| redirectRowPath | Caminho para o registo de linhas incompatíveis ignorados no armazenamento de BLOBs é configurar em "redirectIncompatibleRowSettings". Veja o exemplo abaixo. | Texto (cadeia) |
| executionDetails | Obter mais detalhes sobre as fases de atividade de cópia atravessa, e correspondentes passos, duração, configurações utilizadas, etc. Não é recomendado para analisar nesta secção como eles podem mudar.<br/><br/>O ADF também relata as durações detalhadas (em segundos) gastas nas respectivas etapas `detailedDurations`em. As durações dessas etapas são exclusivas e somente as que se aplicam à execução da atividade de cópia fornecida serão mostradas:<br/>- **Duração** do enfileiramento (`queuingDuration`): O tempo decorrido até que a atividade de cópia realmente seja iniciada no Integration Runtime. Se você usar o IR auto-hospedado e esse valor for grande, sugira para verificar a capacidade e o uso do IR e escalar verticalmente de acordo com sua carga de trabalho. <br/>- **Duração da cópia prévia do script** (`preCopyScriptDuration`): O tempo decorrido entre a atividade de cópia a partir do IR e a atividade de cópia concluindo a execução do script de pré-cópia no repositório de dados do coletor. Aplique quando você configurar o script de pré-cópia. <br/>- **Tempo até o primeiro byte** (`timeToFirstByte`): O tempo decorrido entre o fim da etapa anterior e o IR recebendo o primeiro byte do armazenamento de dados de origem. Aplicar a uma fonte não baseada em arquivo. Se esse valor for grande, sugira para verificar e otimizar a consulta ou o servidor.<br/>- **Duração da transferência** (`transferDuration`): O tempo decorrido entre o fim da etapa anterior e o IR transferindo todos os dados da origem para o coletor. | Array |
| perfRecommendation | Copiar dicas de ajuste de desempenho. Consulte a seção [desempenho e ajuste](#performance-and-tuning) em detalhes. | Array |

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

Consulte a [mapeamento do tipo de esquema e os dados](copy-activity-schema-and-type-mapping.md), que descreve como a atividade de cópia mapeia os dados de origem para o sink.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade de cópia interrompe a cópia de dados e retorna uma falha quando encontra dados incompatíveis entre a origem e o coletor. Pode configurar explicitamente para ignorar e registar linhas incompatíveis e apenas copiar esses dados compatíveis para fazer a cópia foi concluída com êxito. Consulte a [tolerância a falhas de atividade de cópia](copy-activity-fault-tolerance.md) em mais detalhes.

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte a [guia de sintonização de desempenho de atividade de cópia e](copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory. Também o desempenho observado durante o teste interno de lista e descreve várias formas de otimizar o desempenho de atividade de cópia.

Em alguns casos, ao executar uma atividade de cópia no ADF, você verá diretamente "**dicas de ajuste de desempenho**" sobre a página de monitoramento da atividade de [cópia](#monitor-visually) , conforme mostrado no exemplo a seguir. Ele não apenas informa o afunilamento identificado para a execução de cópia fornecida, mas também orienta você sobre o que mudar para aumentar a produtividade da cópia. As dicas de ajuste de desempenho atualmente fornecem sugestões como usar o polybase ao copiar dados para o Azure SQL Data Warehouse, para aumentar a Azure Cosmos DB de DTU ou BD SQL do Azure quando o recurso no lado do repositório de dados é o afunilamento, para remover o preparado desnecessário copiar, etc. As regras de ajuste de desempenho também serão aprimoradas gradualmente.

**Exemplo: copiar para o banco de BD SQL do Azure com dicas de ajuste de desempenho**

Neste exemplo, durante a execução da cópia, o ADF observa que o banco de informações do SQL Azure do coletor atinge uma alta utilização de DTU que reduz as operações de gravação, de modo que a sugestão é aumentar a camada do BD SQL do Azure com mais DTU.

![Copiar monitoramento com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Cópia incremental
O Data Factory dá suporte a cenários de cópia incremental de dados Delta de um armazenamento de dados de origem para um armazenamento de dados de coletor. Ver [Tutorial: copiar dados de forma incremental](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Ler e escrever dados particionados
Na versão 1, o Azure Data Factory suportadas ler ou escrever dados particionados utilizando variáveis de sistema do SliceStart/SliceEnd/WindowStart/WindowEnd. Na versão atual, pode conseguir este comportamento ao utilizar um parâmetro de pipeline e agendada/hora a hora de início do acionador como um valor do parâmetro. Para obter mais informações, consulte [como ler ou escrever particionados dados](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes inícios rápidos, tutoriais e amostras:

- [Copiar dados de uma localização para outra localização no mesmo armazenamento de Blobs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL](tutorial-copy-data-dot-net.md)
- [Copiar dados do SQL Server no local para o Azure](tutorial-hybrid-copy-powershell.md)
