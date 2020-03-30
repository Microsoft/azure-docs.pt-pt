---
title: Formato ORC na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato ORC na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597495"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC na Fábrica de Dados Azure

Siga este artigo quando pretender **analisar os ficheiros ORC ou escrever os dados em formato ORC**. 

O formato ORC é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados ORC.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Orc**. | Sim      |
| localização         | Definições de localização dos ficheiros. Cada conector baseado em ficheiros tem `location`o seu próprio tipo de localização e propriedades suportadas em . Consulte os detalhes na secção de propriedades do conjunto de dados **do conector ->**. | Sim      |

Abaixo está um exemplo de conjunto de dados ORC no Armazenamento de Blob Azure:

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

* Não são suportados tipos de dados complexos (STRUCT, MAP, LIST, UNION).
* Espaço branco em nome da coluna não é suportado.
* O ficheiro ORC tem três [opções relacionadas com a compressão](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NENHUM, ZLIB, SNAPPY. O Data Factory suporta a leitura de dados a partir de um ficheiro ORC em qualquer um destes formatos de compressão. Utiliza o codec de compressão existente nos metadados para ler os dados. No entanto, ao escrever num ficheiro ORC, o Data Factory escolhe a opção ZLIB, que é a predefinição para ORC. De momento, não existem opções para contornar este comportamento.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte orc e pia.

### <a name="orc-as-source"></a>ORC como fonte

As seguintes propriedades são suportadas na secção de *** \*origem\* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **OrcSource**. | Sim      |
| lojasDefinições | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de leitura suportadas em . . Consulte os detalhes na secção de propriedades de propriedade do artigo do **conector -> Copiar**. | Não       |

### <a name="orc-as-sink"></a>ORC como pia

As seguintes propriedades são suportadas na secção de *** \*sumidouro\* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **OrcSink**. | Sim      |
| lojasDefinições | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de escrita suportadas em baixo . Consulte os detalhes na secção de propriedades de propriedade do artigo do **conector -> Copiar**. | Não       |

## <a name="using-self-hosted-integration-runtime"></a>Utilização do Tempo de Integração Auto-Hospedado

> [!IMPORTANT]
> Para cópia sufiada por Tempo de Execução de Integração Auto-hospedado, por exemplo, entre as lojas de dados no local e as lojas de dados em nuvem, se não estiver a copiar os ficheiros ORC **como está,** precisa de instalar o **JRE 8 de 64 bits (Java Runtime Environment) ou OpenJDK** e **Microsoft Visual C++ 2010 Redistribuable Package** na sua máquina de INFRAVERMELHOS. Consulte o parágrafo seguinte com mais detalhes.

Para cópia sintetizada em IR auto-hospedado com serialização/desserialização de ficheiros ORC, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* a ADF localiza o tempo de *`JAVA_HOME`* funcionamento de Java verificando primeiro o registo de JRE, se não for encontrado, verificando em segundo lugar a variável do sistema para OpenJDK.

- **Para utilizar JRE**: O IR de 64 bits requer 64 bits JRE. Pode encontrá-lo [daqui.](https://go.microsoft.com/fwlink/?LinkId=808605)
- Para utilizar o **OpenJDK**: É suportado desde a versão 3.13 do IR. Emba o jvm.dll com todos os outros conjuntos necessários do OpenJDK em máquina de INFRAVERMELHOS auto-hospedada, e coloque a variável do ambiente do sistema JAVA_HOME em conformidade.
- Para instalar o **Pacote Redistribuível Visual C++ 2010**: O Pacote Redistribuível Visual C++ 2010 não está instalado com instalações de INFRAVERMELHOs auto-hospedadas. Pode encontrá-lo [daqui.](https://www.microsoft.com/download/details.aspx?id=14632)

> [!TIP]
> Se copiar dados para/a partir do formato ORC utilizando o Tempo de Execução de Integração Auto-hospedado e atingir um erro dizendo "Ocorreu um erro ao invocar java, mensagem: **java.lang.OutOfMemoryError:Java heap space**", pode adicionar uma variável `_JAVA_OPTIONS` ambiental na máquina que acolhe o IR auto-hospedado para ajustar o tamanho do monte min/max para jVM para capacitar tal cópia, em seguida, reexecutar o pipeline.

![Definir o tamanho da pilha JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir `_JAVA_OPTIONS` variável com valor `-Xms256m -Xmx16g`. A `Xms` bandeira especifica o conjunto inicial de atribuição de memória `Xmx` para uma Máquina Virtual Java (JVM), enquanto especifica o conjunto máximo de atribuição de memória. Isto significa que o JVM será iniciado com `Xms` uma quantidade `Xmx` de memória e será capaz de usar um máximo de quantidade de memória. Por predefinição, a ADF utiliza min 64 MB e máx.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
