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
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 28d8c077f106f12812f7ed710217febd24d81efc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267162"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia numa fábrica de dados do Azure

## <a name="overview"></a>Descrição geral

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

No Azure Data Factory, pode utilizar a atividade de cópia para copiar dados entre dados arquivos localizados no local e na cloud. Depois dos dados são copiados, ele pode ser ainda mais transformado e analisado. Também pode utilizar a atividade de cópia para publicar os resultados da análise de business intelligence (BI) e consumo de aplicativo e de transformação.

![Função de atividade de cópia](media/copy-activity-overview/copy-activity.png)

Atividade de cópia é executada num [Integration Runtime](concepts-integration-runtime.md). Para o cenário de cópia de dados diferentes, pode ser aproveitado sabor diferente do Integration Runtime:

* Quando copiar dados entre dados armazena que ambos estão acessíveis publicamente, atividade de cópia pode ser capacitada por **Runtime de integração do Azure**, que é seguro, fiável e dimensionável, e [globalmente disponível](concepts-integration-runtime.md#integration-runtime-location).
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

Atividade de cópia também suporta a leitura e gravação de arquivos em formatos especificados: **Texto, JSON, Avro, ORC e no Parquet**, compressão e da decompresing arquivos e com os codecs seguintes: **GZip, Deflate, BZip2 e ZipDeflate**. Ver [formatos de ficheiro e de compressão suportados](supported-file-formats-and-compression-codecs.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copiar dados no SQL Server no local e escrever para a geração 2 de armazenamento do Azure Data Lake em formato Parquet.
* Copiar arquivos no formato de texto (CSV) de sistema de ficheiros no local e escrita em BLOBs do Azure no formato Avro.
* Copiar arquivos compactados de sistema de ficheiros no local e, em seguida, descomprimir ' s land para geração 2 de armazenamento do Azure Data Lake.
* Copiar dados em formato compactado texto (CSV) de GZip do Blob do Azure e escrever para a base de dados do Azure SQL.
* E muitos mais casos com a serialização/desserialização ou compactação/descompactação precisam.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que alimenta atividade de cópia está disponível globalmente nas regiões e localizações geográficas listados na [localizações de Runtime de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Ver [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade do Data Factory e o movimento de dados numa região.

## <a name="configuration"></a>Configuração

Para utilizar a atividade de cópia no Azure Data Factory, tem de:

1. **Crie serviços ligados para o arquivo de dados de origem e de arquivo de dados de sink.** Consulte "Propriedades do serviço ligado" secção o artigo de conector sobre como configurar e as propriedades suportadas. Pode encontrar a lista de conector suportados no [arquivos de dados e formatos suportados](#supported-data-stores-and-formats) secção.
2. **Crie conjuntos de dados de origem e sink.** Consulte a origem e sink secção de "Propriedades do conjunto de dados" dos artigos do conector sobre como configurar e as propriedades suportadas.
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
| tipo | A propriedade de tipo de uma atividade de cópia tem de ser definida: **cópia** | Sim |
| entradas | Especifique o conjunto de dados que criou que aponta para a origem de dados. Atividade de cópia suporta apenas uma única entrada. | Sim |
| saídas | Especifique o conjunto de dados que criou que aponta para os dados de sink. Atividade de cópia suporta apenas uma única saída. | Sim |
| typeProperties | Um grupo de propriedades para configurar a atividade de cópia. | Sim |
| source | Especifique o tipo de origem de cópia e as propriedades correspondentes sobre como obter dados.<br/><br/>Conheça os detalhes da secção "Propriedades da atividade copiar" no artigo de conector listado na [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| sink | Especifique o tipo de sink de cópia e as propriedades correspondentes sobre como escrever dados.<br/><br/>Conheça os detalhes da secção "Propriedades da atividade copiar" no artigo de conector listado na [arquivos de dados e formatos suportados](#supported-data-stores-and-formats). | Sim |
| Microsoft Translator | Especifica mapeamentos de colunas explícita de origem para o sink. Aplica-se de que quando o comportamento de cópia de padrão não é possível satisfazer suas necessidades.<br/><br/>Conheça os detalhes da [mapeamento do tipo de esquema e os dados](copy-activity-schema-and-type-mapping.md). | Não |
| dataIntegrationUnits | Especifique o powerfulness de [Runtime de integração do Azure](concepts-integration-runtime.md) para capacitar a cópia de dados. Anteriormente conhecido como na cloud a unidades de movimento de dados (DMU). <br/><br/>Conheça os detalhes da [unidades de integração de dados](copy-activity-performance.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que pretende que a atividade de cópia para utilizar durante a leitura de dados de origem e de escrita de dados de sink.<br/><br/>Conheça os detalhes da [cópia em paralelo](copy-activity-performance.md#parallel-copy). | Não |
| enableStaging<br/>stagingSettings | Opte por colocar os dados provisórias no armazenamento de BLOBs aa, em vez de diretamente copiar dados de origem para o sink.<br/><br/>Conheça os cenários útil e detalhes de configuração do [cópia faseada](copy-activity-performance.md#staged-copy). | Não |
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
>Em alguns cenários, também verá "**sugestões de otimização do desempenho**" sobre a cópia de monitorização de página, que indica o afunilamento identificado e orienta-o sobre o que alterar modo a aumentar o débito de cópia, veja o exemplo com detalhes [aqui](#performance-and-tuning).

**Exemplo: copiar do Amazon S3 para o Azure Data Lake Store**
![detalhes da execução da atividade do Monitor](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemplo: cópia faseada de cópia da base de dados do Azure SQL para utilizar o Azure SQL Data Warehouse**
![detalhes da execução da atividade do Monitor](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorizar de forma programática

Detalhes de execução da atividade de cópia e características de desempenho também são retornadas em resultado da execução de atividade de cópia -> secção de saída. Segue-se uma lista exaustiva; apenas os que aplicável ao seu cenário de cópia serão exibido. Saiba como monitorizar a execução a partir da atividade [guia de introdução secção monitorização](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome da propriedade  | Descrição | Unidade |
|:--- |:--- |:--- |
| DataRead | Tamanho dos dados de leitura de origem | Valor Int64 **bytes** |
| DataWritten | Tamanho dos dados escrito para o sink | Valor Int64 **bytes** |
| filesRead | Número de ficheiros que está a ser copiados quando se copiam dados a partir do armazenamento de ficheiros. | Valor Int64 (nenhuma unidade) |
| fileScanned | Número de ficheiros que está a ser analisados a partir do armazenamento de ficheiros de origem. | Valor Int64 (nenhuma unidade) |
| filesWritten | Número de ficheiros que está a ser copiado ao copiar dados para armazenamento de ficheiros. | Valor Int64 (nenhuma unidade) |
| rowsCopied | Número de linhas a ser copiados (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que está a ser ignorada. Pode ativar a funcionalidade por conjunto "enableSkipIncompatibleRow" como true. | Valor Int64 (nenhuma unidade) |
| Taxa de transferência | Taxa a que os dados são transferidos | No número de vírgula flutuante **KB/s** |
| copyDuration | A duração da cópia | Valor de Int32 em segundos |
| sqlDwPolyBase | Se o PolyBase é utilizado quando se copiam dados para o SQL Data Warehouse. | Booleano |
| redshiftUnload | Se descarregar é utilizado quando se copiam dados a partir do Redshift. | Booleano |
| hdfsDistcp | Se DistCp é utilizado quando se copiam dados do HDFS. | Booleano |
| effectiveIntegrationRuntime | Mostrar que Runtime(s) de integração é utilizada para capacitar a atividade executar, no formato de `<IR name> (<region if it's Azure IR>)`. | Texto (cadeia) |
| usedDataIntegrationUnits | As unidades de integração de dados em vigor durante a cópia. | Valor Int32 |
| usedParallelCopies | O parallelCopies em vigor durante a cópia. | Valor Int32|
| redirectRowPath | Caminho para o registo de linhas incompatíveis ignorados no armazenamento de BLOBs é configurar em "redirectIncompatibleRowSettings". Veja o exemplo abaixo. | Texto (cadeia) |
| executionDetails | Obter mais detalhes sobre as fases de atividade de cópia atravessa, e correspondentes passos, duração, configurações utilizadas, etc. Não é recomendado para analisar nesta secção como eles podem mudar. | Array |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedDataIntegrationUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedDataIntegrationUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Esquema e o mapeamento de tipo de dados

Consulte a [mapeamento do tipo de esquema e os dados](copy-activity-schema-and-type-mapping.md), que descreve como a atividade de cópia mapeia os dados de origem para o sink.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por predefinição, a atividade de cópia parar a cópia de dados e retorna falha quando encontrar dados incompatíveis entre a origem e sink. Pode configurar explicitamente para ignorar e registar linhas incompatíveis e apenas copiar esses dados compatíveis para fazer a cópia foi concluída com êxito. Consulte a [tolerância a falhas de atividade de cópia](copy-activity-fault-tolerance.md) em mais detalhes.

## <a name="performance-and-tuning"></a>Desempenho e otimização

Consulte a [guia de sintonização de desempenho de atividade de cópia e](copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory. Também o desempenho observado durante o teste interno de lista e descreve várias formas de otimizar o desempenho de atividade de cópia.

Em alguns casos, quando executar uma atividade de cópia no ADF, diretamente verá "**sugestões de otimização de desempenho**" na parte superior do [página de monitorização de atividade de cópia](#monitor-visually) conforme mostrado no exemplo a seguir. Ele não apenas diz a o afunilamento identificado para a execução de cópia determinada, mas também o orienta sobre o que alterar modo a aumentar o débito de cópia. O desempenho de sugestões de otimização atualmente fornecer sugestões, como a utilizar quando se copiam dados para o Azure SQL Data Warehouse, o PolyBase para aumentar o Azure Cosmos DB RU ou do Azure SQL DB DTU quando o recurso em dados armazenar lado é o afunilamento, para remover desnecessários de teste cópia, etc. O desempenho, otimização de regras de gradualmente irá ser enriquecido também.

**Exemplo: cópia para o Azure SQL DB com dicas de ajuste de desempenho**

Neste exemplo, durante a cópia em execução, o BD SQL do Azure de sink atinge a alta utilização de DTU que pode atrasar as operações de escrita, de aviso ADF, portanto, a sugestão é aumentar o escalão de BD SQL do Azure com mais de DTU. 

![Copie a monitorização com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Cópia incremental 
O Data Factory suporta cenários de forma incremental copiar os dados delta de um arquivo de dados de origem para um arquivo de dados de destino. Ver [Tutorial: copiar dados de forma incremental](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Ler e escrever dados particionados
Na versão 1, o Azure Data Factory suportadas ler ou escrever dados particionados utilizando variáveis de sistema do SliceStart/SliceEnd/WindowStart/WindowEnd. Na versão atual, pode conseguir este comportamento ao utilizar um parâmetro de pipeline e agendada/hora a hora de início do acionador como um valor do parâmetro. Para obter mais informações, consulte [como ler ou escrever particionados dados](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes inícios rápidos, tutoriais e amostras:

- [Copiar dados de uma localização para outra localização no mesmo armazenamento de Blobs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL](tutorial-copy-data-dot-net.md)
- [Copiar dados do SQL Server no local para o Azure](tutorial-hybrid-copy-powershell.md)
