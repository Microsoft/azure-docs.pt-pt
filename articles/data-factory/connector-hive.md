---
title: Copiar dados de Hive com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Hive para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1db5e0fbdd62ee246d32ca04082b7aedd78ab997
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090263"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiar dados de Hive com o Azure Data Factory 

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Hive. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do hive tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Pode copiar dados de Hive para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Hive.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Hive:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Hive** | Sim |
| host | Endereço IP ou nome do host do servidor Hive, separados por '; ' para vários hosts (somente quando o Service DiscoveryMode está habilitado).  | Sim |
| port | A porta TCP que o servidor do Hive utiliza para escutar ligações de cliente. Se ligar ao Azure HDInsights, especifique a porta como 443. | Sim |
| serverType | O tipo de servidor do Hive. <br/>Valores permitidos são: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Não |
| thriftTransportProtocol | O protocolo de transporte para utilizar na camada de Thrift. <br/>Valores permitidos são: **Binary**, **SASL**, **http** | Não |
| authenticationType | O método de autenticação utilizado para aceder ao servidor do Hive. <br/>Valores permitidos são: **Anônimo**, **nome de usuário**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sim |
| serviceDiscoveryMode | TRUE para indicar a utilizar o serviço de ZooKeeper, false não.  | Não |
| zooKeeperNameSpace | O espaço de nomes no qual 2 de servidor do Hive são adicionados nós do ZooKeeper.  | Não |
| useNativeQuery | Especifica se o driver usa consultas HiveQL nativas ou as converte em um formato equivalente em HiveQL.  | Não |
| username | O nome de utilizador que utiliza para aceder ao servidor do Hive.  | Não |
| password | A palavra-passe correspondente ao utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| httpPath | O URL de parcial correspondente para o servidor do Hive.  | Não |
| enableSsl | Especifica se as ligações ao servidor são encriptadas com SSL. O valor predefinido é false.  | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna para verificar o servidor ao ligar-se através de SSL. Esta propriedade só pode ser definida ao utilizar o SSL em Ir autoalojado. O valor predefinido é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se pretende utilizar um certificado de AC a partir da loja de confiança do sistema ou a partir de um ficheiro PEM especificado. O valor predefinido é false.  | Não |
| allowHostNameCNMismatch | Especifica se exige um nome de certificado SSL emitidos pela AC para coincidir com o nome de anfitrião do servidor ao ligar-se através de SSL. O valor predefinido é false.  | Não |
| allowSelfSignedServerCert | Especifica se pretende permitir que os certificados autoassinados do servidor. O valor predefinido é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Hive.

Para copiar dados de Hive, defina a propriedade de tipo de conjunto de dados para **HiveObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **HiveObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela, incluindo a parte do esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para nova carga de trabalho `schema` , `table`use e. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Hive.

### <a name="hivesource-as-source"></a>HiveSource como origem

Para copiar dados de Hive, definir o tipo de origem na atividade de cópia para **HiveSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **HiveSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
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


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
