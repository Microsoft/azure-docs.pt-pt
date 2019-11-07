---
title: Copiar dados de fontes OData usando Azure Data Factory
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
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 85da7752575164ba505d788a5d45f5af0908edc9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680595"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Copiar dados de uma fonte OData usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-odata-connector.md)
> * [Versão atual](connector-odata.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de uma fonte OData. O artigo se baseia na [atividade de cópia no Azure data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector OData tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de uma fonte OData para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte [armazenamentos e formatos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector OData dá suporte a:

- OData versão 3,0 e 4,0.
- Copiar dados usando uma das seguintes autenticações: **anônima**, **básica**, **Windows**, entidade de **serviço do AAD**e **identidades gerenciadas para recursos do Azure**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas a um conector OData.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um serviço vinculado do OData:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **OData**. |Sim |
| url | A URL raiz do serviço OData. |Sim |
| authenticationType | O tipo de autenticação usado para se conectar à fonte OData. Os valores permitidos são **Anonymous**, **Basic**, **Windows**, **AadServicePrincipal**e **ManagedServiceIdentity**. Não há suporte para o OAuth baseado em usuário. | Sim |
| Usu | Especifique o **nome de usuário** se você usar a autenticação básica ou do Windows. | Não |
| palavra-passe | Especifique a **senha** da conta de usuário que você especificou para **username**. Marque este campo como um tipo **SecureString** para armazená-lo com segurança em data Factory. Você também pode [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalId | Especifique a ID do cliente do aplicativo Azure Active Directory. | Não |
| aadServicePrincipalCredentialType | Especifique o tipo de credencial a ser usado para autenticação de entidade de serviço. Os valores permitidos são: `ServicePrincipalKey` ou `ServicePrincipalCert`. | Não |
| servicePrincipalKey | Especifique a chave do aplicativo Azure Active Directory. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalEmbeddedCert | Especifique o certificado codificado em base64 do seu aplicativo registrado no Azure Active Directory. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| servicePrincipalEmbeddedCertPassword | Especifique a senha do certificado se o certificado estiver protegido com uma senha. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md).  | Não|
| vários | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Recupere-o passando o mouse no canto superior direito do portal do Azure. | Não |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização.| Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo 1: usando a autenticação anônima**

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

**Exemplo 2: usando a autenticação básica**

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

**Exemplo 3: usando a autenticação do Windows**

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

**Exemplo 4: usando a autenticação de chave de entidade de serviço**

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

**Exemplo 5: usando a autenticação de certificado da entidade de serviço**

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Esta seção fornece uma lista das propriedades que o conjunto de suporte do OData suporta.

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do OData, defina a propriedade **Type** do conjunto de dado como **ODataResource**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **ODataResource**. | Sim |
| Multi-Path | O caminho para o recurso OData. | Sim |

**Exemplo**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades às quais a fonte OData dá suporte.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData como fonte

Para copiar dados do OData, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **OData**. | Sim |
| consulta | Opções de consulta OData para filtrar dados. Exemplo: `"$select=Name,Description&$top=5"`.<br/><br/>**Observação**: o conector OData copia dados da URL combinada: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Para obter mais informações, consulte [componentes de URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estivesse usando `RelationalSource` fonte tipada, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo no futuro.

## <a name="data-type-mapping-for-odata"></a>Mapeamento de tipo de dados para OData

Quando você copia dados do OData, os seguintes mapeamentos são usados entre tipos de dados OData e Azure Data Factory tipos de dados provisórios. Para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Data Factory tipo de dados provisório |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | bool |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimal | Vírgula |
| Edm.Double | Clique |
| EDM. single | Único |
| EDM. GUID | GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | String |
| EDM.Time | Período |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Não há suporte para tipos de dados complexos OData (como **Object**).


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores em Azure Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md##supported-data-stores-and-formats).