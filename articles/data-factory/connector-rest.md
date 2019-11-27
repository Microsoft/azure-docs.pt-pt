---
title: Copiar dados de uma fonte REST usando Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou fonte REST local para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 1178c18b29c5e38d33e51ff0da5db683990daed3
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546959"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto de extremidade REST usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um ponto de extremidade REST. O artigo se baseia na [atividade de cópia no Azure data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

A diferença entre esse conector REST, o [conector http](connector-http.md) e o [conector de tabela da Web](connector-web-table.md) são:

- O **conector REST** oferece suporte especificamente à cópia de dados de APIs RESTful; 
- O **conector http** é genérico para recuperar dados de qualquer ponto de extremidade http, por exemplo, para baixar o arquivo. Antes que esse conector REST fique disponível, você pode usar o conector HTTP para copiar dados da API RESTful, que tem suporte, mas menos funcional comparando o conector REST.
- O **conector de tabela da Web** extrai o conteúdo da tabela de uma página HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de uma fonte REST para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte [armazenamentos e formatos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector REST genérico dá suporte a:

- Recuperando dados de um ponto de extremidade REST usando os métodos **Get** ou **post** .
- Recuperando dados usando uma das seguintes autenticações: **anônima**, **básica**, entidade de **serviço do AAD**e **identidades gerenciadas para recursos do Azure**.
- **[Paginação](#pagination-support)** nas APIs REST.
- Copiar a resposta JSON REST [como está](#export-json-response-as-is) ou analisá-la usando o [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping). Há suporte apenas para carga de resposta em **JSON** .

> [!TIP]
> Para testar uma solicitação de recuperação de dados antes de configurar o conector REST no Data Factory, saiba mais sobre a especificação de API para requisitos de cabeçalho e corpo. Você pode usar ferramentas como o postmaster ou um navegador da Web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas para o conector REST.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado REST:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **RestService**. | Sim |
| url | A URL base do serviço REST. | Sim |
| enableServerCertificateValidation | Se o certificado SSL do lado do servidor deve ser validado ao se conectar ao ponto de extremidade. | Não<br /> (o padrão é **true**) |
| authenticationType | Tipo de autenticação usado para se conectar ao serviço REST. Os valores permitidos são **Anonymous**, **Basic**, **AadServicePrincipal** e **ManagedServiceIdentity**. Consulte as seções correspondentes abaixo em mais propriedades e exemplos, respectivamente. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, essa propriedade usará o Azure Integration Runtime padrão. |Não |

### <a name="use-basic-authentication"></a>Usar autenticação básica

Defina a propriedade **authenticationType** como **básica**. Além das propriedades genéricas que são descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usado para acessar o ponto de extremidade REST. | Sim |
| palavra-passe | A senha do usuário (o valor de **nome de usuário** ). Marque este campo como um tipo **SecureString** para armazená-lo com segurança em data Factory. Você também pode [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Usar a autenticação de entidade de serviço do AAD

Defina a propriedade **authenticationType** como **AadServicePrincipal**. Além das propriedades genéricas que são descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo Azure Active Directory. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo Azure Active Directory. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização, por exemplo, `https://management.core.windows.net`.| Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Usar identidades gerenciadas para a autenticação de recursos do Azure

Defina a propriedade **authenticationType** como **ManagedServiceIdentity**. Além das propriedades genéricas que são descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização, por exemplo, `https://management.core.windows.net`.| Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista das propriedades que o conjunto de DataSet do REST dá suporte. 

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do REST, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **RestResource**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente a URL especificada na definição de serviço vinculado é usada. O conector HTTP copia dados da URL combinada: `[URL specified in linked service]/[relative URL specified in dataset]`. | Não |

Se você estivesse Configurando `requestMethod`, `additionalHeaders`, `requestBody` e `paginationRules` no conjunto de uma, ele ainda terá suporte como está, enquanto você deve usar o novo modelo na origem da atividade no futuro.

**Exemplo:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista das propriedades às quais a fonte REST dá suporte.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST como fonte

As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **REST**. | Sim |
| requestMethod | O método HTTP. Os valores permitidos são **Get** (padrão) e **post**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| paginationRules | As regras de paginação para compor solicitações da próxima página. Consulte a seção [suporte à paginação](#pagination-support) em detalhes. | Não |
| httpRequestTimeout | O tempo limite (o valor de **TimeSpan** ) para a solicitação HTTP obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor padrão é **00:01:40**.  | Não |
| requestInterval | O tempo de espera antes de enviar a solicitação para a próxima página. O valor padrão é **00:00:01** |  Não |

>[!NOTE]
>O conector REST ignora qualquer cabeçalho "Accept" especificado em `additionalHeaders`. Como o conector REST só dá suporte à resposta em JSON, ele gerará automaticamente um cabeçalho de `Accept: application/json`.

**Exemplo 1: usando o método Get com paginação**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo 2: usando o método post**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Suporte à paginação

Normalmente, a API REST limita seu tamanho de carga de resposta de uma única solicitação em um número razoável; Embora seja possível retornar uma grande quantidade de dados, ele divide o resultado em várias páginas e exige que os chamadores enviem solicitações consecutivas para obter a próxima página do resultado. Normalmente, a solicitação de uma página é dinâmica e composta pelas informações retornadas da resposta da página anterior.

Este conector REST genérico dá suporte aos seguintes padrões de paginação: 

* Valor da propriedade da URL absoluta ou relativa da próxima solicitação no corpo da resposta atual
* URL absoluta ou relativa da próxima solicitação = valor do cabeçalho nos cabeçalhos de resposta atuais
* Parâmetro de consulta da próxima solicitação = valor da propriedade no corpo da resposta atual
* Parâmetro de consulta da próxima solicitação = valor do cabeçalho nos cabeçalhos de resposta atuais
* Cabeçalho da próxima solicitação = valor da propriedade no corpo da resposta atual
* Cabeçalho da próxima solicitação = valor do cabeçalho nos cabeçalhos de resposta atuais

**As regras de paginação** são definidas como um dicionário no conjunto de dados que contém um ou mais pares chave-valor que diferenciam maiúsculas de minúsculas. A configuração será usada para gerar a solicitação a partir da segunda página. O conector deixará de iteração quando obter o código de status HTTP 204 (sem conteúdo) ou qualquer uma das expressões JSONPath em "paginationRules" retornar NULL.

**Chaves com suporte** nas regras de paginação:

| Chave | Descrição |
|:--- |:--- |
| AbsoluteUrl | Indica a URL para emitir a próxima solicitação. Ele pode ser **uma URL absoluta ou relativa**. |
| QueryParameters. *request_query_parameter* OU QueryParameters [' request_query_parameter '] | "request_query_parameter" é definido pelo usuário, que faz referência a um nome de parâmetro de consulta na próxima URL de solicitação HTTP. |
| Conector. *request_header* OU cabeçalhos [' request_header '] | "request_header" é definido pelo usuário, que faz referência a um nome de cabeçalho na próxima solicitação HTTP. |

**Valores com suporte** nas regras de paginação:

| Valor | Descrição |
|:--- |:--- |
| Conector. *response_header* OU cabeçalhos [' response_header '] | "response_header" é definido pelo usuário que faz referência a um nome de cabeçalho na resposta HTTP atual, o valor que será usado para emitir a próxima solicitação. |
| Uma expressão JSONPath que começa com "$" (representando a raiz do corpo da resposta) | O corpo da resposta deve conter apenas um objeto JSON. A expressão JSONPath deve retornar um único valor primitivo, que será usado para emitir a próxima solicitação. |

**Exemplo:**

O Facebook API do Graph retorna a resposta na estrutura a seguir, caso em que a URL da próxima página é representada na ***paginação. em seguida***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

A configuração de origem da atividade de cópia REST correspondente especialmente a `paginationRules` é a seguinte:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>Exportar resposta JSON como está

Você pode usar esse conector REST para exportar a resposta JSON da API REST no estado em que se encontra em vários repositórios baseados em arquivo. Para obter essa cópia independente de esquema, ignore a seção "estrutura" (também chamada de *esquema*) no conjunto de dados e no mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do ponto de extremidade REST para o coletor tabular, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores em Azure Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
