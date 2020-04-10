---
title: Copiar dados da Hive usando a Azure Data Factory
description: Saiba como copiar dados da Hive para armazenar dados de sumidouro suportados utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1854465c463d78999674080048207cfa0916da3b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992169"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiar dados da Hive usando a Azure Data Factory 

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Hive. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector hive é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Hive para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector da Colmeia.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Hive:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Colmeia** | Sim |
| anfitrião | Endereço IP ou nome do anfitrião do servidor Hive, separado por ';' para vários anfitriões (apenas quando o serviçoDiscoveryMode está ativado).  | Sim |
| porta | A porta TCP que o servidor da Hive usa para ouvir as ligações do cliente. Se ligar ao Azure HDInsights, especifique a porta como 443. | Sim |
| servidorType | O tipo de servidor da Colmeia. <br/>Os valores permitidos são: **HiveServer1,** **HiveServer2**, **HiveThriftServer** | Não |
| thriftTransportProtocol | O protocolo de transporte a utilizar na camada Thrift. <br/>Os valores permitidos são: **Binário,** **SASL,** **HTTP** | Não |
| authenticationType | O método de autenticação utilizado para aceder ao servidor da Colmeia. <br/>Os valores permitidos são: **Anónimo,** **Nome de utilizador,** **UsernameAndPassword,** **WindowsAzureHDInsightService** | Sim |
| serviçoDiscoveryMode | fiel a indicar usando o serviço ZooKeeper, falso não.  | Não |
| zooKeeperNameSpace | O espaço de nome no ZooKeeper sob o qual são adicionados os nós do Hive Server 2.  | Não |
| usarNativeQuery | Especifica se o condutor utiliza consultas nativas da HiveQL ou as converte numa forma equivalente na HiveQL.  | Não |
| o nome de utilizador | O nome de utilizador que utiliza para aceder ao Hive Server.  | Não |
| palavra-passe | A palavra-passe correspondente ao utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| httpPath | O URL parcial correspondente ao servidor da Colmeia.  | Não |
| enableSsl | Especifica se as ligações ao servidor estão encriptadas utilizando TLS. O valor predefinido é false.  | Não |
| trustedCertPath | O caminho completo do ficheiro .pem contendo certificados CA fidedignos para verificar o servidor ao ligar-se através de TLS. Esta propriedade só pode ser definida quando se utiliza TLS em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve utilizar um certificado CA a partir da loja fiduciário do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | Não |
| permitirHostNameCNMismatch | Especifica se deve exigir um nome de certificado TLS/SSL emitido pela CA para corresponder ao nome de anfitrião do servidor ao ligar-se ao TLS. O valor predefinido é false.  | Não |
| permitir AutoSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da Hive.

Para copiar dados da Hive, detete a propriedade do tipo do conjunto de dados para **HiveObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **HiveObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa, incluindo parte do esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `schema` `table`carga de trabalho, use e . | Não (se for especificada a "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte da Hive.

### <a name="hivesource-as-source"></a>HiveSource como fonte

Para copiar dados da Hive, delineie o tipo de origem na atividade de cópia para **hiveSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **HiveSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
