---
title: Atividade de cópia na Fábrica de Dados Azure
description: Conheça a atividade da Cópia na Azure Data Factory. Pode usá-lo para copiar dados de uma loja de dados de origem suportada para uma loja de dados de sink suportada.
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
ms.openlocfilehash: 616cdc0387d5c5cf3c2980ae1cfbc10e3c1119f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261362"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia na Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão da Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

Na Azure Data Factory, pode utilizar a atividade Copy para copiar dados entre lojas de dados localizadas no local e na nuvem. Depois de copiar os dados, pode usar outras atividades para transformá-los e analisá-los. Também pode utilizar a atividade copy para publicar resultados de transformação e análise para inteligência empresarial (BI) e consumo de aplicação.

![O papel da atividade copy](media/copy-activity-overview/copy-activity.png)

A atividade copy é executada em um tempo de [execução de integração](concepts-integration-runtime.md). Pode utilizar diferentes tipos de tempos de integração para diferentes cenários de cópia de dados:

* Quando estiver a copiar dados entre duas lojas de dados que são acessíveis ao público através da internet a partir de qualquer IP, pode utilizar o tempo de execução da integração Azure para a atividade de cópia. Este tempo de integração é seguro, fiável, escalável e [globalmente disponível.](concepts-integration-runtime.md#integration-runtime-location)
* Quando está a copiar dados de e para lojas de dados que estão localizados no local ou numa rede com controlo de acesso (por exemplo, uma rede virtual Azure), é necessário configurar um tempo de execução de integração auto-hospedado.

Um tempo de execução de integração tem de ser associado a cada fonte e afunda uma loja de dados. Para obter informações sobre como a atividade do Copy determina qual o tempo de funcionamento da integração a utilizar, consulte [a Determinação do IR a utilizar](concepts-integration-runtime.md#determining-which-ir-to-use).

Para copiar dados de uma fonte para um lavatório, o serviço que executa a atividade copy executa estes passos:

1. Lê os dados de um arquivo de dados de origem.
2. Realiza serialização/desserialização, compressão/descompressão, mapeamento de colunas, e assim por diante. Executa estas operações com base na configuração do conjunto de dados de entrada, conjunto de dados de saída e atividade de Cópia.
3. Escreve dados para o arquivo de dados de sink/destino.

![Descrição geral da atividade de cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formatos e arquivos de dados suportados

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Pode utilizar a atividade Copy para copiar ficheiros como está entre duas lojas de dados baseadas em ficheiros, caso em que os dados são copiados de forma eficiente sem qualquer serialização ou desserialização. Além disso, também pode analisar ou gerar ficheiros de um determinado formato, por exemplo, pode executar o seguinte:

* Copie os dados de uma base de dados do SQL Server no local e escreva para O Armazenamento de Lago de Dados Azure Gen2 em formato Parquet.
* Copiar ficheiros em formato de texto (CSV) a partir de um sistema de ficheiros no local e escrever para o armazenamento Azure Blob em formato Avro.
* Copie ficheiros com fecho de um sistema de ficheiros no local, descomprima-os no voo e escreva ficheiros extraídos para o Azure Data Lake Storage Gen2.
* Copie os dados no formato gzip de texto comprimido (CSV) do armazenamento da Blob Azure e escreva-os na Base de Dados Azure SQL.
* Muitas mais atividades que requerem serialização/desserialização ou compressão/descompressão.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que permite a atividade do Copy está disponível globalmente nas regiões e geografias listadas em locais de execução de [integração Azure.](concepts-integration-runtime.md#integration-runtime-location) A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Consulte [produtos por região](https://azure.microsoft.com/regions/#services) para verificar a disponibilidade da Data Factory e o movimento de dados numa região específica.

## <a name="configuration"></a>Configuração

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Em geral, para utilizar a atividade copy na Azure Data Factory, é necessário:

1. **Crie serviços ligados para a loja de dados de origem e para a loja de dados do lavatório.** Pode encontrar a lista de conectores suportados nas lojas de dados suportadas e na secção de [formatos](#supported-data-stores-and-formats) deste artigo. Consulte a secção "Propriedades de serviço ligadas" do artigo conector para obter informações de configuração e propriedades suportadas. 
2. **Crie conjuntos de dados para a fonte e afunde.** Consulte as secções "Dataset properties" da fonte e afunda artigos de conector para informações de configuração e propriedades suportadas.
3. **Crie um oleoduto com a atividade copy.** A secção seguinte fornece um exemplo.

### <a name="syntax"></a>Sintaxe

O seguinte modelo de uma atividade de Cópia contém uma lista completa de propriedades suportadas. Especifique os que se adequam ao seu cenário.

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
| tipo | Para uma atividade de Cópia, definida para `Copy` | Sim |
| inputs | Especifique o conjunto de dados que criou que aponta para os dados de origem. A atividade copy suporta apenas uma única entrada. | Sim |
| outputs | Especifique o conjunto de dados que criou que aponta para os dados do lavatório. A atividade copy suporta apenas uma única saída. | Sim |
| typeProperties | Especifique propriedades para configurar a atividade da Cópia. | Sim |
| source | Especifique o tipo de origem da cópia e as propriedades correspondentes para a recuperação de dados.<br/>Para mais informações, consulte a secção "Copiar propriedades de atividade" no artigo do conector listado em [lojas e formatos](#supported-data-stores-and-formats)de dados suportados . | Sim |
| sink | Especifique o tipo de sumidouro de cópia e as propriedades correspondentes para a escrita de dados.<br/>Para mais informações, consulte a secção "Copiar propriedades de atividade" no artigo do conector listado em [lojas e formatos](#supported-data-stores-and-formats)de dados suportados . | Sim |
| translator | Especifica mapeamentos de colunas explícita de origem para o sink. Esta propriedade aplica-se quando o comportamento predefinido da cópia não satisfaz as suas necessidades.<br/>Para mais informações, consulte [o mapeamento de Schema na atividade](copy-activity-schema-and-type-mapping.md)de cópia. | Não |
| dataIntegrationUnits | Especifique uma medida que represente a quantidade de energia que o tempo de execução de [integração azure](concepts-integration-runtime.md) utiliza para cópia de dados. Estas unidades eram anteriormente conhecidas como Unidades de Movimento de Dados em nuvem (DMU). <br/>Para mais informações, consulte Unidades de [Integração de Dados](copy-activity-performance.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que pretende que a atividade da Cópia utilize ao ler dados da fonte e escrever dados para o lavatório.<br/>Para mais informações, consulte [a cópia paralela](copy-activity-performance.md#parallel-copy). | Não |
| preservar | Especifique se deve preservar metadados/ACLs durante a cópia de dados. <br/>Para mais informações, consulte [Preservar metadados](copy-activity-preserve-metadata.md). |Não |
| enableStaging<br/>stagingSettings | Especifique se deve ser a deestágio dos dados provisórios no armazenamento blob em vez de copiar diretamente os dados de origem para afundar.<br/>Para obter informações sobre cenários úteis e detalhes de configuração, consulte [a cópia encenada](copy-activity-performance.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como lidar com linhas incompatíveis quando copia dados de origem para afundar.<br/>Para mais informações, consulte a [tolerância à falha.](copy-activity-fault-tolerance.md) | Não |

## <a name="monitoring"></a>Monitorização

Pode monitorizar a atividade do Copy executada na Azure Data Factory tanto visual como programáticamente. Para mais detalhes, consulte a [atividade da cópia do Monitor](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Cópia incremental

Data Factory permite-lhe copiar gradualmente dados delta de uma loja de dados de origem para uma loja de dados afundada. Para mais detalhes, consulte [Tutorial: Copiar incrementalmente os dados](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização

A experiência de [monitorização da atividade](copy-activity-monitoring.md) de cópia mostra-lhe as estatísticas de desempenho da cópia para cada uma das suas atividades. O guia de [desempenho e escalabilidade](copy-activity-performance.md) da atividade da Cópia descreve factores-chave que afetam o desempenho do movimento de dados através da atividade copy na Fábrica de Dados Azure. Também enumera os valores de desempenho observados durante os testes e discute como otimizar o desempenho da atividade da Cópia.

## <a name="resume-from-last-failed-run"></a>Retomar da última corrida falhada

A atividade de cópia suporta o currículo da última execução falhada quando copia o tamanho grande dos ficheiros como está com o formato binário entre lojas baseadas em ficheiros e opta por preservar a hierarquia de pasta/ficheiro de origem a afundar, por exemplo, migrar dados da Amazon S3 para o Azure Data Lake Storage Gen2. Aplica-se aos seguintes conectores baseados em ficheiros: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md)e [SFTP](connector-sftp.md).

Pode aproveitar o currículo da atividade da cópia nas seguintes duas formas:

- **Retry nível de atividade:** Pode definir a contagem de retry na atividade de cópia. Durante a execução do gasoduto, se esta execução de atividade de cópia falhar, a próxima retentativa automática começará a partir do ponto de avaria do último ensaio.
- **Reexecutar da atividade falhada:** Após a conclusão da execução do gasoduto, também pode desencadear uma recandidatura da atividade falhada na vista de monitorização da UI ADF ou programáticamente. Se a atividade falhada for uma atividade de cópia, o gasoduto não só reexecutará desta atividade, como também será retomado a partir do ponto de avaria da execução anterior.

    ![Currículo de cópia](media/copy-activity-overview/resume-copy.png)

Poucos pontos a notar:

- O currículo acontece ao nível dos ficheiros. Se a atividade de cópia falhar ao copiar um ficheiro, na próxima execução, este ficheiro específico será recopiado.
- Para que o reinício funcione corretamente, não altere as definições de atividade da cópia entre as repetições.
- Quando copia dados da Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 e Google Cloud Storage, a atividade de cópia pode ser retomada a partir do número arbitrário de ficheiros copiados. Enquanto para o resto dos conectores baseados em ficheiros como fonte, atualmente os suportes de atividade copiam currículos a partir de um número limitado de ficheiros, geralmente ao alcance de dezenas de milhares e varia em função do comprimento dos caminhos dos ficheiros; os ficheiros para além deste número serão recopiados durante as repetições.

Para outros cenários que não a cópia de ficheiros binários, a reexecução da atividade da cópia começa desde o início.

## <a name="preserve-metadata-along-with-data"></a>Preservar metadados juntamente com dados

Ao copiar dados de origem para afundar, em cenários como a migração de data lake, também pode optar por preservar os metadados e OSAc juntamente com os dados que utilizam a atividade de cópia. Consulte os [metadados da Preserve](copy-activity-preserve-metadata.md) para obter mais detalhes.

## <a name="schema-and-data-type-mapping"></a>Esquema e o mapeamento de tipo de dados

Consulte [o Schema e](copy-activity-schema-and-type-mapping.md) o mapeamento do tipo de dados para obter informações sobre como a atividade da Cópia mapeia os seus dados de origem para a sua pia.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade copy sopre cede a cópia de dados e devolve uma falha quando as linhas de dados de origem são incompatíveis com as linhas de dados do lavatório. Para que a cópia tenha sucesso, pode configurar a atividade copy para saltar e registar as linhas incompatíveis e copiar apenas os dados compatíveis. Consulte a tolerância à [falha da atividade da Cópia](copy-activity-fault-tolerance.md) para detalhes.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes inícios rápidos, tutoriais e amostras:

- [Copiar dados de um local para outro local na mesma conta de armazenamento do Azure Blob](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do armazenamento da Blob Azure para a Base de Dados Azure SQL](tutorial-copy-data-dot-net.md)
- [Copiar dados de uma base de dados do SQL Server no local para o Azure](tutorial-hybrid-copy-powershell.md)
