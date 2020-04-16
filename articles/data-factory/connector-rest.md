---
title: Copiar dados de uma fonte REST utilizando a Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou no local fonte REST para lojas de dados de sink suportadas usando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 2657f1998e3ca908bc52166154ac3353e1e5a66b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415033"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto final REST utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um ponto final REST. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

A diferença entre este conector REST, [o conector HTTP](connector-http.md) e o [conector](connector-web-table.md) da tabela Web são:

- **O conector REST** suporta especificamente a cópia de dados de APIs restful; 
- **O conector HTTP** é genérico para recuperar dados de qualquer ponto final http, por exemplo, para descarregar ficheiros. Antes de este conector REST ficar disponível, pode por acaso utilizar o conector HTTP para copiar dados da API RESTful, que é suportado mas menos funcional em comparação com o conector REST.
- **O conector** de mesa web extrai o conteúdo da tabela a partir de uma página web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de uma fonte REST para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector genérico REST suporta:

- Recuperando dados de um ponto final REST utilizando os métodos **GET** ou **POST.**
- Recuperação de dados utilizando uma das seguintes autenticações: **Anónimo,** **Básico,** **AAD principal,** e **identidades geridas para recursos Azure**.
- **[Paginação](#pagination-support)** nas APIs do REST.
- Copiando a resposta REST JSON como está ou [analisá-la](#export-json-response-as-is) utilizando [o mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping). Apenas a carga útil de resposta na **JSON** é suportada.

> [!TIP]
> Para testar um pedido de recuperação de dados antes de configurar o conector REST na Data Factory, conheça a especificação da API para os requisitos do cabeçalho e do corpo. Pode utilizar ferramentas como o Carteiro ou um navegador web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas do conector REST.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao REST:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **RestService**. | Sim |
| url | O URL base do serviço REST. | Sim |
| permitirServerCertificateValidação | Quer validar o certificado TLS/SSL do lado do servidor ao ligar-se ao ponto final. | Não<br /> (o padrão é **verdadeiro)** |
| authenticationType | Tipo de autenticação utilizada para ligar ao serviço REST. Os valores permitidos são **Anónimos,** **Básicos,** **AadServicePrincipal** e **ManagedServiceIdentity**. Consulte as secções correspondentes abaixo sobre mais propriedades e exemplos, respectivamente. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificada, esta propriedade utiliza o tempo de execução de integração azure padrão. |Não |

### <a name="use-basic-authentication"></a>Utilizar a autenticação básica

Detete a propriedade **autenticaçãoType** para **Basic**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| userName | O nome do utilizador a utilizar para aceder ao ponto final do REST. | Sim |
| palavra-passe | A palavra-passe para o utilizador (o valor **userName).** Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre de Chaves Azure.](store-credentials-in-key-vault.md) | Sim |

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

### <a name="use-aad-service-principal-authentication"></a>Utilize a autenticação principal do serviço AAD

Detete a propriedade **autenticaçãoType** para **AadServicePrincipal**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| serviçoPrincipalId | Especifique o ID do cliente do cliente da aplicação Azure Ative Diretório. | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação Azure Ative Directory. Marque este campo como um **SecureString** para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| aadResourceId | Especifique o recurso AAD que está `https://management.core.windows.net`a solicitar para autorização, por exemplo.| Sim |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Utilize identidades geridas para autenticação de recursos Azure

Detete a propriedade **autenticaçãoType** para **ManagedServiceIdentity**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| aadResourceId | Especifique o recurso AAD que está `https://management.core.windows.net`a solicitar para autorização, por exemplo.| Sim |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades que o conjunto de dados REST suporta. 

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md) 

Para copiar dados do REST, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do conjunto de dados deve ser definida para **RestResource**. | Sim |
| parenteUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não é especificada, apenas é utilizado o URL especificado na definição de serviço ligado. O conector HTTP copia `[URL specified in linked service]/[relative URL specified in dataset]`dados do URL combinado: . | Não |

Se estava `requestMethod`a `additionalHeaders` `requestBody` definir `paginationRules` , e no conjunto de dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo na fonte de atividade.

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Esta secção fornece uma lista de propriedades que a fonte rest suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST como fonte

