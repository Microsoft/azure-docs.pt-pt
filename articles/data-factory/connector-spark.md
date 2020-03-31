---
title: Copiar dados da Spark
description: Saiba como copiar dados da Spark para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 63e352ce47c5934171594ae87ee307603fff4c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931014"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Copiar dados da Spark utilizando a Azure Data Factory 

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Spark. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Spark é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Spark para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Spark.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Faísca:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Faísca** | Sim |
| anfitrião | Endereço IP ou nome anfitrião do servidor Spark  | Sim |
| porta | A porta TCP que o servidor Spark utiliza para ouvir as ligações do cliente. Se ligar ao Azure HDInsights, especifique a porta como 443. | Sim |
| servidorType | O tipo de servidor Spark. <br/>Os valores permitidos são: **SharkServer,** **SharkServer2**, **SparkThriftServer** | Não |
| thriftTransportProtocol | O protocolo de transporte a utilizar na camada Thrift. <br/>Os valores permitidos são: **Binário,** **SASL,** **HTTP** | Não |
| authenticationType | O método de autenticação utilizado para aceder ao servidor Spark. <br/>Os valores permitidos são: **Anónimo,** **Nome de utilizador,** **UsernameAndPassword,** **WindowsAzureHDInsightService** | Sim |
| o nome de utilizador | O nome de utilizador que utiliza para aceder ao Spark Server.  | Não |
| palavra-passe | A palavra-passe correspondente ao utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| httpPath | O URL parcial correspondente ao servidor Spark.  | Não |
| enableSsl | Especifica se as ligações ao servidor estão encriptadas utilizando o SSL. O valor predefinido é false.  | Não |
| trustedCertPath | O caminho completo do ficheiro .pem contendo certificados CA fidedignos para verificar o servidor ao ligar-se através do SSL. Esta propriedade só pode ser definida quando se utiliza SSL em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve utilizar um certificado CA a partir da loja fiduciário do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | Não |
| permitirHostNameCNMismatch | Especifica se deve exigir um nome de certificado SSL emitido pela CA para corresponder ao nome de anfitrião do servidor ao ligar-se ao SSL. O valor predefinido é false.  | Não |
| permitir AutoSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

**Exemplo:**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Spark.

Para copiar dados da Spark, detete a propriedade do tipo do conjunto de dados para **SparkObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SparkObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` `table` e para nova carga de trabalho. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte spark.

### <a name="spark-as-source"></a>Faísca como fonte

Para copiar dados da Spark, delineie o tipo de origem na atividade de cópia para **sparkSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **SparkSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
