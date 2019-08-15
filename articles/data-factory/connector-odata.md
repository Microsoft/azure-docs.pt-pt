---
title: Copiar dados de fontes OData usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de fontes OData para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 30bad3dd519d622d7e224da7bd53e7c6625014f6
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966483"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Copiar dados de uma fonte OData usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-odata-connector.md)
> * [Versão atual](connector-odata.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de uma fonte OData. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de uma fonte OData para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector OData dá suporte a:

- OData versão 3,0 e 4,0.
- Copiando dados usando uma das seguintes autenticações: **Anônimo**, **básico**, **Windows**, **entidade de serviço do AAD**e **identidades gerenciadas para recursos do Azure**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas a um conector OData.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para um serviço vinculado do OData:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **OData**. |Sim |
| url | A URL raiz do serviço OData. |Sim |
| authenticationType | O tipo de autenticação usado para se conectar à fonte OData. Os valores permitidos são **Anonymous**, **Basic**, **Windows**, **AadServicePrincipal**e **ManagedServiceIdentity**. Não há suporte para o OAuth baseado em usuário. | Sim |
| userName | Especifique **userName** se utilizar autenticação básica ou do Windows. | Não |
| password | Especifique **password** para o utilizador da conta que especificou para **userName**. Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Você também pode [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalId | Especifique a ID do cliente do aplicativo Azure Active Directory. | Não |
| aadServicePrincipalCredentialType | Especifique o tipo de credencial a ser usado para autenticação de entidade de serviço. Os valores permitidos são `ServicePrincipalKey` : `ServicePrincipalCert`ou. | Não |
| servicePrincipalKey | Especifique a chave do aplicativo Azure Active Directory. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalEmbeddedCert | Especifique o certificado codificado em base64 do seu aplicativo registrado no Azure Active Directory. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalEmbeddedCertPassword | Especifique a senha do certificado se o certificado estiver protegido com uma senha. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md).  | Não|
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Não |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização.| Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

**Exemplo 1: Usando a autenticação anônima**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Usando a autenticação básica**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 3: Usando a autenticação do Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 4: Usando a autenticação da chave da entidade de serviço**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exemplo 5: Usando a autenticação de certificado da entidade de serviço**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista das propriedades que o conjunto de suporte do OData suporta.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados do OData, defina a propriedade **Type** do conjunto de dado como **ODataResource**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** do conjunto de conjuntos deve ser definida como **ODataResource**. | Sim |
| path | O caminho para o recurso OData. | Sim |

**Exemplo**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista das propriedades às quais a fonte OData dá suporte.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData como fonte

Para copiar dados do OData, defina o tipo de **origem** na atividade de cópia como **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **RelationalSource**. | Sim |
| query | Opções de consulta OData para filtrar dados. Exemplo: `"$select=Name,Description&$top=5"`.<br/><br/>**Nota**: O conector OData copia dados da URL combinada: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Para obter mais informações, consulte [componentes de URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Mapeamento de tipo de dados para OData

Quando você copia dados do OData, os seguintes mapeamentos são usados entre tipos de dados OData e Azure Data Factory tipos de dados provisórios. Para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte Mapeamentos de [tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| EDM. time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Não há suporte para tipos de dados complexos OData (como **Object**).


## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md##supported-data-stores-and-formats).