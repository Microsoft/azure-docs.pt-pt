---
title: Formato parquet na Azure Data Factory
description: Este tópico descreve como lidar com o formato Parquet na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: c99225b53266fc74ea357151de824cd8d8ed2088
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011613"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros Parquet ou escrever os dados no formato Parquet**. 

O formato Parquet é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Parquet.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida para **Parquet**. | Sim      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . **Consulte os detalhes na secção de propriedades do conector -> Dataset**. | Sim      |
| compressãoCodec | O codec de compressão para usar ao escrever para ficheiros Parquet. Ao ler os ficheiros Parquet, as Fábricas de Dados determinam automaticamente o código de compressão com base nos metadados do ficheiro.<br>Os tipos suportados são "**nenhum**", "**gzip**", "**snappy**" (padrão), e "**lzo**". Nota atualmente A atividade copy não suporta LZO quando lê/escreve ficheiros Parquet. | Não       |

> [!NOTE]
> O espaço branco no nome da coluna não é suportado para ficheiros Parquet.

Abaixo está um exemplo do conjunto de dados do Parquet no Armazenamento Azure Blob:

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de imóveis suportados pela fonte parquet e pia.

### <a name="parquet-as-source"></a>Parquet como fonte

As seguintes propriedades são suportadas na atividade de cópia **_ \_ secção \* fonte*** .

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida para **ParquetSource**. | Sim      |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como pia

As seguintes propriedades são suportadas na atividade de **cópia _ \_ pia \**** secção.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **ParquetSink**. | Sim      |
| formatoStas | Um grupo de propriedades. Consulte a tabela de **definições de escrita Parquet** abaixo. |    Não      |
| lojaSs | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

Configurações de **escrita de Parquet** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoStas devem ser definidas em **ParquetWriteSettings**. | Sim                                                   |
| maxRowsPerFile | Ao escrever dados numa pasta, pode optar por escrever em vários ficheiros e especificar as linhas máximas por ficheiro.  | Não |
| fileNamePrefix | Aplicável quando `maxRowsPerFile` é configurado.<br> Especifique o prefixo do nome do ficheiro ao escrever dados em vários ficheiros, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo do nome do ficheiro será gerado automaticamente. Esta propriedade não se aplica quando a fonte é loja baseada em ficheiros ou [loja de dados ativada por opção de partição.](copy-activity-performance-features.md)  | Não |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler e escrever para o formato parquet nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte de parquet. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `parquet` | yes | `parquet` | formato |
| Caminhos de wild card | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | não | Corda[] | wildcardPaths |
| Caminho da raiz da partição | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | não | String | partitionRootPath |
| Lista de ficheiros | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | não | `true` ou `false` | fileList |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem | não | String | rowUrlColumn |
| Após a conclusão | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | não | Excluir: `true` ou `false` <br> Mover-se: `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | Timestamp | modificado Depois <br> modificadoSForo antes |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de origem parquet nos fluxos de dados de mapeamento.

![Fonte parquet](media/data-flow/parquet-source.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório de parquet. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Formato deve ser `parquet` | yes | `parquet` | formato |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Opção de nome de ficheiro | O formato de nomeação dos dados escritos. Por predefinição, um ficheiro por partição em formato `part-#####-tid-<guid>` | não | Padrão: Corda <br> Por partição: String[] <br> Como dados na coluna: String <br> Saída para um único ficheiro: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Exemplo de pia

A imagem abaixo é um exemplo de uma configuração de pia de parquet em fluxos de dados de mapeamento.

![Pia de parquet](media/data-flow/parquet-sink.png)

O script de fluxo de dados associado é:

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>Suporte ao tipo de dados

Os tipos de dados complexos de parquet (por exemplo, MAP, LIST, STRUCT) são atualmente suportados apenas em Fluxos de Dados, e não em Atividade de Cópia. Para utilizar tipos complexos nos fluxos de dados, não importe o esquema de ficheiros no conjunto de dados, deixando o esquema em branco no conjunto de dados. Depois, na transformação da Fonte, importa a projeção.

## <a name="using-self-hosted-integration-runtime"></a>Utilização do tempo de execução de integração auto-hospedado

> [!IMPORTANT]
> Para cópias habilitadas pelo Self-hosted Integration Runtime, por exemplo, entre as lojas de dados no local e na nuvem, se não estiver a copiar ficheiros Parquet **como-is,** precisa de instalar o **JRE 8 (Java Runtime Environment) de 64 bits ou o OpenJDK** e **o Microsoft Visual C++ 2010 Redistributable Package** na sua máquina DE IR. Consulte o parágrafo seguinte com mais detalhes.

Para a cópia em execução em IR auto-hospedado com serialização/deserialização de ficheiros Parquet, a ADF localiza o tempo de execução de Java, verificando primeiro o registo *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* de JRE, se não for encontrado, verificando em segundo lugar a variável do sistema *`JAVA_HOME`* de verificação para OpenJDK.

- **Para utilizar o JRE**: O IR de 64 bits requer JRE de 64 bits. Pode encontrá-lo [daqui.](https://go.microsoft.com/fwlink/?LinkId=808605)
- **Para utilizar o OpenJDK:** É suportado desde a versão IV 3.13. Embale o jvm.dll com todos os outros conjuntos necessários de OpenJDK na máquina de INFRAVERMELHO Auto-hospedada, e definir a variável do ambiente do sistema JAVA_HOME em conformidade.
- **Para instalar o Pacote Redistribuable Visual C++ 2010**: O Pacote Redistribuable Visual C++ 2010 não está instalado com instalações de INFRA auto-hospedadas. Pode encontrá-lo [daqui.](https://www.microsoft.com/download/details.aspx?id=14632)

> [!TIP]
> Se copiar dados para/de Parquet utilizando o tempo de execução de integração auto-hospedado e o erro de sucesso dizendo "Ocorreu um erro ao invocar java, mensagem: **java.lang.OutOfMemoryError:Java heap space**", pode adicionar uma variável ambiental `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho da pilha min/max para JVM para capacitar tal cópia, em seguida, reenexerte o pipeline.

![Definir o tamanho da pilha JVM em IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: definir variável `_JAVA_OPTIONS` com valor `-Xms256m -Xmx16g` . A bandeira `Xms` especifica o conjunto inicial de atribuição de memória para uma Máquina Virtual Java (JVM), enquanto `Xmx` especifica o conjunto máximo de atribuição de memória. Isto significa que jVM será iniciado com `Xms` quantidade de memória e será capaz de usar um máximo de quantidade de `Xmx` memória. Por predefinição, a ADF utiliza min 64 MB e máx 1G.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
