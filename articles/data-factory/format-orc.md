---
title: Formato ORC no Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato ORC no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: beff81b7e8d207a2173497d039b915b7614fc30e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952280"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos orc ou gravar os dados no formato Orc**. 

O formato ORC tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Armazenamento](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de ORC.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade Type do conjunto de conjuntos deve ser definida como **Orc**. | Sim      |
| localização         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e as propriedades com suporte em `location`. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |

Veja abaixo um exemplo de conjunto de ORC no armazenamento de BLOBs do Azure:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Tenha em atenção os seguintes pontos:

* Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST, UNION).
* Não há suporte para o espaço em branco no nome da coluna.
* O ficheiro ORC tem três [opções relacionadas com a compressão](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NENHUM, ZLIB, SNAPPY. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro ORC, o Data Factory escolhe a opção ZLIB, que é a predefinição para ORC. De momento, não existem opções para contornar este comportamento.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do ORC.

### <a name="orc-as-source"></a>ORC como fonte

As propriedades a seguir têm suporte na seção ***\*de origem*** da atividade de cópia\*.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade Type da fonte da atividade de cópia deve ser definida como **OrcSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="orc-as-sink"></a>ORC como coletor

As propriedades a seguir têm suporte na seção de ***\*do coletor*** de atividade de cópia\*.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade Type da fonte da atividade de cópia deve ser definida como **OrcSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para cópia habilitada pelo Integration Runtime autohospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos ORC **como estão**, será necessário instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou o OpenJDK** em seu computador ir. Consulte o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com serialização/desserialização de arquivo ORC, o ADF localiza o tempo de execução do Java verificando primeiro o registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* para o JRE, se não for encontrado, verificando a variável do sistema *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. Você pode encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o JVM. dll com todos os outros assemblies necessários de OpenJDK no computador IR auto-hospedado e defina a variável de ambiente do sistema JAVA_HOME de acordo.

> [!TIP]
> Se você copiar dados de/para o formato ORC usando o autohospedado Integration Runtime e erro de ocorrência indicando "ocorreu um erro ao invocar Java, mensagem: **Java. lang. OutOfMemoryError: espaço de heap Java**", você pode adicionar uma variável de ambiente `_JAVA_OPTIONS` no computador que hospeda o IR auto-hospedado para ajustar o tamanho de heap mínimo/máximo para a JVM para capacitar essa cópia, em seguida, execute novamente o pipeline.

![Definir o tamanho do heap JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para um Máquina Virtual Java (JVM), enquanto `Xmx` especifica o pool de alocação de memória máximo. Isso significa que a JVM será iniciada com a quantidade `Xms` de memória e poderá usar uma quantidade máxima de `Xmx` de memória. Por padrão, o ADF usa mín. de 64MB e Max 1G.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