As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **RestSource**. | Sim |
| solicitarMétodo | O método HTTP. Os valores permitidos são **Get** (padrão) e **Post**. | Não |
| cabeçalhos adicionais | Cabeçalhos adicionais de pedido HTTP. | Não |
| requestBody | O corpo para o pedido http. | Não |
| paginaçãoRegras | As regras de paginação para compor os pedidos da próxima página. Consulte a secção de suporte de [paginação](#pagination-support) sobre detalhes. | Não |
| httpRequestTimeout | O prazo (o valor **TimeSpan)** para o pedido http para obter uma resposta. Este valor é o tempo de saída para obter uma resposta, não o tempo de paragem para ler dados de resposta. O valor predefinido é **00:01:40**.  | Não |
| pedidoIntervalo | O tempo para esperar antes de enviar o pedido para a próxima página. O valor padrão é **00:00:01** |  Não |

>[!NOTE]
>O conector REST ignora qualquer `additionalHeaders`cabeçalho "Aceitar" especificado em . Como o conector REST apenas suporta resposta em `Accept: application/json`JSON, ele gerará automaticamente um cabeçalho de .

**Exemplo 1: Utilizar o método Get com paginação**

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

**Exemplo 2: Utilização do método Post**

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

## <a name="pagination-support"></a>Apoio à paginação

Normalmente, a API REST limita a sua carga útil de resposta de um único pedido num número razoável; enquanto devolve uma grande quantidade de dados, divide o resultado em várias páginas e exige que os chamadores enviem pedidos consecutivos para obter a próxima página do resultado. Normalmente, o pedido de uma página é dinâmico e composto pela informação devolvida da resposta da página anterior.

Este conector genérico REST suporta os seguintes padrões de paginação: 

* URL absoluto ou relativo do próximo pedido = valor da propriedade no corpo de resposta atual
* URL absoluto ou relativo do próximo pedido = valor cabeçalho nos cabeçalhos de resposta atual
* Parâmetro de consulta do próximo pedido = valor da propriedade no corpo de resposta atual
* Parâmetro de consulta do próximo pedido = valor cabeçalho nos cabeçalhos de resposta atual
* Cabeçalho do próximo pedido = valor da propriedade no corpo de resposta atual
* Cabeçalho do próximo pedido = valor do cabeçalho nos cabeçalhos de resposta atual

As regras de **paginação** são definidas como um dicionário em conjunto de dados que contém um ou mais pares de valor-chave sensíveis a casos. A configuração será usada para gerar o pedido a partir da segunda página. O conector deixará de iterar quando obtém o código de estado HTTP 204 (Sem Conteúdo), ou qualquer uma das expressões JSONPath em "paginnationRules" declara nulo.

**Chaves suportadas** nas regras de paginação:

| Chave | Descrição |
|:--- |:--- |
| AbsoluteUrl | Indica o URL para emitir o próximo pedido. Pode ser **URL absoluto ou URL relativo**. |
| Consultas Parâmetros. *request_query_parameter* OU QueryParameters['request_query_parameter'] | "request_query_parameter" é definido pelo utilizador, que refere um nome de parâmetro de consulta no próximo URL de pedido http. |
| Cabeçalhos. *request_header* OR Cabeçalhos['request_header'] | "request_header" é definido pelo utilizador quais refere um nome cabeçalho no próximo pedido http. |

**Valores suportados** nas regras de paginação:

| Valor | Descrição |
|:--- |:--- |
| Cabeçalhos. *response_header* OR Cabeçalhos['response_header'] | "response_header" é definido pelo utilizador quais as referências de um nome cabeçalho na resposta http atual, qual o valor será utilizado para emitir o próximo pedido. |
| Uma expressão JSONPath começando com "$" (representando a raiz do corpo de resposta) | O corpo de resposta deve conter apenas um objeto JSON. A expressão JSONPath deve devolver um único valor primitivo, que será usado para emitir o próximo pedido. |

**Exemplo:**

Facebook Graph API devolve resposta na seguinte estrutura, caso em que o URL da página seguinte está representado em ***paging.next***:

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

A configuração de origem `paginationRules` de fonte de origem de origem de cópia de cópia REST correspondente, especialmente a seguinte:

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

## <a name="use-oauth"></a>Use OAuth (Utilizar a OAuth)
Esta secção descreve como usar um modelo de solução para copiar dados do conector REST para o Armazenamento do Lago De dados Azure em formato JSON usando o OAuth. 

### <a name="about-the-solution-template"></a>Sobre o modelo de solução

O modelo contém duas atividades:
- **A** atividade web recupera o token do portador e, em seguida, passa-o para a atividade copy subsequente como autorização.
- **Copiar** dados de atividade do REST para O Armazenamento do Lago De Dados Azure.

O modelo define dois parâmetros:
- **SinkContainer** é o caminho da pasta de raiz para onde os dados são copiados no seu Armazenamento do Lago De Dados Azure. 
- **SinkDirectory** é o caminho do diretório sob a raiz onde os dados são copiados no seu Armazenamento de Lago de Dados Azure. 

### <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá à Cópia de REST ou HTTP utilizando o modelo **OAuth.** Crie uma nova ligação para a Ligação Fonte. 
    ![Criar novas ligações](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Abaixo estão os passos-chave para novas definições de serviço ligado (REST):
    
     1. No URL **base,** especifique o parâmetro de url para o seu próprio serviço REST de origem. 
     2. Para o tipo de **autenticação,** escolha *Anónimo*.
        ![Nova ligação REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Crie uma nova ligação para a Ligação de Destino.  
    ![Nova ligação gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selecione **Utilize este modelo**.
    ![Utilizar este modelo](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Veria o gasoduto criado como mostrado no ![seguinte exemplo: Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selecione atividade **web.** Em **Definições,** **especifique**o URL correspondente, **Método,** **Cabeçalhos**e **Corpo** para recuperar o token do portador da OAuth a partir da API de login do serviço a que pretende copiar dados. O espaço reservado no modelo apresenta uma amostra de OAuth de Diretório Ativo Azure (AAD). Nota Aad autenticação é suportada de forma nativa pelo conector REST, aqui é apenas um exemplo para o fluxo de OAuth. 

    | Propriedade | Descrição |
    |:--- |:--- |:--- |
    | do IdP |Especifique a url para recuperar o token do portador da OAuth. por exemplo, na amostra aqui éhttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Método | O método HTTP. Os valores permitidos são **Post** and **Get**. | 
    | Cabeçalhos | O cabeçalho é definido pelo utilizador, que refere um nome cabeçalho no pedido HTTP. | 
    | Corpo | O corpo para o pedido http. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Na atividade de **dados da Cópia,** selecione o separador *Fonte,* pode ver que o token (access_token) do portador recuperado do passo anterior seria passado para copiar a atividade de dados como **Autorização** em cabeçalhos adicionais. Confirme as definições para seguir as propriedades antes de iniciar uma execução de gasoduto.

    | Propriedade | Descrição |
    |:--- |:--- |:--- | 
    | Método de pedido | O método HTTP. Os valores permitidos são **Get** (padrão) e **Post**. | 
    | Cabeçalhos adicionais | Cabeçalhos adicionais de pedido HTTP.| 

   ![Autenticação de fonte de cópia](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.
   ![Gasoduto](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Quando o gasoduto termina com sucesso, veria o resultado semelhante ![ao seguinte exemplo: Resultado da execução do gasoduto](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Clique no ícone "Output" da coluna WebActivity in **Actions,** e veria o access_token devolvido pelo serviço.

   ![Saída de token](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Clique no ícone "Entrada" da coluna CopyActivity in **Actions,** e veria que o access_token recuperado pela WebActivity é passado para CopyActivity para autenticação. 

    ![Entrada simbólica](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Para evitar que o token seja registado em texto simples, ative "Secure output" na atividade web e "Secure input" na atividade copy.


## <a name="export-json-response-as-is"></a>Exportação JSON resposta as-is

Pode utilizar este conector REST para exportar a resposta REST API JSON como está em várias lojas baseadas em ficheiros. Para obter tal cópia schema-agnóstica, ignore a secção "estrutura" (também chamada *de esquema*) no conjunto de dados e no mapeamento de esquemas na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de schema

Para copiar dados do ponto final do REST para o lavatório tabular, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
