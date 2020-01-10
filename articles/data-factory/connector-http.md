---
title: Copiar dados de uma origem HTTP usando Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou de uma origem HTTP local para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 7532db883b6267c402e380d865c917d16a7052da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440623"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto de extremidade HTTP usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um ponto de extremidade HTTP. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

A diferença entre esse conector HTTP, o [conector REST](connector-rest.md) e o [conector de tabela da Web](connector-web-table.md) são:

- O **conector REST** especificamente dá suporte à cópia de dados de APIs RESTful; 
- O **conector http** é genérico para recuperar dados de qualquer ponto de extremidade http, por exemplo, para baixar o arquivo. Antes que o conector REST fique disponível, você pode usar o conector HTTP para copiar dados da API RESTful, que tem suporte, mas menos funcional comparando o conector REST.
- O **conector de tabela da Web** extrai o conteúdo da tabela de uma página HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector HTTP tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de uma origem HTTP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar este conector HTTP para:

- Recupere dados de um ponto de extremidade HTTP/S usando os métodos HTTP **Get** ou **post** .
- Recupere dados usando uma das seguintes autenticações: **anônima**, **básica**, **Digest**, **Windows**ou **ClientCertificate**.
- Copie a resposta HTTP como está ou analise-a usando [formatos de arquivo com suporte e codecs de compactação](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Para testar uma solicitação HTTP para recuperação de dados antes de configurar o conector HTTP no Data Factory, saiba mais sobre a especificação de API para requisitos de cabeçalho e corpo. Você pode usar ferramentas como o postmaster ou um navegador da Web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Começar

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas para o conector HTTP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado de HTTP:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **HttpServer**. | Sim |
| url | A URL base para o servidor Web. | Sim |
| enableServerCertificateValidation | Especifique se a validação de certificado SSL do servidor deve ser habilitada quando você se conectar a um ponto de extremidade HTTP. Se o servidor HTTPS usar um certificado autoassinado, defina essa propriedade como **false**. | Não<br /> (o padrão é **true**) |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anonymous**, **Basic**, **Digest**, **Windows**e **ClientCertificate**. <br><br> Consulte as seções que seguem esta tabela para obter mais propriedades e exemplos de JSON para esses tipos de autenticação. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação básica, Digest ou Windows

Defina a propriedade **authenticationType** como **Basic**, **Digest**ou **Windows**. Além das propriedades genéricas que são descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usado para acessar o ponto de extremidade HTTP. | Sim |
| palavra-passe | A palavra-passe para o utilizador (o **userName** valor). Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Usando a autenticação ClientCertificate

Para usar a autenticação ClientCertificate, defina a propriedade **authenticationType** como **ClientCertificate**. Além das propriedades genéricas que são descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificado codificados em base64. | Especifique **embeddedCertData** ou **certThumbprint**. |
| certThumbprint | A impressão digital do certificado que está instalado no repositório de certificados de seu computador Integration Runtime auto-hospedado. Aplica-se somente quando o tipo auto-hospedado de Integration Runtime é especificado na propriedade **connectVia** . | Especifique **embeddedCertData** ou **certThumbprint**. |
| palavra-passe | A senha que está associada ao certificado. Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |

Se você usar o **certThumbprint** para autenticação e o certificado estiver instalado no repositório pessoal do computador local, conceda permissões de leitura para o Integration Runtime de hospedagem interna:

1. Abra o MMC (console de gerenciamento Microsoft). Adicione o snap-in de **certificados** que se destina ao **computador local**.
2. Expanda **certificados** > **pessoal**e, em seguida, selecione **certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **todas as tarefas** > **gerenciar chaves privadas**.
3. Na guia **segurança** , adicione a conta de usuário sob a qual o serviço de Host Integration Runtime (DIAHostService) está em execução, com acesso de leitura ao certificado.

**Exemplo 1: usando certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: usando embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HTTP em configurações de `location` no conjunto de entrada baseado em formato:

| Propriedade    | Descrição                                                  | Obrigatório |
| ----------- | ------------------------------------------------------------ | -------- |
| tipo        | A propriedade Type em `location` no conjunto de texto deve ser definida como **HttpServerLocation**. | Sim      |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. O conector HTTP copia dados da URL combinada: `[URL specified in linked service]/[relative URL specified in dataset]`.   | Não       |

> [!NOTE]
> O tamanho da carga de solicitação HTTP com suporte é de cerca de 500 KB. Se o tamanho da carga que você deseja passar para o ponto de extremidade da Web for maior que 500 KB, considere o envio em lote da carga em partes menores.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista das propriedades às quais a origem HTTP dá suporte.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HTTP em configurações de `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade Type em `storeSettings` deve ser definida como **HttpReadSetting**. | Sim      |
| requestMethod            | O método HTTP. <br>Os valores permitidos são **Get** (padrão) e **post**. | Não       |
| addtionalHeaders         | Cabeçalhos de solicitação HTTP adicionais.                             | Não       |
| RequestBody              | O corpo da solicitação HTTP.                               | Não       |
| httpRequestTimeout           | O tempo limite (o valor de **TimeSpan** ) para a solicitação HTTP obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor padrão é **00:01:40**. | Não       |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
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
                    "type": "HttpReadSetting",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte como estão para compatibilidade com versões anteriores. É recomendável usar o novo modelo mencionado nas seções acima no futuro e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **httpfile**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente a URL especificada na definição de serviço vinculado é usada. | Não |
| requestMethod | O método HTTP. Os valores permitidos são **Get** (padrão) e **post**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| RequestBody | O corpo da solicitação HTTP. | Não |
| format | Se você quiser recuperar dados do ponto de extremidade HTTP como estão sem analisá-los e, em seguida, copiar os dados para um repositório baseado em arquivo, ignore a seção **formato** nas definições do conjunto de dados de entrada e saída.<br/><br/>Se você deseja analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Em **formato**, defina a propriedade **Type** como um desses valores. Para obter mais informações, consulte [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), formato [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Tipos com suporte: **gzip**, **deflate**, **bzip2**e **ZipDeflate**.<br/>Níveis com suporte: **ideal** e **mais rápido**. |Não |

> [!NOTE]
> O tamanho da carga de solicitação HTTP com suporte é de cerca de 500 KB. Se o tamanho da carga que você deseja passar para o ponto de extremidade da Web for maior que 500 KB, considere o envio em lote da carga em partes menores.

**Exemplo 1: usando o método Get (padrão)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exemplo 2: usando o método post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem da atividade de cópia herdada

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **httpname**. | Sim |
| httpRequestTimeout | O tempo limite (o valor de **TimeSpan** ) para a solicitação HTTP obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor padrão é **00:01:40**.  | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
