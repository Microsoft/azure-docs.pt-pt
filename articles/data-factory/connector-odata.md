---
title: Copiar dados de fontes OData utilizando a Azure Data Factory
description: Saiba como copiar dados de fontes OData para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: jingwang
ms.openlocfilehash: 10121243961d4c81ecc67d7453019c26743fe610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87845770"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Copie dados de uma fonte OData utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-odata-connector.md)
> * [Versão atual](connector-odata.md)

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados de uma fonte OData. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector OData é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma fonte OData para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector OData suporta:

- OData versão 3.0 e 4.0.
- Copiar dados utilizando uma das seguintes autenticações: **Anónimo,** **Básico,** **Windows**e **principal serviço AAD**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas de um conector OData.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado ao OData:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **OData**. |Sim |
| url | O URL raiz do serviço OData. |Sim |
| authenticationType | O tipo de autenticação utilizado para ligar à fonte OData. Os valores permitidos são **Anónimos,** **Básicos,** **Windows**e **AadServicePrincipal**. O OAuth baseado no utilizador não é suportado. | Sim |
| userName | Especifique **o nome do utilizador** se utilizar a autenticação Básica ou o Windows. | Não |
| palavra-passe | Especifique a **palavra-passe** para a conta de utilizador especificada para **o nome de utilizador**. Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) | Não |
| servicePrincipalId | Especificar o ID do cliente do Azure Ative Directory. | Não |
| aadServicePrincipalCredentialType | Especifique o tipo de credencial a utilizar para a autenticação principal do serviço. Os valores permitidos são: `ServicePrincipalKey` ou `ServicePrincipalCert` . | Não |
| servicePrincipalKey | Especifique a chave da aplicação do Diretório Ativo Azure. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Não |
| serviçoPrincipalEmbeddedCert | Especifique o certificado codificado base64 da sua aplicação registada no Diretório Ativo Azure. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Não |
| serviçoPrincipalEmbeddedCertPassword | Especifique a palavra-passe do seu certificado se o seu certificado estiver protegido com uma senha. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md).  | Não|
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Não |
| aadResourceId | Especifique o recurso AAD que está a solicitar para autorização.| Não |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação AAD está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | Não |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |Não |

**Exemplo 1: Utilização da autenticação anónima**

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

**Exemplo 2: Utilização da autenticação básica**

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

**Exemplo 3: Utilização da autenticação do Windows**

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

**Exemplo 4: Utilização da autenticação principal do serviço**

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

**Exemplo 5: Utilização da autenticação do certificado principal do serviço**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades que o conjunto de dados OData suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados do OData, defina **a** propriedade tipo do conjunto de dados para **ODataResource**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **ODataResource**. | Sim |
| caminho | O caminho para o recurso OData. | Sim |

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a fonte OData suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData como fonte

Para copiar dados do OData, as seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida como **ODataSource**. | Sim |
| consulta | Opções de consulta OData para filtragem de dados. Exemplo: `"$select=Name,Description&$top=5"`.<br/><br/>**Nota**: O conector OData copia os dados do URL combinado: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` . Para obter mais informações, consulte [os componentes URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. Se não for especificado, o valor predefinido é **00:30:00** (30 minutos). | Não |

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

Se estava a usar `RelationalSource` uma fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

## <a name="data-type-mapping-for-odata"></a>Mapeamento do tipo de dados para OData

Ao copiar dados do OData, são utilizados os seguintes mapeamentos entre os tipos de dados OData e os tipos de dados provisórios da Azure Data Factory. Para saber como a Copy Activity mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados.](copy-activity-schema-and-type-mapping.md)

| Tipo de dados OData | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double (Duplo) |
| Edm.Single | Único |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Cadeia |
| Edm.Tempo | TimeSpan |
| Edm.DateTimeOffset | Início de execução de tempo de data |

> [!NOTE]
> Os tipos de dados complexos oData (como **o Object)** não são suportados.


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)