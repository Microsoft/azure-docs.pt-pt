---
title: Copiar dados de uma fonte http utilizando a Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou no local http fonte para lojas de dados de sink suportados usando uma atividade de cópia num pipeline Azure Data Factory.
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
ms.openlocfilehash: 1ca439d1a82e3cdbe2cc0274cf63653d39048057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532557"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto final http utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um ponto final http. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

A diferença entre este conector HTTP, o [conector REST](connector-rest.md) e o [conector](connector-web-table.md) da tabela Web são:

- **O conector REST** suporta especificamente a cópia de dados de APIs restful; 
- **O conector HTTP** é genérico para recuperar dados de qualquer ponto final http, por exemplo, para descarregar ficheiros. Antes de o conector REST ficar disponível, pode por acaso utilizar o conector HTTP para copiar dados da API RESTful, que é suportada mas menos funcional em comparação com o conector REST.
- **O conector** de mesa web extrai o conteúdo da tabela a partir de uma página web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector HTTP é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma fonte http para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Pode utilizar este conector HTTP para:

- Recolha dados de um ponto final HTTP/S utilizando os métodos HTTP **GET** ou **POST.**
- Recupere os dados utilizando uma das seguintes autenticações: **Anónimo,** **Básico,** **Digest,** **Windows,** ou **ClientCertificate**.
- Copie a resposta HTTP como está ou analise-a utilizando [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs.md)de compressão .

> [!TIP]
> Para testar um pedido http para a recuperação de dados antes de configurar o conector HTTP na Data Factory, conheça a especificação da API para os requisitos do cabeçalho e do corpo. Pode utilizar ferramentas como o Carteiro ou um navegador web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas do conector HTTP.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado http:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida para **HttpServer**. | Sim |
| url | O URL base para o servidor web. | Sim |
| permitirServerCertificateValidação | Especifique se ativa a validação do certificado SSL do servidor quando se ligar a um ponto final HTTP. Se o seu servidor HTTPS utilizar um certificado auto-assinado, detete to **false**this property . | Não<br /> (o padrão é **verdadeiro)** |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anónimos,** **Básicos,** **Digest,** **Windows**e **ClientCertificate.** <br><br> Consulte as secções que seguem esta tabela para obter mais propriedades e amostras JSON para estes tipos de autenticação. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, é utilizado o tempo de execução de integração azure padrão. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilização de autenticação básica, digestiva ou windows

Detete a propriedade **autenticaçãoType** para **Básico,** **Digest,** ou **Windows**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| userName | O nome do utilizador a utilizar para aceder ao ponto final http. | Sim |
| palavra-passe | A palavra-passe para o utilizador (o valor **userName).** Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre de Chaves Azure.](store-credentials-in-key-vault.md) | Sim |

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

### <a name="using-clientcertificate-authentication"></a>Utilização da autenticação do ClientCertificate

Para utilizar a autenticação do ClientCertificate, detete a propriedade **autenticaçãoType** para **ClientCertificate**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificados codificados base64. | Especifique o **embeddedCertData** ou **o certThumbprint**. |
| certThumbprint | A impressão digital do certificado que está instalado na loja cert da máquina de integração autónoma. Aplica-se apenas quando o tipo de tempo de execução de integração auto-hospedado é especificado na propriedade **connectVia.** | Especifique o **embeddedCertData** ou **o certThumbprint**. |
| palavra-passe | A senha que está associada ao certificado. Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre de Chaves Azure.](store-credentials-in-key-vault.md) | Não |

Se utilizar **certThumbprint** para autenticação e o certificado estiver instalado na loja pessoal do computador local, conceda permissões de leitura para o tempo de execução de integração auto-hospedado:

