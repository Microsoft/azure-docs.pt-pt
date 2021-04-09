---
title: Copiar dados de e para um ponto final REST utilizando a Azure Data Factory
description: Saiba como copiar dados de uma fonte de nuvem ou no local REST para lojas de dados de sumidouros suportados, ou de uma loja de dados de origem suportada para um lavatório REST utilizando uma atividade de cópia num oleoduto Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: jingwang
ms.openlocfilehash: 6d9bb17e0e68c563c6d8cc18669d8c298d4f267b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951849"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de e para um ponto final REST utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados de e para um ponto final REST. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

A diferença entre este conector REST, [conector HTTP,](connector-http.md)e o [conector de mesa Web](connector-web-table.md) são:

- **O conector REST** suporta especificamente a cópia de dados de APIs RESTful; 
- **O conector HTTP** é genérico para obter dados de qualquer ponto final HTTP, por exemplo, para descarregar ficheiro. Antes deste conector REST pode, por acaso, utilizar o conector HTTP para copiar dados da API RESTful, que é suportado mas menos funcional em comparação com o conector REST.
- **O conector da tabela web** extrai o conteúdo da tabela a partir de uma página web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de uma fonte REST para qualquer loja de dados de lavatórios suportados. Também pode copiar dados de qualquer loja de dados de origem suportada para uma pia REST. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector GENÉRICO REST suporta:

