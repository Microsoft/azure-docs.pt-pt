---
title: Copiar dados de fontes OData utilizando a Azure Data Factory
description: Saiba como copiar dados de fontes OData para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jingwang
ms.openlocfilehash: 9dd86b4982edf5d206e64431a5e1458c4b848e9e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968500"
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
- Copiar dados utilizando uma das seguintes autenticações: **Anónimo,** **Básico,** **Windows** e **principal serviço AAD**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas de um conector OData.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado ao OData:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **OData**. |Yes |
| url | O URL raiz do serviço OData. |Yes |
| authenticationType | O tipo de autenticação utilizado para ligar à fonte OData. Os valores permitidos são **Anónimos,** **Básicos,** **Windows** e **AadServicePrincipal**. O OAuth baseado no utilizador não é suportado. Pode ainda configurar cabeçalhos de autenticação em `authHeader` propriedade.| Yes |
| authHeaders | Cabeçalhos adicionais de pedido DE HTTP para autenticação.<br/> Por exemplo, para utilizar a autenticação da chave API, pode selecionar o tipo de autenticação como "Anónimo" e especificar a tecla API no cabeçalho. | No |
| userName | Especifique **o nome do utilizador** se utilizar a autenticação Básica ou o Windows. | No |
| palavra-passe | Especifique a **palavra-passe** para a conta de utilizador especificada para **o nome de utilizador**. Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) | No |
| servicePrincipalId | Especificar o ID do cliente do Azure Ative Directory. | No |
| aadServicePrincipalCredentialType | Especifique o tipo de credencial a utilizar para a autenticação principal do serviço. Os valores permitidos são: `ServicePrincipalKey` ou `ServicePrincipalCert` . | No |
| servicePrincipalKey | Especifique a chave da aplicação do Diretório Ativo Azure. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| serviçoPrincipalEmbeddedCert | Especifique o certificado codificado base64 da sua aplicação registada no Diretório Ativo Azure. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| serviçoPrincipalEmbeddedCertPassword | Especifique a palavra-passe do seu certificado se o seu certificado estiver protegido com uma senha. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md).  | No|
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | No |
| aadResourceId | Especifique o recurso AAD que está a solicitar para autorização.| No |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação AAD está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | No |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |No |

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

**Exemplo 6: Utilização da autenticação da chave API**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades que o conjunto de dados OData suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados do OData, defina **a** propriedade tipo do conjunto de dados para **ODataResource**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **ODataResource**. | Yes |
| caminho | O caminho para o recurso OData. | Yes |

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
| tipo | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida como **ODataSource**. | Yes |
| consulta | Opções de consulta OData para filtragem de dados. Exemplo: `"$select=Name,Description&$top=5"`.<br/><br/>**Nota**: O conector OData copia os dados do URL combinado: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` . Para obter mais informações, consulte [os componentes URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. Se não for especificado, o valor predefinido é **00:30:00** (30 minutos). | No |

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
| Edm.String | String |
| Edm.Tempo | TimeSpan |
| Edm.DateTimeOffset | Início de execução de tempo de data |

> [!NOTE]
> Os tipos de dados complexos oData (como **o Object)** não são suportados.

## <a name="copy-data-from-project-online"></a>Copiar dados do Project Online

Para copiar dados do Project Online, pode utilizar o conector OData e um token de acesso obtido a partir de ferramentas como o Carteiro.

> [!CAUTION]
> O token de acesso expira em 1 hora por padrão, você precisa obter um novo token de acesso quando expirar.

1. Use **o Carteiro** para obter o token de acesso:

   1. Navegue para o separador **autorização** no site do Carteiro.
   1. Na caixa **Tipo,** selecione **OAuth 2.0**, e nos **dados** de autorização adicionar para caixa, selecione **Cabeçalhos de Pedido**.
   1. Preencha as seguintes informações na página **Configure New Token** para obter um novo token de acesso: 
      - **Tipo de concessão**: Código **de Autorização Selecionado**.
      - **URL de retorno:** Insira `https://www.localhost.com/` . 
      - **URL Auth**: Insira `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com` . Substitua `<your tenant name>` pelo seu próprio nome de inquilino. 
      - **Acesso TOKen URL**: Insira `https://login.microsoftonline.com/common/oauth2/token` .
      - **ID do cliente:** Insira o seu ID principal de serviço AAD.
      - **Segredo do Cliente:** Insira o seu principal segredo de serviço.
      - **Autenticação do cliente**: Selecione **Enviar como cabeçalho Básico de Auth**.
     
   1. Ser-lhe-á pedido que faça login com o seu nome de utilizador e senha.
   1. Assim que tiver o seu token de acesso, por favor, copie e guarde-o para o próximo passo.
   
    [![Use o Carteiro para obter o token de acesso](./media/connector-odata/odata-project-online-postman-access-token-inline.png)](./media/connector-odata/odata-project-online-postman-access-token-expanded.png#lightbox)

1. Criar o serviço ligado ao OData:
    - **URL de serviço**: Insira `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata` . Substitua `<your tenant name>` pelo seu próprio nome de inquilino. 
    - **Tipo de autenticação**: Selecione **Anonymous**.
    - **Cabeçalhos auth:**
        - **Nome da propriedade**: Escolha **a Autorização.**
        - **Valor**: Introduza o **token** de acesso copiado do passo 1.
    - Teste o serviço ligado.

    ![Criar serviço ligado ao OData](./media/connector-odata/odata-project-online-linked-service.png)

1. Criar o conjunto de dados OData:
    1. Crie o conjunto de dados com o serviço ligado ao OData criado no passo 2.
    1. Dados de pré-visualização.
 
    ![Dados de pré-visualização](./media/connector-odata/odata-project-online-preview-data.png)
 


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)