---
title: Formato parquet no Azure Data Factory
description: Este tópico descreve como lidar com o formato parquet no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9047e82713b709027275c75b17eb955877c62f08
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674775"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos parquet ou gravar os dados no formato parquet**. 

O formato parquet tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Armazenamento em nuvem](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de parquet.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade Type do conjunto de conjuntos deve ser definida como **parquet**. | Sim      |
| localização         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location`. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| compressionCodec | O codec de compactação a ser usado ao gravar em arquivos parquet. Ao ler de arquivos parquet, Data Factory determinará automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**", "**gzip**", "**encaixado**" (padrão) e "**LZO**". Observação a atividade de cópia atualmente não dá suporte a LZO. | Não       |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos parquet.

Veja abaixo um exemplo de conjunto de parquet no armazenamento de BLOBs do Azure:

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do parquet.

### <a name="parquet-as-source"></a>Parquet como fonte

As propriedades a seguir têm suporte na seção ***\*de origem*** da atividade de cópia\*.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como coletor

As propriedades a seguir têm suporte na seção de ***\*do coletor*** de atividade de cópia\*.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Atualmente, não há suporte para tipos de dados complexos do parquet (por exemplo, mapa, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para cópia habilitada pelo Integration Runtime autohospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos parquet **como estão**, será necessário instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** em seu computador ir. Consulte o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo parquet, o ADF localiza o tempo de execução do Java verificando primeiro o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para o JRE, se não for encontrado, verificando a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. Você pode encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários do OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

> [!TIP]
> Se você copiar dados de/para o formato parquet usando o autohospedado Integration Runtime e erro de ocorrência que diz "ocorreu um erro ao invocar Java, mensagem: **Java. lang. OutOfMemoryError: espaço de heap Java**", você pode adicionar uma variável de ambiente `_JAVA_OPTIONS` no computador que hospeda o IR auto-hospedado para ajustar o tamanho de heap mínimo/máximo para a JVM para capacitar tal cópia, em seguida, execute novamente o pipeline.

![Definir o tamanho do heap JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para um Máquina Virtual Java (JVM), enquanto `Xmx` especifica o pool de alocação de memória máximo. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e poderá usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa mín. de 64MB e Max 1G.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