- Copiar dados de um ponto final REST utilizando os métodos **GET** ou **POST** e copiando dados para um ponto final DE REST utilizando os métodos **POST**, **PUT** ou **PATCH.**
- Copiar dados utilizando uma das seguintes autenticações: **Anónima,** **Base,** **principal do serviço AAD,** e **identidades geridas para recursos Azure**.
- **[Paginação](#pagination-support)** nas APIs de REST.
- Para rest como fonte, copiando a resposta REST JSON [como-é](#export-json-response-as-is) ou analise-a utilizando [o mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping). Suporta-se apenas a carga útil de resposta no **JSON.**

> [!TIP]
> Para testar um pedido de recuperação de dados antes de configurar o conector REST na Data Factory, saiba mais sobre a especificação API para os requisitos do cabeçalho e do corpo. Você pode usar ferramentas como o Carteiro ou um navegador web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas do conector REST.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado REST:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **RestService**. | Yes |
| url | O URL base do serviço REST. | Yes |
| enableServerCertificateValidation | Se validar o certificado TLS/SSL do lado do servidor ao ligar-se ao ponto final. | No<br /> (o padrão é **verdadeiro)** |
| authenticationType | Tipo de autenticação utilizada para ligar ao serviço REST. Os valores permitidos são **Anónimos,** **Básicos,** **AadServicePrincipal** e **ManagedServiceIdentity**. O OAuth baseado no utilizador não é suportado. Pode ainda configurar cabeçalhos de autenticação em `authHeader` propriedade. Consulte as secções correspondentes abaixo em mais propriedades e exemplos, respectivamente.| Yes |
| authHeaders | Cabeçalhos adicionais de pedido DE HTTP para autenticação.<br/> Por exemplo, para utilizar a autenticação da chave API, pode selecionar o tipo de autenticação como "Anónimo" e especificar a tecla API no cabeçalho. | No |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, esta propriedade utiliza o tempo de execução de integração Azure predefinido. |No |

### <a name="use-basic-authentication"></a>Utilizar a autenticação básica

Desautense a propriedade **autenticaçãoType** para **Basic**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final REST. | Yes |
| palavra-passe | A palavra-passe para o utilizador (o valor **do nome do utilizador).** Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) | Yes |

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

Desa estaione a propriedade **autenticaçãoType** para **a AadServicePrincipal**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especificar o ID do cliente do Azure Ative Directory. | Yes |
| servicePrincipalKey | Especifique a chave da aplicação do Diretório Ativo Azure. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Yes |
| aadResourceId | Especifique o recurso AAD que está a solicitar para autorização, por exemplo, `https://management.core.windows.net` .| Yes |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação AAD está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | No |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Utilizar identidades geridas para autenticação de recursos Azure

Desaprova a propriedade **autenticaçãoType** para **ManagedServiceIdentity**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| aadResourceId | Especifique o recurso AAD que está a solicitar para autorização, por exemplo, `https://management.core.windows.net` .| Yes |

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

### <a name="using-authentication-headers"></a>Utilização de cabeçalhos de autenticação

Além disso, pode configurar os cabeçalhos de pedido para autenticação juntamente com os tipos de autenticação incorporado.

**Exemplo: Utilização da autenticação da chave API**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
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

Esta secção fornece uma lista de propriedades que o conjunto de dados REST suporta. 

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados da REST, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **RestResource**. | Yes |
| relativoUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não é especificada, apenas é utilizado o URL especificado na definição de serviço ligado. O conector HTTP copia os dados do URL combinado: `[URL specified in linked service]/[relative URL specified in dataset]` . | No |

Se estiver a configurar `requestMethod` , e em conjunto de `additionalHeaders` `requestBody` `paginationRules` dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo em atividade.

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades suportadas pela fonte REST e pia.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>DESCANSE como fonte

As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade de cópia deve ser definida como **RestSource**. | Yes |
| requestMethod | O método HTTP. Os valores permitidos são **GET** (padrão) e **POST**. | No |
| cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais. | No |
| requestCorp | O corpo para o pedido HTTP. | No |
| paginationRules | As regras de paginação para compor os pedidos da próxima página. Consulte a secção [de suporte de paginação](#pagination-support) sobre detalhes. | No |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor predefinido é **00:01:40**.  | No |
| solicitaçãoInterval | O tempo para esperar antes de enviar o pedido para a próxima página. O valor predefinido é **00:00:01** |  No |

>[!NOTE]
>O conector REST ignora qualquer cabeçalho "Aceitar" especificado em `additionalHeaders` . Como o conector REST apenas suporta a resposta em JSON, gerará automaticamente um cabeçalho de `Accept: application/json` .

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

**Exemplo 2: Utilização do método post**

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

### <a name="rest-as-sink"></a>DESCANSE como pia

As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do lavatório de atividade de cópia deve ser definida para **RestSink**. | Yes |
| requestMethod | O método HTTP. Os valores permitidos são **POST** (padrão), **PUT** e **PATCH**. | No |
| cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais. | No |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para escrever os dados. O valor predefinido é **00:01:40**.  | No |
| solicitaçãoInterval | O intervalo entre diferentes pedidos em milissegundo. O valor do intervalo de pedido deve ser um número entre [10,60000]. |  No |
| httpCompressionType | Tipo de compressão HTTP para utilizar ao enviar dados com o Nível de Compressão Ideal. Os valores permitidos não são **nenhum** e **gzip**. | No |
| escreverBatchSize | Número de registos para escrever para o lavatório REST por lote. O valor predefinido é 10000. | No |

O conector REST como pia funciona com as APIs REST que aceitam JSON. Os dados serão enviados em JSON com o seguinte padrão. Se necessário, pode utilizar o [mapeamento de esquema de](copy-activity-schema-and-type-mapping.md#schema-mapping) atividade de cópia para remodelar os dados de origem de modo a estar em conformidade com a carga útil esperada pela API REST.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Suporte de paginação

Ao copiar dados de APIs rest, normalmente, a API REST limita o tamanho da carga útil de resposta de um único pedido com um número razoável; ao mesmo tempo que devolve uma grande quantidade de dados, divide o resultado em várias páginas e requer que os chamadores enviem pedidos consecutivos para obter a próxima página do resultado. Normalmente, o pedido de uma página é dinâmico e composto pela informação devolvida a partir da resposta da página anterior.

Este conector GENÉRICO REST suporta os seguintes padrões de paginação: 

* URL absoluto ou relativo do próximo pedido = valor da propriedade no corpo de resposta atual
* URL absoluto ou relativo do próximo pedido = valor do cabeçalho de cabeçalho de resposta atual
* Parâmetro de consulta do próximo pedido = valor da propriedade no corpo de resposta atual
* Parâmetro de consulta do próximo pedido = valor do cabeçalho nos cabeçalhos de resposta atuais
* Cabeçalho do próximo pedido = valor da propriedade no corpo de resposta atual
* Cabeçalho do próximo pedido = valor do cabeçalho nos cabeçalhos de resposta atuais

**As regras de paginação** são definidas como um dicionário no conjunto de dados, que contém um ou mais pares de valor-chave sensíveis a casos. A configuração será usada para gerar o pedido a partir da segunda página. O conector deixará de iterar quando receber o código de estado HTTP 204 (Sem Conteúdo), ou qualquer das expressões JSONPath em "paginationRules" retorna de nulo.

**Chaves suportadas** nas regras de paginação:

| Chave | Descrição |
|:--- |:--- |
| AbsoluteUrl | Indica o URL para emitir o próximo pedido. Pode ser **URL absoluto ou URL relativo.** |
| ConsultasParametros. *request_query_parameter* OU ConsultasParameters ['request_query_parameter'] | "request_query_parameter" é definido pelo utilizador, que faz referência a um nome de parâmetro de consulta no URL de pedido http seguinte. |
| Cabeçalhos. *request_header* OU Cabeçalhos ['request_header'] | "request_header" é definido pelo utilizador, que faz referência a um nome de cabeçalho no próximo pedido HTTP. |

**Valores suportados** nas regras de paginação:

| Valor | Descrição |
|:--- |:--- |
| Cabeçalhos. *response_header* OU Cabeçalhos ['response_header'] | "response_header" é definido pelo utilizador, que faz referência a um nome de cabeçalho na resposta HTTP atual, do qual será utilizado o valor para emitir o próximo pedido. |
| Uma expressão JSONPath a começar por "$" (representando a raiz do corpo de resposta) | O corpo de resposta deve conter apenas um objeto JSON. A expressão JSONPath deve devolver um único valor primitivo, que será usado para emitir o próximo pedido. |

**Exemplo:**

A API do Facebook Graph devolve a resposta na seguinte estrutura, caso em que o URL da próxima página está representado em ***paging.next***:

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

A configuração correspondente da fonte de origem da cópia REST, especialmente a `paginationRules` seguinte:

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
Esta secção descreve como usar um modelo de solução para copiar dados do conector REST para o Azure Data Lake Storage em formato JSON usando OAuth. 

### <a name="about-the-solution-template"></a>Sobre o modelo de solução

O modelo contém duas atividades:
- **A** atividade web recupera o token do portador e, em seguida, passa-o para a atividade de Cópia subsequente como autorização.
- **Copiar** a atividade copia dados do REST para o Azure Data Lake Storage.

O modelo define dois parâmetros:
- **SinkContainer** é o caminho da pasta raiz onde os dados são copiados no seu Azure Data Lake Storage. 
- **SinkDirectory** é o caminho do diretório sob a raiz onde os dados são copiados no seu Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá ao Copy from REST ou HTTP utilizando o modelo **OAuth.** Criar uma nova ligação para a Ligação Origem. 
    ![Criar novas ligações](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Abaixo estão os passos-chave para as novas definições de serviços ligados (REST):
    
     1. Em **URL base**, especifique o parâmetro url para o seu próprio serviço REST de origem. 
     2. Para **tipo de autenticação,** escolha *Anónimo*.
        ![Nova ligação REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Criar uma nova ligação para a Ligação de Destino.  
    ![Nova ligação à Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selecione **Utilize este modelo.**
    ![Utilizar este modelo](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Veria o gasoduto criado como mostrado no seguinte exemplo:  ![ Screenshot mostra o gasoduto criado a partir do modelo.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selecione atividade **web.** Em Definições , **especifique** o **URL** correspondente , **Método**, **Cabeçalhos** e **Corpo** para recuperar o token portador de OAuth a partir da API de login do serviço a partir do qual pretende copiar dados. O espaço reservado no modelo apresenta uma amostra de Azure Ative Directory (AAD) OAuth. Nota Aad a autenticação é suportada de forma nativa pelo conector REST, aqui é apenas um exemplo para o fluxo OAuth. 

    | Propriedade | Descrição |
    |:--- |:--- |
    | URL |Especifique o url para recuperar o símbolo do portador de OAuth de. por exemplo, na amostra aqui é https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Método | O método HTTP. Os valores permitidos são **Post** and **Get**. | 
    | Cabeçalhos | O cabeçalho é definido pelo utilizador, que faz referência a um nome de cabeçalho no pedido HTTP. | 
    | Corpo | O corpo para o pedido HTTP. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Na atividade **de dados copy,** selecione'Source tab, você pode ver que o token do portador (access_token) recuperado do passo anterior seria passado para copiar a atividade de dados como  **Autorização** sob cabeçalhos adicionais. Confirme as definições para seguir as propriedades antes de iniciar uma corrida ao gasoduto.

    | Propriedade | Descrição |
    |:--- |:--- |
    | Método de pedido | O método HTTP. Os valores permitidos são **Get** (predefinido) e **Post**. | 
    | Cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais.| 

   ![Autenticação de fonte de cópia](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.
   ![Curso de gasoduto](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Quando o gasoduto estiver concluído com sucesso, veria o resultado semelhante ao seguinte exemplo: ![ Resultado da execução do gasoduto](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Clique no ícone "Output" da coluna WebActivity in **Actions,** verá o access_token devolvido pelo serviço.

   ![Saída simbólica](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Clique no ícone "Input" da coluna CopyActivity in **Actions,** verá que o access_token recuperado pela WebActivity é passado para CopyActivity para autenticação. 

    ![Entrada simbólica](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Para evitar que o token seja registado em texto simples, ative a "saída segura" na atividade web e a "entrada segura" na atividade copy.


## <a name="export-json-response-as-is"></a>Resposta JSON de exportação como-é

Pode utilizar este conector REST para exportar a resposta REST API JSON como-é para várias lojas baseadas em ficheiros. Para obter tal cópia esquema-agnóstico, salte a secção "estrutura" (também chamada *de esquema)* no conjunto de dados e no mapeamento de esquemas na atividade da cópia.

## <a name="schema-mapping"></a>Mapeamento de Schema

Para copiar os dados do ponto final DO REST para a pia tabular, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
