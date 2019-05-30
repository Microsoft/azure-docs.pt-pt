---
title: Copiar dados de ou para a geração 1 de armazenamento do Azure Data Lake com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para o Azure Data Lake Store ou do Data Lake Store para lojas de sink suportado, com o Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: 94fb3fbe9def034dc36467d219cb3e8b45c40dad
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560617"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copiar dados de ou para a geração 1 de armazenamento do Azure Data Lake com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como copiar dados para e de geração 1 de armazenamento do Azure Data Lake (ADLS Gen1). Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de geração 1 de armazenamento do Azure Data Lake é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suportado de matriz de origem/sink](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector suporta:

- Copiar ficheiros através de um dos seguintes métodos de autenticação: **principal de serviço** ou **geridos identidades para recursos do Azure**.
- Copiar ficheiros como-está, ou análise ou gerar arquivos com o [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se copiar dados através do runtime de integração autoalojado, configurar a firewall da empresa para permitir tráfego de saída `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Azure Security Token Service que o runtime de integração precisa se comunicar com ao obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter instruções para utilizar o conector do Azure Data Lake Store, consulte [carregar dados para o Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o Store do Azure Data Lake de serviço ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O `type` propriedade deve ser definida **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Estas informações recebe um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | O ID de subscrição do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |
| resourceGroupName | O nome do grupo de recursos do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o runtime de integração do Azure ou integration runtime autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se esta propriedade não for especificada, ele usa o runtime de integração do Azure padrão. |Não |

### <a name="use-service-principal-authentication"></a>Utilizar a autenticação do principal de serviço

Para utilizar autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory e conceder acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

- ID da aplicação
- Chave da aplicação
- ID do inquilino

>[!IMPORTANT]
> Certifique-se de que concede a service principal permissão adequada no Data Lake Store:
>- **Como origem**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **leitura + execução** permissão para listar e copie os ficheiros em pastas e subpastas. Em alternativa, pode conceder **leitura** permissão para copiar um ficheiro individual. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Não existe nenhum requisito no controle de conta de nível de acesso (IAM).
>- **Como sink**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Se usar o runtime de integração do Azure para copiar (origem e sink são na cloud), no IAM, conceder, pelo menos, o **leitor** função para detetar a região para o Data Lake Store com o Data Factory. Se quiser evitar esta função IAM explicitamente [criar um runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização do Data Lake Store. Por exemplo, se seu Store de Lake de dados estiver da Europa Ocidental, crie um runtime de integração do Azure com a localização definida como "Leste Europeu". Associá-las no serviço do Data Lake Store ligado como no exemplo seguinte.

>[!NOTE]
>A lista de pastas a partir da raiz, tem de definir a permissão do principal de serviço, sendo concedido ao **no nível de raiz com permissão de "Executar"** . Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação.
>Se tiver a preocupação de concessão de permissão no nível raiz, pode ignorar manualmente a ligação de teste e o caminho de entrada durante a criação. Atividade de cópia continuarão a funcionar, desde que o principal de serviço é concedido com a permissão adequada nos arquivos sejam copiados.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um `SecureString` armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Utilizar identidades geridas para a autenticação de recursos do Azure

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Pode utilizar diretamente esta identidade gerida para a autenticação do Data Lake Store, semelhante à utilização da sua própria principal de serviço. Ele permite que esta fábrica designada para aceder e copiar dados de ou para o Data Lake Store.

Para utilizar identidades geridas para a autenticação de recursos do Azure:

1. [Obter as informações de identidade do data factory gerido](data-factory-service-identity.md#retrieve-managed-identity) ao copiar o valor do "serviço de ID da Identity Application" gerada juntamente com sua fábrica.
2. Conceda o acesso de identidade gerida para o Data Lake Store, da mesma forma que fizer para o principal de serviço, seguindo estas notas.

>[!IMPORTANT]
> Certifique-se de que concede o data factory gerido identidade permissão adequada no Data Lake Store:
>- **Como origem**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **leitura + execução** permissão para listar e copie os ficheiros em pastas e subpastas. Em alternativa, pode conceder **leitura** permissão para copiar um ficheiro individual. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Não existe nenhum requisito no controle de conta de nível de acesso (IAM).
>- **Como sink**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Se usar o runtime de integração do Azure para copiar (origem e sink são na cloud), no IAM, conceder, pelo menos, o **leitor** função para detetar a região para o Data Lake Store com o Data Factory. Se quiser evitar esta função IAM explicitamente [criar um runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização do Data Lake Store. Associá-las no serviço do Data Lake Store ligado como o exemplo seguinte.

>[!NOTE]
>A lista de pastas a partir da raiz, tem de definir a permissão de a identidade gerida sendo concedido ao **no nível de raiz com permissão de "Executar"** . Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação.
>Se tiver a preocupação de concessão de permissão no nível raiz, pode ignorar manualmente a ligação de teste e o caminho de entrada durante a criação. Atividade de cópia continuarão a funcionar, desde que a identidade gerida é concedida com a permissão adequada nos arquivos sejam copiados.

No Azure Data Factory, não terá de especificar quaisquer propriedades além das informações gerais do Data Lake Store no serviço ligado.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. 

- Para **Parquet e o formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) secção.
- Como outros formatos **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) secção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet e o conjunto de dados de formato de texto delimitado

Para copiar dados de e para ADLS Gen1 na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo num conjunto de dados com base no formato e definições suportadas. As seguintes propriedades são suportadas para a geração 1 do ADLS em `location` definições no conjunto de dados com base no formato:

| Propriedade   | Descrição                                                  | Necessário |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade de tipo sob `location` no conjunto de dados deve ser definida como **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para a pasta. Se pretender utilizar o caráter universal para a pasta de filtro, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |
| fileName   | O nome de ficheiro em determinado folderPath. Se pretender utilizar o caráter universal para filtrar os ficheiros, ignorar esta definição e especifique nas definições da origem de atividade. | Não       |

> [!NOTE]
>
> **AzureDataLakeStoreFile** tipo conjunto de dados com o formato Parquet/Text mencionado na secção seguinte ainda é suportado como-é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas ele não funciona com o mapeamento de fluxo de dados. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de dados do formato

Para copiar dados de e para ADLS Gen1 na **formato ORC/Avro/JSON/binário**, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **AzureDataLakeStoreFile** |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não for especificado, ele aponta para a raiz. <br/><br/>Filtro de carateres universais é suportado, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br/><br/>Exemplos: rootfolder/subpasta /, veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). |Não |
| fileName | **Filtro de nome ou o caráter universal** para o ficheiro ou ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída e **preserveHierarchy** não seja especificado no sink de atividade, a atividade de cópia gera automaticamente o nome de ficheiro com o seguinte padrão: "*Dados. [o GUID de ID de execução da atividade]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]* ". Por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar a partir da origem de tabela com o nome da tabela em vez de consulta, o padrão de nome é " *[nome da tabela]. [ formato]. [compressão se configurado]* ". Por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral do movimento de dados vai ser afetado por ativar esta definição quando pretender que o filtro de ficheiros a partir de enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser nulo, isso significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| modifiedDatetimeEnd | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral do movimento de dados vai ser afetado por ativar esta definição quando pretender que o filtro de ficheiros a partir de enormes quantidades de ficheiros. <br/><br/> As propriedades podem ser nulo, isso significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado.| Não |
| format | Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar arquivos com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Ideal** e **mais rápida**. |Não |


>[!TIP]
>Para copiar todos os ficheiros numa pasta, especifique **folderPath** apenas.<br>Para copiar um único ficheiro com um nome específico, especifique **folderPath** com parte da pasta e **fileName** com o nome de ficheiro.<br>Para copiar um subconjunto de ficheiros numa pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro de carateres universais. 

**Exemplo:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pelo Azure Data Lake Store origem e sink.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como origem

- Para copiar a partir **Parquet e o formato de texto delimitado**, consulte [Parquet e origem de formato de texto delimitado](#parquet-and-delimited-text-format-source) secção.
- Para copiar a partir de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra origem de formato](#other-format-source) secção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e origem de formato de texto delimitado

Para copiar dados de geração 1 do ADLS na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo na origem de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para a geração 1 do ADLS em `storeSettings` definições na origem de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade de tipo sob `storeSettings` deve ser definida como **AzureDataLakeStoreReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que quando recursiva é definida como true e o sink é um arquivo baseado em arquivo, uma pasta vazia ou uma subpasta não é copiada ou criada no coletor. Valores permitidos são **true** (predefinição) e **falso**. | Não                                            |
| wildcardFolderPath       | O caminho de pasta com carateres universais para filtrar as pastas de origem. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape. <br>Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome de ficheiro com carateres universais em determinado folderPath/wildcardFolderPath para ficheiros de origem do filtro. <br>Permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual); utilize `^` para se o seu nome de pasta real tem carateres universais ou esse caractere de escape dentro de escape.  Veja mais exemplos [exemplos de filtro de ficheiros e pastas](#folder-and-file-filter-examples). | Se Sim `fileName` não está especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de ficheiros baseado no atributo: Última modificação. Os ficheiros serão selecionados, se sua hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulo o que significa que nenhum filtro de atributo de ficheiro será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de datetime mas `modifiedDatetimeEnd` má hodnotu NULL, significa que os ficheiros cujo último atributo modificado é maior que ou igual a com o valor de datetime será selecionado.  Quando `modifiedDatetimeEnd` tem o valor de datetime mas `modifiedDatetimeStart` for nulo, significa que os ficheiros cujo último atributo modificado é menor do que o valor de datetime será selecionado. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de armazenamento em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **AzureDataLakeStoreSource** continua a ser suportada como origem da atividade de cópia de tipo mencionada na secção seguinte-é para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra origem de formato

Para copiar dados de geração 1 do ADLS na **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O `type` propriedade da origem de atividade de cópia tem de ser definida como: **AzureDataLakeStoreSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que, ao `recursive` está definido como true e o sink é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Valores permitidos são: **true** (predefinição) e **falso**. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como sink

- Para copiar para **Parquet e o formato de texto delimitado**, consulte [Parquet e de sink de formato de texto delimitado](#parquet-and-delimited-text-format-sink) secção.
- Para copiar para outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outro sink de formato](#other-format-sink) secção.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e de sink de formato de texto delimitado

Para copiar dados para a geração 1 do ADLS na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo sobre o sink de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para a geração 1 do ADLS em `storeSettings` as definições de sink de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade de tipo sob `storeSettings` deve ser definida como **AzureDataLakeStoreWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: Preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: Todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes de geradas automaticamente. <br/><b>-MergeFiles</b>: Une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, é um nome de ficheiro gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não       |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **um AzureDataLakeStoreSink** continua a ser suportado como sink de atividade de cópia de tipo mencionado na secção seguinte-é para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro sink de formato

Para copiar dados para a geração 1 do ADLS na **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas no **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O `type` propriedade do coletor de atividade de cópia tem de ser definida como: **AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes gerado automaticamente. <br/><b>-MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro, o nome de ficheiro intercalada é o nome especificado. Caso contrário, o nome de ficheiro é gerado automaticamente. | Não |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de dados em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Pasta e exemplos de filtro de ficheiro

Esta secção descreve o comportamento resultante o nome de ficheiro e caminho de pasta com filtros de caráter universal.

| folderPath | fileName | recursive | Resultado de estrutura e o filtro de pasta de origem (arquivos no **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, utilizar a predefinição) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (vazio, utilizar a predefinição) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de `recursive` e `copyBehavior` valores.

| recursive | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num arquivo, com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar as ACLs para a geração 2 do Data Lake Storage

Se pretender replicar as ACLs juntamente com os ficheiros de dados ao atualizar do Data Lake Storage Gen1 para geração 2, consulte [preservar as ACLs de geração 1 de armazenamento do Data Lake](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Propriedades de fluxo de dados de mapeamento

Conheça os detalhes da [transformação de origem](data-flow-source.md) e [sink transformação](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados suportados como origens e sinks, atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