1. Abra a Consola de Gestão da Microsoft (MMC). Adicione o snap-in dos **Certificados** que visa o **Computador Local**.
2. Expandir **Certificados** > **Pessoais,** e, em seguida, selecionar **Certificados**.
3. Clique no certificado da loja pessoal e, em seguida, selecione **Todas as Tarefas** > Gerir**Chaves Privadas**.
3. No separador **Segurança,** adicione a conta de utilizador sob a qual está a funcionar o Serviço de Anfitriões de Prazo de Integração (DIAHostService), com acesso lido ao certificado.

**Exemplo 1: Utilização de certThumbprint**

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

**Exemplo 2: Utilização de DatacertData incorporado**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são `location` suportadas para HTTP em definições no conjunto de dados baseado em formato:

| Propriedade    | Descrição                                                  | Necessário |
| ----------- | ------------------------------------------------------------ | -------- |
| tipo        | A propriedade `location` do tipo em conjunto de dados deve ser definida para **HttpServerLocation**. | Sim      |
| parenteUrl | Um URL relativo ao recurso que contém os dados. O conector HTTP copia `[URL specified in linked service][relative URL specified in dataset]`dados do URL combinado: .   | Não       |

> [!NOTE]
> O tamanho suportado do pedido HTTP é de cerca de 500 KB. Se o tamanho da carga útil que pretende passar para o seu ponto final da web for superior a 500 KB, considere o lote amento da carga útil em pedaços menores.

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Esta secção fornece uma lista de propriedades que a fonte http suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são `storeSettings` suportadas para HTTP em definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade `storeSettings` do tipo em baixo deve ser definida para **HttpReadSettings**. | Sim      |
| solicitarMétodo            | O método HTTP. <br>Os valores permitidos são **Get** (padrão) e **Post**. | Não       |
| addtionalHeaders         | Cabeçalhos adicionais de pedido HTTP.                             | Não       |
| requestBody              | O corpo para o pedido http.                               | Não       |
| httpRequestTimeout           | O prazo (o valor **TimeSpan)** para o pedido http para obter uma resposta. Este valor é o tempo de saída para obter uma resposta, não o tempo de paragem para ler dados de resposta. O valor predefinido é **00:01:40**. | Não       |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente ao armazém. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os seguintes modelos ainda são suportados como é para retrocompatibilidade. É-lhe sugerido que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do conjunto de dados deve ser definida para **HttpFile**. | Sim |
| parenteUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não é especificada, apenas é utilizado o URL especificado na definição de serviço ligado. | Não |
| solicitarMétodo | O método HTTP. Os valores permitidos são **Get** (padrão) e **Post**. | Não |
| cabeçalhos adicionais | Cabeçalhos adicionais de pedido HTTP. | Não |
| requestBody | O corpo para o pedido http. | Não |
| formato | Se pretender recuperar dados do ponto final http como está sem os analisar e, em seguida, copiar os dados para uma loja baseada em ficheiros, ignore a secção de **formato** nas definições de entrada e de conjunto de dados de saída.<br/><br/>Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Em **formato,** detete a propriedade **tipo** para um destes valores. Para mais informações, consulte o [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato de texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para mais informações, consulte [formatos de ficheiros suportados e codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)de compressão .<br/><br/>Tipos suportados: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**.<br/>Níveis suportados: **Ideal** e **Mais Rápido**. |Não |

> [!NOTE]
> O tamanho suportado do pedido HTTP é de cerca de 500 KB. Se o tamanho da carga útil que pretende passar para o seu ponto final da web for superior a 500 KB, considere o lote amento da carga útil em pedaços menores.

**Exemplo 1: Utilizar o método Get (padrão)**

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

**Exemplo 2: Utilização do método Post**

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de fonte de fonte de atividade de cópia legado

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **httpSource**. | Sim |
| httpRequestTimeout | O prazo (o valor **TimeSpan)** para o pedido http para obter uma resposta. Este valor é o tempo de saída para obter uma resposta, não o tempo de paragem para ler dados de resposta. O valor predefinido é **00:01:40**.  | Não |

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

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
