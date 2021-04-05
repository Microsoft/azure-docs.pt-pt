---
title: Copiar dados da Hive usando a Azure Data Factory
description: Saiba como copiar dados da Hive para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367032"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Copiar e transformar dados da Colmeia utilizando a Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Hive. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector hive é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Hive para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não é necessário instalar manualmente qualquer controlador utilizando este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector hive.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Hive:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Colmeia** | Yes |
| anfitrião | Endereço IP ou nome de anfitrião do servidor Hive, separado por ';' para vários anfitriões (apenas quando o serviçoDiscoveryMode estiver ativado).  | Yes |
| porta | A porta TCP que o servidor Hive utiliza para ouvir as ligações do cliente. Se ligar ao Azure HDInsights, especifique a porta como 443. | Yes |
| serverType | O tipo de servidor hive. <br/>Os valores permitidos são: **HiveServer1,** **HiveServer2,** **HiveThriftServer** | No |
| thriftTransportProtocol | O protocolo de transporte para usar na camada Thrift. <br/>Os valores permitidos são: **Binário,** **SASL,** **HTTP** | No |
| authenticationType | O método de autenticação utilizado para aceder ao servidor Hive. <br/>Os valores permitidos são: **Anónimo**, **Nome de Utilizador,** **UsernameAndPassword**, **WindowsAzureHDInsightService**. A autenticação kerberos não é suportada agora. | Yes |
| serviçoDiscoveryMode | verdadeiro para indicar usando o serviço ZooKeeper, falso não.  | No |
| zooKeeperNameSpace | O espaço de nome no ZooKeeper sob o qual são adicionados nós do Hive Server 2.  | No |
| useNativeQuery | Especifica se o condutor utiliza consultas nativas de HiveQL ou as converte numa forma equivalente no HiveQL.  | No |
| nome de utilizador | O nome de utilizador que usa para aceder ao Hive Server.  | No |
| palavra-passe | A palavra-passe correspondente ao utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| httpPath | O URL parcial correspondente ao servidor Hive.  | No |
| ativarSl | Especifica se as ligações ao servidor são encriptadas utilizando O S.TLS. O valor predefinido é false.  | No |
| trustedCertPath | O percurso completo do ficheiro .pem que contém certificados ca fidedignos para verificar o servidor ao ligar através de TLS. Esta propriedade só pode ser definida quando se utiliza TLS em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | No |
| useSystemTrustStore | Especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | No |
| permitirHostNameCNMismatch | Especifica se deve exigir um nome de certificado TLS/SSL emitido pela CA para corresponder ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é false.  | No |
| permitirSelfSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |
| armazenamentoReferência | Uma referência ao serviço ligado da conta de armazenamento utilizada para a encenação de dados no fluxo de dados de mapeamento. Isto só é necessário quando se utiliza o serviço ligado à Colmeia no fluxo de dados de mapeamento | No |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da Hive.

Para copiar dados da Hive, defina a propriedade tipo do conjunto de dados para a **HiveObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **HiveObject** | Yes |
| esquema | O nome do esquema. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | Nome da mesa, incluindo a parte do esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte da Hive.

### <a name="hivesource-as-source"></a>HiveSource como fonte

Para copiar dados da Hive, desa estale o tipo de origem na atividade de cópia para **HiveSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **HiveSource** | Yes |
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

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

O conector da colmeia é suportado como uma fonte [de conjunto de dados inline](data-flow-source.md#inline-datasets) nos fluxos de dados de mapeamento. Leia usando uma consulta ou diretamente de uma tabela de Colmeia em HDInsight. Os dados da colmeia são encenados numa conta de armazenamento como ficheiros de parquet antes de serem transformados como parte de um fluxo de dados. 

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte de colmeia. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Armazenamento | Loja deve ser `hive` | sim |  `hive` | Loja | 
| Formato | Quer esteja a ler de uma mesa ou de uma consulta | sim | `table` ou `query` | formato |
| Nome do esquema | Se ler de uma mesa, o esquema da tabela de origem |  Sim, se o formato é `table` | String | schemaName |
| Nome da tabela | Se ler de uma mesa, o nome da mesa |   Sim, se o formato é `table` | String | tableName |
| Consulta | Se o formato for `query` , a consulta de origem no serviço ligado à Colmeia | Sim, se o formato é `query` | String | consulta |
| Encenado | A mesa da colmeia será sempre encenada. | sim | `true` | encenado |
| Recipiente de armazenamento | Recipiente de armazenamento usado para encenar dados antes de ler da Colmeia ou escrever para a Colmeia. O aglomerado de colmeias deve ter acesso a este recipiente. | sim | String | storageContainer |
| Base de dados de encenação | O esquema/base de dados onde a conta de utilizador especificada no serviço ligado tem acesso. É usado para criar tabelas externas durante a encenação e caiu depois | não | `true` ou `false` | stagingDatabaseName |
| Scripts Pré SQL | Código SQL para executar na tabela Hive antes de ler os dados | não | String | pré-QLs |

#### <a name="source-example"></a>Exemplo de origem

Abaixo está um exemplo de uma configuração de origem da Colmeia:

![Exemplo de fonte de colmeia](media/data-flow/hive-source.png "[Exemplo de fonte de colmeia")

Estas definições traduzem-se no seguinte script de fluxo de dados:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Limitações conhecidas

* Tipos complexos como matrizes, mapas, estruturas e sindicatos não são apoiados para leitura. 
* O conector hive suporta apenas as tabelas de Colmeia em Azure HDInsight da versão 4.0 ou superior (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
