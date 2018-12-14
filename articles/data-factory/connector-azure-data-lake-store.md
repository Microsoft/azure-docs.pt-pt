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
ms.date: 11/05/2018
ms.author: jingwang
ms.openlocfilehash: 411223c2ef7b1400f2019ebc6704b3c4a5d41235
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387034"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copiar dados de ou para a geração 1 de armazenamento do Azure Data Lake com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Data Lake Storage Gen1 (anteriormente conhecido como o Azure Data Lake Store). Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer arquivo de dados de origem suportada para o Azure Data Lake Store ou copiar dados do Azure Data Lake Store para qualquer arquivo de dados de sink suportados. Consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do Azure Data Lake Store suporta:

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
| tipo | O `type` propriedade deve ser definida **AzureDataLakeStore**. | Sim |
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
>- **Como sink**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Se usar o runtime de integração do Azure para copiar (origem e sink são na cloud), no IAM, conceder, pelo menos, o **leitor** função para detetar a região para o Data Lake Store com o Data Factory. Se quiser evitar esta função IAM explicitamente [criar um runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização do Data Lake Store. Associá-las no serviço do Data Lake Store ligado como o exemplo seguinte.

>[!NOTE]
>A lista de pastas a partir da raiz, tem de definir a permissão do principal de serviço, sendo concedido ao **no nível de raiz com permissão de "Executar"**. Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um `SecureString` armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

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

Uma fábrica de dados pode ser associada com um [identidade de recursos do Azure gerida](data-factory-service-identity.md), que representa esta fábrica de dados específicos. Pode utilizar diretamente esta identidade de serviço para a autenticação do Data Lake Store, semelhante à utilização da sua própria principal de serviço. Ele permite que esta fábrica designada para aceder e copiar dados de ou para o Data Lake Store.

Para utilizar identidades geridas para a autenticação de recursos do Azure:

1. [Obter a identidade de serviço da fábrica de dados](data-factory-service-identity.md#retrieve-service-identity) ao copiar o valor do "serviço de ID da Identity Application" gerada juntamente com sua fábrica.
2. Conceda o acesso de identidade de serviço para o Data Lake Store, da mesma forma que fizer para o principal de serviço, seguindo estas notas.

>[!IMPORTANT]
> Certifique-se de que concede o data factory serviço identidade permissão adequada no Data Lake Store:
>- **Como origem**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **leitura + execução** permissão para listar e copie os ficheiros em pastas e subpastas. Em alternativa, pode conceder **leitura** permissão para copiar um ficheiro individual. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Não existe nenhum requisito no controle de conta de nível de acesso (IAM).
>- **Como sink**: Na **o Data explorer** > **acesso**, conceder, pelo menos, **escrita + execução** permissão para criar itens subordinados numa pasta. Pode optar por adicionar ao **esta pasta e todos os filhos** para recursiva e adicionar como **uma permissão de acesso e uma entrada de permissão predefinida**. Se usar o runtime de integração do Azure para copiar (origem e sink são na cloud), no IAM, conceder, pelo menos, o **leitor** função para detetar a região para o Data Lake Store com o Data Factory. Se quiser evitar esta função IAM explicitamente [criar um runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com a localização do Data Lake Store. Associá-las no serviço do Data Lake Store ligado como o exemplo seguinte.

>[!NOTE]
>A lista de pastas a partir da raiz, tem de definir a permissão do principal de serviço, sendo concedido ao **no nível de raiz com permissão de "Executar"**. Isso é verdade, quando utiliza o:
>- **Ferramenta copiar dados** para o pipeline de cópia de autor.
>- **IU do Data Factory** para testar a ligação e navegar pastas durante a criação.

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

Para copiar dados de e para o Azure Data Lake Store, defina o `type` propriedade do conjunto de dados para **AzureDataLakeStoreFile**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **AzureDataLakeStoreFile** |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Não é suportado o filtro de carateres universais. Se não for especificado, ele aponta para a raiz. Exemplo: rootfolder/subpasta / |Não |
| fileName | **Filtro de nome ou o caráter universal** para o ficheiro ou ficheiros sob o "folderPath" especificado. Se não especificar um valor para esta propriedade, o conjunto de dados aponta para todos os ficheiros na pasta. <br/><br/>Para o filtro, permitidos carateres universais são: `*` (corresponde a zero ou mais carateres) e `?` (corresponde a zero ou caráter individual).<br/>-Exemplo 1: `"fileName": "*.csv"`<br/>-Exemplo 2: `"fileName": "???20180427.txt"`<br/>Utilize `^` para se o seu nome de ficheiro real tem carateres universais ou esse caractere de escape dentro de escape.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída e **preserveHierarchy** não seja especificado no sink de atividade, a atividade de cópia gera automaticamente o nome de ficheiro com o seguinte padrão: "*Dados. [id de execução da atividade GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compressão se configurado]* ". Por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se copiar a partir da origem de tabela com o nome da tabela em vez de consulta, o padrão de nome é "*[nome da tabela]. [ formato]. [compressão se configurado]* ". Por exemplo, "MyTable.csv". |Não |
| Formato | Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar ou gerar arquivos com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não (apenas para o cenário de cópia binária) |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Ideal** e **mais rápida**. |Não |


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
            "fileName": "myfile.csv.gz",
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

Para copiar dados do Data Lake Store, defina o tipo de origem na atividade de cópia para **AzureDataLakeStoreSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O `type` propriedade da origem de atividade de cópia tem de ser definida como: **AzureDataLakeStoreSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. Tenha em atenção que, ao `recursive` está definido como true e o sink é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Valores permitidos são: **true** (predefinição) e **falso**. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

Para copiar dados para o Data Lake Store, defina o tipo de sink na atividade de cópia para **um AzureDataLakeStoreSink**. As seguintes propriedades são suportadas os **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O `type` propriedade do coletor de atividade de cópia tem de ser definida como: **Um AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros a partir de um arquivo de dados baseados em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm nomes gerado automaticamente. <br/><b>-MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, o nome de ficheiro é gerado automaticamente. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de `recursive` e `copyBehavior` valores.

| recursiva | copyBehavior | Estrutura de pastas de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num arquivo, com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | O destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
