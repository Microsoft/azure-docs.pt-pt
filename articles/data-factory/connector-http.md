---
title: Copiar dados a partir de uma origem HTTP utilizando o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de uma origem HTTP na cloud ou no local para os arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: f25b0f2c7b5e3148bae778c4b50a3f0bd0c148da
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875952"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto final HTTP através do Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um ponto final HTTP. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

A diferença entre este conector HTTP, o [conector REST](connector-rest.md) e o [conector de tabela de Web](connector-web-table.md) são:

- **Conector REST** especificamente suporte copiam dados a partir de RESTful APIs; 
- **Conector HTTP** é genérico para recuperar dados a partir de qualquer ponto final de HTTP, por exemplo, para transferir o ficheiro. Antes do conector REST torna-se disponível, pode ocorrer utilizar o conector HTTP para copiar dados a partir da API RESTful, que é suportado, mas menos funcional comparando com o conector REST.
- **Conector de tabela de Web** extrações de tabela conteúdo a partir de uma página da Web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir de uma origem HTTP para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Pode utilizar este conector HTTP para:

- Recuperar dados de um ponto de final HTTP/S utilizando o HTTP **Obtenha** ou **POST** métodos.
- Obter dados ao utilizar uma das autenticações seguintes: **Anónimo**, **básica**, **Digest**, **Windows**, ou **ClientCertificate**.
- Copiar a resposta HTTP como-é ou analisá-lo usando [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Para testar uma solicitação HTTP para obtenção de dados antes de configurar o conector HTTP no Data Factory, saiba mais sobre a especificação de API do cabeçalho e requisitos de corpo. Pode usar ferramentas como o Postman ou um navegador da web para validar.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector HTTP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de HTTP:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **HttpServer**. | Sim |
| url | O URL de base para o servidor web. | Sim |
| enableServerCertificateValidation | Especifique se pretende ativar a validação de certificado SSL do servidor quando se liga a um ponto final HTTP. Se o seu servidor HTTPS utiliza um certificado autoassinado, defina esta propriedade como **false**. | Não<br /> (a predefinição é **true**) |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são **anónimo**, **básica**, **Digest**, **Windows**, e **ClientCertificate**. <br><br> Veja as secções que se seguem esta tabela para obter mais propriedades e exemplos JSON para esses tipos de autenticação. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou um Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, esta propriedade usa o padrão do Runtime de integração do Azure. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilizar a autenticação básica, Digest ou do Windows

Definir o **authenticationType** propriedade **básica**, **Digest**, ou **Windows**. Além de propriedades genéricas que são descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final HTTP. | Sim |
| password | A palavra-passe para o utilizador (o **nome de utilizador** valor). Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

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

### <a name="using-clientcertificate-authentication"></a>Utilizar a autenticação de ClientCertificate

Para utilizar a autenticação de ClientCertificate, defina o **authenticationType** propriedade **ClientCertificate**. Além de propriedades genéricas que são descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificado com codificação Base64. | Especifique **embeddedCertData** ou **certThumbprint**. |
| certThumbprint | O thumbprint do certificado que está instalado no arquivo de certificados do computador do Integration Runtime autoalojado. Aplica-se apenas quando o tipo de hospedagem interna do Integration Runtime é especificado na **connectVia** propriedade. | Especifique **embeddedCertData** ou **certThumbprint**. |
| password | A palavra-passe associada ao certificado. Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |

Se usar **certThumbprint** para autenticação e o certificado está instalado no arquivo pessoal do computador local, conceder permissões de leitura para o Runtime de integração autoalojado:

1. Abra o Console de gerenciamento Microsoft (MMC). Adicionar a **certificados** snap-in direcionada **computador Local**.
2. Expanda **certificados** > **pessoal**e, em seguida, selecione **certificados**.
3. O certificado do arquivo pessoal com o botão direito e, em seguida, selecione **todas as tarefas** > **gerir chaves privadas**.
3. Sobre o **segurança** separador, adicione a conta de utilizador sob a qual o serviço de anfitrião do Integration Runtime (DIAHostService) está em execução, com acesso de leitura para o certificado.

**Exemplo 1: Usando certThumbprint**

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

**Exemplo 2: Usando embeddedCertData**

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

- Para **Parquet e o formato de texto delimitado**, consulte [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) secção.
- Como outros formatos **formato ORC/Avro/JSON/binário**, consulte [outro conjunto de dados do formato](#other-format-dataset) secção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet e o conjunto de dados de formato de texto delimitado

Para copiar dados de HTTP na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) num conjunto de dados com base no formato de artigos e suporte definições. As seguintes propriedades são suportadas para HTTP em `location` definições no conjunto de dados com base no formato:

| Propriedade    | Descrição                                                  | Necessário |
| ----------- | ------------------------------------------------------------ | -------- |
| tipo        | A propriedade de tipo sob `location` no conjunto de dados deve ser definida como **HttpServerLocation**. | Sim      |
| relativeUrl | Um URL relativo ao recurso que contém os dados.       | Não       |

> [!NOTE]
> O tamanho de payload de pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga que pretende passar para o ponto final de web for superior a 500 KB, considere a criação de batches o payload em segmentos mais pequenos.

> [!NOTE]
> **HttpFile** tipo conjunto de dados com o formato Parquet/Text mencionado na secção seguinte ainda é suportado como-destina-se a atividade de cópia/pesquisa para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

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

### <a name="other-format-dataset"></a>Outro conjunto de dados do formato

Para copiar dados de HTTP na **formato ORC/Avro/JSON/binário**, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **HttpFile**. | Sim |
| relativeUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não for especificada, é utilizado apenas o URL especificado na definição do serviço ligado. | Não |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** (predefinição) e **Post**. | Não |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestBody | O corpo do pedido HTTP. | Não |
| Formato | Se pretender recuperar dados do ponto de extremidade HTTP como-é sem análise e copie os dados para um armazenamento baseado em arquivo, ignore o **formato** secção em ambas as definições do conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e **ParquetFormat**. Sob **formato**, defina o **tipo** propriedade para um dos seguintes valores. Para obter mais informações, consulte [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Para obter mais informações, consulte [formatos de arquivo e codecs de compressão suportados](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Tipos suportados: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis de suporte:  **Ideal** e **mais rápida**. |Não |

> [!NOTE]
> O tamanho de payload de pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga que pretende passar para o ponto final de web for superior a 500 KB, considere a criação de batches o payload em segmentos mais pequenos.

**Exemplo 1: Usando o método Get (predefinição)**

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

**Exemplo 2: Usando o método Post**

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

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suporta a origem HTTP.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como origem

- Para copiar a partir **Parquet e o formato de texto delimitado**, consulte [Parquet e origem de formato de texto delimitado](#parquet-and-delimited-text-format-source) secção.
- Para copiar a partir de outros formatos, como **formato ORC/Avro/JSON/binário**, consulte [outra origem de formato](#other-format-source) secção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e origem de formato de texto delimitado

Para copiar dados de HTTP na **Parquet ou formato de texto delimitado**, consulte [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo na origem de atividade de cópia baseada em formato e definições suportadas. As seguintes propriedades são suportadas para HTTP em `storeSettings` definições na origem de cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Necessário |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade de tipo sob `storeSettings` deve ser definida como **HttpReadSetting**. | Sim      |
| requestMethod            | O método HTTP. <br>Valores permitidos são **Obtenha** (predefinição) e **Post**. | Não       |
| addtionalHeaders         | Cabeçalhos de pedido HTTP adicionais.                             | Não       |
| requestBody              | O corpo do pedido HTTP.                               | Não       |
| requestTimeout           | O tempo limite (o **TimeSpan** valor) para o pedido HTTP para obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. O valor predefinido é **01:00:40**. | Não       |
| maxConcurrentConnections | O número de ligações para ligar ao arquivo de armazenamento em simultâneo. Especifique apenas quando pretender limitar a ligação em simultâneo ao arquivo de dados. | Não       |

> [!NOTE]
> Para o formato de texto delimitados/Parquet, **HttpSource** continua a ser suportada como origem da atividade de cópia de tipo mencionada na secção seguinte-destina-se para compatibilidade com versões anteriores. São sugeridas para usar este novo modelo daqui em diante, e o ADF criação da interface do Usuário mudou para gerar esses novos tipos.

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

#### <a name="other-format-source"></a>Outra origem de formato

Para copiar dados de HTTP na **formato ORC/Avro/JSON/binário**, as seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **HttpSource**. | Sim |
| httpRequestTimeout | O tempo limite (o **TimeSpan** valor) para o pedido HTTP para obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. O valor predefinido é **01:00:40**.  | Não |

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


## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
