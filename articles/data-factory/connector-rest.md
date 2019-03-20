---
title: Copiar dados de uma origem REST com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de uma origem REST na cloud ou no local para os arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 807a6b38b9f2cbe2a3c8787fe09c2ea14106a942
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864903"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto final REST através do Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um ponto final REST. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

A diferença entre este conector REST [conector HTTP](connector-http.md) e o [conector de tabela de Web](connector-web-table.md) são:

- **Conector REST** especificamente suporte copiam dados a partir de RESTful APIs; 
- **Conector HTTP** é genérico para recuperar dados a partir de qualquer ponto final de HTTP, por exemplo, para transferir o ficheiro. Para que este conector REST fica disponível, pode ocorrer ao utilizar o conector HTTP para copiar dados a partir da API RESTful, que é suportado, mas menos funcional comparando com o conector REST.
- **Conector de tabela de Web** extrações de tabela conteúdo a partir de uma página da Web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de uma origem REST para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector genérico do REST suporta:

- Obter dados a partir de um ponto final REST utilizando o **Obtenha** ou **POST** métodos.
- Recuperação de dados ao utilizar uma das autenticações seguintes: **Anónimo**, **básica**, **principal de serviço do AAD**, e **geridos identidades para recursos do Azure**.
- **[Paginação](#pagination-support)**  nas REST APIs.
- Copiar a resposta JSON de REST [como-é](#export-json-response-as-is) ou analisá-lo utilizando [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping). Payload de resposta no **JSON** é suportada.

> [!TIP]
> Para testar um pedido de obtenção de dados antes de configurar o conector REST no Data Factory, saiba mais sobre a especificação de API do cabeçalho e requisitos de corpo. Pode usar ferramentas como o Postman ou um navegador da web para validar.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector REST.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço REST ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **HttpServer**. | Sim |
| url | O URL de base do serviço REST. | Sim |
| enableServerCertificateValidation | Se é necessário validar o certificado SSL de lado do servidor ao ligar-se para o ponto final. | Não<br /> (a predefinição é **true**) |
| authenticationType | Tipo de autenticação utilizado para ligar ao serviço REST. Valores permitidos são **anónimo**, **básica**, **AadServicePrincipal** e **ManagedServiceIdentity**. Consulte as secções correspondentes abaixo sobre mais propriedades e exemplos, respetivamente. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou um Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, esta propriedade usa o padrão do Runtime de integração do Azure. |Não |

### <a name="use-basic-authentication"></a>Utilize a autenticação básica

Definir o **authenticationType** propriedade **básica**. Além de propriedades genéricas que são descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final REST. | Sim |
| palavra-passe | A palavra-passe para o utilizador (o **nome de utilizador** valor). Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "HttpServer",
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

### <a name="use-aad-service-principal-authentication"></a>Utilizar a autenticação de principal de serviço do AAD

Definir o **authenticationType** propriedade **AadServicePrincipal**. Além de propriedades genéricas que são descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique o ID de cliente. da aplicação do Azure Active Directory | Sim |
| servicePrincipalKey | Especifique a chave da aplicação do Azure Active Directory. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especificar o recurso do AAD que está a pedir para autorização, por exemplo, `https://management.core.windows.net`.| Sim |

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

### <a name="managed-identity"></a> Utilizar identidades geridas para a autenticação de recursos do Azure

Definir o **authenticationType** propriedade **ManagedServiceIdentity**. Além de propriedades genéricas que são descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| aadResourceId | Especificar o recurso do AAD que está a pedir para autorização, por exemplo, `https://management.core.windows.net`.| Sim |

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

Esta seção fornece uma lista de propriedades que suporta o conjunto de dados REST. 

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados de REST, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **RestResource**. | Sim |
| relativeUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não for especificada, é utilizado apenas o URL especificado na definição do serviço ligado. | Não |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** (predefinição) e **Post**. | Não |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestBody | O corpo do pedido HTTP. | Não |
| paginationRules | As regras de paginação para compor próximas solicitações de página. Consulte a [suporte de paginação](#pagination-support) secção em detalhes. | Não |

**Exemplo 1: Usando o método Get com a paginação**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Exemplo 2: Usando o método Post**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suporta a origem REST.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST como origem

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **RestSource**. | Sim |
| httpRequestTimeout | O tempo limite (o **TimeSpan** valor) para o pedido HTTP para obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. O valor predefinido é **01:00:40**.  | Não |
| requestInterval | O tempo de espera antes de enviar o pedido para a página seguinte. O valor predefinido é **00:00:01** |  Não |

**Exemplo**

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
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Suporte de paginação

Normalmente, a REST API limitar seu tamanho de payload de resposta de uma única solicitação num número razoável; tempo para retornar a grande quantidade de dados, ele divide o resultado em várias páginas e requer que os autores de chamadas enviar pedidos consecutivos para obter a página seguinte do resultado. Normalmente, o pedido para uma página é dinâmica e compostos por informações retornadas da resposta da página anterior.

Este conector genérico do REST suporta os seguintes padrões de paginação: 

* URL absoluto ou relativo do próximo pedido = valor da propriedade no corpo de resposta atual
* URL absoluto ou relativo do próximo pedido = valor do cabeçalho nos cabeçalhos de resposta atual
* Parâmetro de consulta do pedido seguinte = valor da propriedade no corpo de resposta atual
* Parâmetro de consulta do pedido seguinte = valor do cabeçalho nos cabeçalhos de resposta atual
* Cabeçalho do pedido seguinte = valor da propriedade no corpo de resposta atual
* Cabeçalho do pedido seguinte = valor do cabeçalho nos cabeçalhos de resposta atual

**Regras de paginação** são definidos como um dicionário no conjunto de dados que contêm um ou mais pares de chave-valor diferencia maiúsculas de minúsculas. A configuração será utilizada para gerar o pedido a partir da segunda página. O conector será a iteração é interrompida quando obtém o código de estado HTTP 204 (sem conteúdo) ou qualquer um da expressão de JSONPath na "paginationRules" devolve um valor nulo.

**Suportado chaves** nas regras de paginação:

| Chave | Descrição |
|:--- |:--- |
| AbsoluteUrl | Indica a URL para emitir o pedido seguinte. Pode ser **URL absoluto ou relativo URL**. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter" é definida pelo utilizador que referencia um nome de parâmetro de consulta no seguinte URL de pedido HTTP. |
| Headers.*request_header* OR Headers['request_header'] | "request_header" é definida pelo utilizador que referencia um nome de cabeçalho na próxima solicitação HTTP. |

**Valores suportados** nas regras de paginação:

| Value | Descrição |
|:--- |:--- |
| Headers.*response_header* OR Headers['response_header'] | "response_header" é definida pelo utilizador que referencia um nome de cabeçalho na resposta HTTP atual, o valor que será utilizado para emitir o pedido seguinte. |
| Uma expressão de JSONPath começando com "$" (que representa a raiz do corpo da resposta) | O corpo da resposta deve conter apenas um objeto JSON. A expressão de JSONPath deverá devolver um valor único primitivo, que será utilizado para emitir o pedido seguinte. |

**Exemplo:**

Graph API do Facebook retorna a resposta na estrutura seguinte, em que casos seguinte do URL da página é representado no ***paging.next***:

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

A configuração de conjunto de dados REST correspondente especialmente a `paginationRules` é o seguinte:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Exportar resposta JSON como-é

Pode utilizar este conector REST para exportar resposta JSON da API de REST como-consiste em vários arquivos baseados em ficheiros. Para alcançar essa cópia sem esquema, ignore a "estrutura" (também denominado *esquema*) secção no conjunto de dados e o mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados de ponto final REST para o sink de tabela, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
