---
title: Formato Avro na Azure Data Factory
description: Este tópico descreve como lidar com o formato Avro na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 7d61121b4c80b7b89ec29ade4ab1bfab91a660d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334349"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros Avro ou escrever os dados no formato Avro**. 

O formato Avro é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Avro.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida para **Avro**. | Sim      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . **Consulte os detalhes na secção de propriedades do conector -> Dataset**. | Sim      |
| avroCompressionCodec | O codec de compressão para usar ao escrever para ficheiros Avro. Ao ler os ficheiros avro, a Data Factory determina automaticamente o código de compressão com base nos metadados do ficheiro.<br>Os tipos suportados são "**nenhum**" (padrão), "**esvaziar**", "**snappy**". Nota atualmente A atividade copy não suporta Snappy quando lê/escreve ficheiros Avro. | Não       |

> [!NOTE]
> O espaço branco no nome da coluna não é suportado para ficheiros Avro.

Abaixo está um exemplo do conjunto de dados da Avro no Armazenamento Azure Blob:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia avro.

### <a name="avro-as-source"></a>Avro como fonte

As seguintes propriedades são suportadas na secção *** \* de origem \* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **AvroSource**. | Sim      |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

### <a name="avro-as-sink"></a>Avro como pia

As seguintes propriedades são suportadas na secção de *** \* lavatório \* *** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida como **AvroSink**. | Sim      |
| formatoStas          | Um grupo de propriedades. Consulte a tabela de **definições de escrita avro** abaixo.| Não      |
| lojaSs | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

Configurações de **escrita da Avro** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoStas devem ser definidas para **AvroWriteSettings**. | Sim                                                   |
| maxRowsPerFile | Ao escrever dados numa pasta, pode optar por escrever em vários ficheiros e especificar as linhas máximas por ficheiro.  | Não |
| fileNamePrefix | Aplicável quando `maxRowsPerFile` é configurado.<br> Especifique o prefixo do nome do ficheiro ao escrever dados em vários ficheiros, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo do nome do ficheiro será gerado automaticamente. Esta propriedade não se aplica quando a fonte é loja baseada em ficheiros ou [loja de dados ativada por opção de partição.](copy-activity-performance-features.md)  | Não |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler e escrever para o formato avro nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte avro. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos de wild card | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | não | Corda[] | wildcardPaths |
| Caminho da raiz da partição | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | não | Cadeia | partitionRootPath |
| Lista de ficheiros | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | não | `true` ou `false` | fileList |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem | não | Cadeia | rowUrlColumn |
| Após a conclusão | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | não | Excluir: `true` ou `false` <br> Mover-se: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | Timestamp | modificado Depois <br> modificadoSForo antes |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório avro. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Opção de nome de ficheiro | O formato de nomeação dos dados escritos. Por predefinição, um ficheiro por partição em formato `part-#####-tid-<guid>` | não | Padrão: Corda <br> Por partição: String[] <br> Como dados na coluna: String <br> Saída para um único ficheiro: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Citar tudo | Incluir todos os valores em cotações | não | `true` ou `false` | citaçãoTo |

## <a name="data-type-support"></a>Suporte ao tipo de dados

### <a name="copy-activity"></a>Atividade Copiar
Os [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) de Avro não são suportados (registos, enums, matrizes, mapas, sindicatos e fixos) na Copy Activity.

### <a name="data-flows"></a>Fluxos de dados
Ao trabalhar com ficheiros Avro em fluxos de dados, pode ler e escrever tipos de dados complexos, mas certifique-se de limpar o esquema físico do conjunto de dados primeiro. Nos fluxos de dados, pode definir a sua projeção lógica e colunas derivantes que são estruturas complexas, em seguida, mapear automaticamente esses campos para um ficheiro Avro.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
