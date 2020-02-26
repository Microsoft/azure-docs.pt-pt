---
title: Formato Parquet na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato Parquet na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597631"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato Parquet na Fábrica de Dados Azure

Siga este artigo quando pretender **analisar os ficheiros Parquet ou escrever os dados no formato Parquet**. 

O formato Parquet é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Parquet.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Parquet**. | Sim      |
| localização         | Definições de localização dos ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location`. **Consulte detalhes no artigo do conector -> Secção**de propriedades do conjunto de dados . | Sim      |
| compressãoCodec | O código de compressão para usar ao escrever para ficheiros Parquet. Ao ler os ficheiros Parquet, as Fábricas de Dados determinam automaticamente o código de compressão com base nos metadados do ficheiro.<br>Os tipos suportados são "**nenhum**", "**gzip**", "**snappy**" (padrão) e "**lzo**". Nota atualmente A atividade da Cópia não suporta a LZO ao ler/escrever ficheiros Parquet. | Não       |

> [!NOTE]
> O espaço branco em nome da coluna não é suportado para ficheiros Parquet.

Abaixo está um exemplo de conjunto de dados parquet no armazenamento de Blob Azure:

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte parquet e pia.

### <a name="parquet-as-source"></a>Parquet como fonte

As seguintes propriedades são suportadas na atividade de cópia ***\*secção\*fonte.***

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida para **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

### <a name="parquet-as-sink"></a>Parquet como pia

As seguintes propriedades são suportadas na atividade de cópia ***\*pia\**** secção.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida para **ParquetSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Aprenda detalhes da transformação de [fontes](data-flow-source.md) e [transformação](data-flow-sink.md) de afundar no fluxo de dados de mapeamento.

## <a name="data-type-support"></a>Suporte de tipo de dados

Os tipos de dados complexos de Parquet não são atualmente suportados (por exemplo, MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Utilização do Tempo de Integração Auto-Hospedado

> [!IMPORTANT]
> Para cópias empoderadas por Tempo de Execução de Integração Auto-hospedado, por exemplo, entre as lojas de dados no local e as lojas de dados em nuvem, se não estiver a copiar os ficheiros Parquet **como está,** precisa de instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** e o **Microsoft Visual C++ 2010 Redistribuable Package** na sua máquina de INFRAVERMELHOS. Consulte o parágrafo seguinte com mais detalhes.

Para cópia sintetizada em IR auto-hospedado com serialização/desserialização de ficheiros Parquet, a ADF localiza o tempo de funcionamento de Java verificando primeiro o registo *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para JRE, se não for encontrado, verificando em segundo lugar a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para utilizar JRE**: O IR de 64 bits requer 64 bits JRE. Pode encontrá-lo [daqui.](https://go.microsoft.com/fwlink/?LinkId=808605)
- Para utilizar o **OpenJDK**: É suportado desde a versão 3.13 do IR. Emba o jvm.dll com todos os outros conjuntos necessários do OpenJDK em máquina de INFRAVERMELHOS auto-hospedada, e coloque a variável do ambiente do sistema JAVA_HOME em conformidade.
- **Para instalar C++ o Pacote Redistribuível Visual 2010**: O Pacote Redistribuível Visual C++ 2010 não está instalado com instalações de INFRAVERMELHOS auto-hospedadas. Pode encontrá-lo [daqui.](https://www.microsoft.com/download/details.aspx?id=14632)

> [!TIP]
> Se copiar dados para/a partir do formato Parquet utilizando o Tempo de Execução de Integração Auto-hospedado e atingir um erro dizendo "Ocorreu um erro ao invocar java, mensagem: **java.lang.OutOfMemoryError:Java heap space",** pode adicionar uma variável ambiental `_JAVA_OPTIONS` na máquina que acolhe o IR auto-hospedado para ajustar o tamanho do monte min/max para jVM para capacitar essa cópia, em seguida, reexecutar o pipeline.

![Definir o tamanho da pilha JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir `_JAVA_OPTIONS` variável com `-Xms256m -Xmx16g`de valor . O `Xms` da bandeira especifica o conjunto inicial de atribuição de memória para uma Máquina Virtual Java (JVM), enquanto `Xmx` especifica o conjunto máximo de atribuição de memória. Isto significa que o JVM será iniciado com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por predefinição, a ADF utiliza min 64 MB e máx.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
