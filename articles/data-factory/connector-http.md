---
title: Copie dados de uma fonte HTTP utilizando a Azure Data Factory
description: Saiba como copiar dados de uma fonte HTTP em nuvem ou no local para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
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
ms.openlocfilehash: 730efb552ef218cc5a5ce6a984d20b4e23b364ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416945"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copie os dados de um ponto final HTTP utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados de um ponto final HTTP. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

A diferença entre este conector HTTP, o [conector REST](connector-rest.md) e o [conector](connector-web-table.md) de mesa Web são:

- **O conector REST** suporta especificamente os dados de cópia de APIs RESTful; 
- **O conector HTTP** é genérico para obter dados de qualquer ponto final HTTP, por exemplo, para descarregar ficheiros. Antes de o conector REST ficar disponível, pode por acaso utilizar o conector HTTP para copiar dados da API RESTful, que é suportado mas menos funcional em comparação com o conector REST.
- **O conector da tabela web** extrai o conteúdo da tabela a partir de uma página web HTML.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector HTTP é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma fonte HTTP para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Pode utilizar este conector HTTP para:

- Obtenha dados de um ponto final HTTP/S utilizando os métodos HTTP **GET** ou **POST.**
- Recupere os dados utilizando uma das seguintes autenticações: **Anónimo,** **Básico,** **Digest,** **Windows**ou **ClientCertificate**.
- Copie a resposta HTTP como é ou analise-a utilizando [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Para testar um pedido HTTP de recuperação de dados antes de configurar o conector HTTP na Data Factory, saiba mais sobre a especificação API para os requisitos do cabeçalho e do corpo. Você pode usar ferramentas como o Carteiro ou um navegador web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas do conector HTTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço http linked:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **HttpServer**. | Sim |
| url | O URL base para o servidor web. | Sim |
| enableServerCertificateValidation | Especificar se deve ativar a validação do certificado TLS/SSL do servidor quando ligar a um ponto final HTTP. Se o seu servidor HTTPS utilizar um certificado auto-assinado, descreva esta propriedade **como falsa**. | Não<br /> (o padrão é **verdadeiro)** |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anónimos,** **Básicos,** **Digest,** **Windows**e **ClientCertificate**. <br><br> Consulte as secções que seguem esta tabela para obter mais propriedades e amostras JSON para estes tipos de autenticação. | Sim |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilização da autenticação Básica, Digest ou Windows

Desa estação A propriedade **do Tipo de autenticação** para **Basic,** **Digest**ou **Windows**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final HTTP. | Sim |
| palavra-passe | A palavra-passe para o utilizador (o valor **do nome do utilizador).** Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) | Sim |

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

### <a name="using-clientcertificate-authentication"></a>Utilização da autenticação Do ClienteCertificado

Para utilizar a autenticação ClientCertificate, desafie a propriedade **autenticaçãoType** ao **ClientCertificate**. Além das propriedades genéricas descritas na secção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| EmbeddedCertData | Dados de certificado codificados de base64. | Especifique o **CertifiData incorporado** ou **o certificadoThumbprint**. |
| certThumbprint | A impressão digital do certificado instalado na loja de cert da sua máquina de execução de integração auto-hospedada. Aplica-se apenas quando o tipo de tempo de execução de integração auto-alojado é especificado na propriedade **connectVia.** | Especifique o **CertifiData incorporado** ou **o certificadoThumbprint**. |
| palavra-passe | A senha que está associada ao certificado. Marque este campo como um tipo **SecureString** para armazená-lo de forma segura na Data Factory. Também pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) | Não |

Se utilizar **o certThumbprint** para autenticação e o certificado estiver instalado na loja pessoal do computador local, conceda permissões de leitura ao tempo de execução de integração auto-hospedado:

1. Abra a Consola de Gestão da Microsoft (MMC). Adicione o encaixe de **certificados** que visa **o Computador Local.**
2. Expandir **Certificados**  >  **Pessoais**e, em seguida, selecionar **Certificados**.
3. Clique com o botão direito no certificado da loja pessoal e, em seguida, selecione **Todas as Tarefas**  >  **Gerencie as Teclas Privadas**.
3. No separador **Segurança,** adicione a conta de utilizador sob a qual está a funcionar o Serviço de Anfitriões de Integração (DIAHostService), com acesso lido ao certificado.

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

**Exemplo 2: Utilização incorporada DoCertData**

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

As seguintes propriedades são suportadas para HTTP em `location` definições no conjunto de dados baseado em formato:

| Propriedade    | Descrição                                                  | Obrigatório |
| ----------- | ------------------------------------------------------------ | -------- |
| tipo        | A propriedade tipo `location` em conjunto de dados deve ser definida para **HttpServerLocation**. | Sim      |
| relativoUrl | Um URL relativo ao recurso que contém os dados. O conector HTTP copia os dados do URL combinado: `[URL specified in linked service][relative URL specified in dataset]` .   | Não       |

> [!NOTE]
> O tamanho da carga útil do pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga útil que pretende passar para o seu ponto final da web for superior a 500 KB, considere a lotação útil em pedaços menores.

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a fonte HTTP suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para HTTP em `storeSettings` definições na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tipo                     | A propriedade tipo em baixo `storeSettings` deve ser definida para **HttpReadSettings**. | Sim      |
| requestMethod            | O método HTTP. <br>Os valores permitidos são **Get** (predefinido) e **Post**. | Não       |
| addtionalHeaders         | Cabeçalhos de pedido HTTP adicionais.                             | Não       |
| requestCorp              | O corpo para o pedido HTTP.                               | Não       |
| httpRequestTimeout           | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor predefinido é **00:01:40**. | Não       |
| maxConcurrentConnections | O número de ligações a ligar ao armazém simultaneamente. Especifique apenas quando pretende limitar a ligação simultânea à loja de dados. | Não       |

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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos seguintes ainda são suportados como é para retrocompatibilidade. Sugere-se que utilize o novo modelo mencionado nas secções acima, e a UI de autoria da ADF passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **HttpFile**. | Sim |
| relativoUrl | Um URL relativo ao recurso que contém os dados. Quando esta propriedade não é especificada, apenas é utilizado o URL especificado na definição de serviço ligado. | Não |
| requestMethod | O método HTTP. Os valores permitidos são **Get** (predefinido) e **Post**. | Não |
| cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestCorp | O corpo para o pedido HTTP. | Não |
| formato | Se pretender obter dados do ponto final HTTP como-é sem analisá-los e, em seguida, copiar os dados para uma loja baseada em ficheiros, salte a secção de **formato** nas definições de conjunto de dados de entrada e saída.<br/><br/>Se pretender analisar o conteúdo de resposta HTTP durante a cópia, suportam-se os seguintes tipos de formato de ficheiro: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Em **formato,** desateia a propriedade **tipo** a um destes valores. Para mais informações, consulte [o formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato de texto,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Para obter mais informações, consulte [formatos de ficheiros suportados e codecs de compressão](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Tipos suportados: **GZip,** **Deflate,** **BZip2**e **ZipDeflate**.<br/>Níveis suportados:  **Ideal** e **Mais Rápido**. |Não |

> [!NOTE]
> O tamanho da carga útil do pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga útil que pretende passar para o seu ponto final da web for superior a 500 KB, considere a lotação útil em pedaços menores.

**Exemplo 1: Utilização do método Get (padrão)**

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

**Exemplo 2: Utilização do método post**

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de origem de atividade de cópia de legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade de cópia deve ser definida para **HttpSource**. | Sim |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. O valor predefinido é **00:01:40**.  | Não |

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

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
