---
title: Formato parquet no Azure Data Factory | Documentos da Microsoft
description: Este tópico descreve como lidar com o formato Parquet no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65144887"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory

Siga este artigo, quando deseja **analisar os ficheiros Parquet ou escrever os dados em formato Parquet**. 

Formato parquet é suportado para os conectores seguintes: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOBs do Azure](connector-azure-blob-storage.md), [do Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [File Storage do Azure](connector-azure-file-storage.md), [Sistema de ficheiros](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados Parquet.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade de tipo do conjunto de dados tem de ser definida **Parquet**. | Sim      |
| localização         | Definições de localização dos ficheiros pelo. Cada conector baseado em arquivo tem seu próprio tipo de localização e suportada propriedades em `location`. **Ver os detalhes no artigo de conector -> secção de propriedades do conjunto de dados**. | Sim      |
| compressionCodec | O codec de compressão para utilizar a escrita em ficheiros Parquet. Durante a leitura de ficheiros Parquet, o Data Factory determinar automaticamente o codec de compressão com base nos metadados.<br>Tipos suportados são "**none**","**gzip**","**snappy**" (predefinição), e "**lzo**". Tenha em atenção atualmente não suporta a atividade de cópia LZO. | Não       |

> [!NOTE]
> Espaço em branco no nome da coluna não é suportado para ficheiros Parquet.

Segue-se um exemplo de Parquet conjunto de dados no armazenamento de Blobs do Azure:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de Parquet e o sink.

### <a name="parquet-as-source"></a>Parquet como origem

As seguintes propriedades são suportadas na atividade de cópia ***\*origem\**** secção.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade de tipo de origem de atividade de cópia tem de ser definida **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados a partir de um arquivo de dados. Cada conector baseado em arquivo tem suas próprias definições de leitura suportadas em `storeSettings`. **Ver os detalhes no artigo de conector -> secção de propriedades de atividade de cópia**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como sink

As seguintes propriedades são suportadas na atividade de cópia ***\*sink\**** secção.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade de tipo de origem de atividade de cópia tem de ser definida **ParquetSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como escrever dados para um arquivo de dados. Cada conector baseado em arquivo tem suas próprias definições de escrita suportadas em `storeSettings`. **Ver os detalhes no artigo de conector -> secção de propriedades de atividade de cópia**. | Não       |

## <a name="mapping-data-flow-properties"></a>Propriedades de fluxo de dados de mapeamento

Conheça os detalhes da [transformação de origem](data-flow-source.md) e [sink transformação](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="data-type-support"></a>Tipo de dados de suporte

Parquet tipos de dados complexos não estão atualmente suportados (por exemplo, mapa, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando o Integration Runtime autoalojado

> [!IMPORTANT]
> Para copiar capacitados pelo Runtime de integração autoalojado por exemplo, entre no local e na cloud armazena os dados, se não estiver a copiar ficheiros Parquet **como-é**, tem de instalar o **64-bit JRE 8 (Java Runtime Environment) ou OpenJDK** no seu computador do Runtime de integração. Veja o parágrafo a seguir com mais detalhes.

Para obter uma cópia em execução no runtime de integração autoalojado com ficheiros de Parquet serialização/desserialização, o ADF localiza o tempo de execução Java verificando o registo em primeiro lugar *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, se não for encontrado, a verificação em segundo lugar de variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para utilizar o JRE**: O runtime de integração de 64 bits requer um JRE de 64 bits. Pode encontrá-lo partir [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para utilizar OpenJDK**: é suportado desde a versão 3.13 de Runtime de integração. Pacote jvm.dll com todos os outros necessário assemblies de OpenJDK em Ir Autoalojado máquina e variável de ambiente de sistema do conjunto JAVA_HOME em conformidade.

> [!TIP]
> Se copiar dados de/para Parquet formatar a utilizar o Integration Runtime autoalojado e pressionar a indicação de erro "Ocorreu um erro ao invocar java, mensagem: **espaço de área dinâmica para dados java.lang.OutOfMemoryError:Java**", pode adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que aloja o runtime de integração autoalojado para ajustar o tamanho de heap Mín/Máx para JVM capacitar a cópia, em seguida, voltar a executar o pipeline.

![Definir o tamanho da área dinâmica para dados JVM em Ir Autoalojado](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: Definir variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` Especifica o conjunto de alocação de memória inicial para um Java Virtual Machine (JVM), enquanto `Xmx` Especifica o conjunto de alocação de memória máxima. Isso significa que JVM será iniciado com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por predefinição, o ADF utilize mínimo 64MB e máximo de 1G.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)