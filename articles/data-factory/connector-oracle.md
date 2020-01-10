---
title: Copiar dados de e para o Oracle usando o Azure Data Factory
description: Saiba como copiar dados de armazenamentos de origem com suporte para um Oracle Database ou do Oracle para repositórios de coletor com suporte, usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5fd13531e438b8bcda8e3720758e338c964f77af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444260"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um banco de dado Oracle. Ele se baseia na [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Oracle tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dado de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para um Oracle Database. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Oracle oferece suporte a:

- As seguintes versões de um banco de dados Oracle:
    - Oracle 18C R1 (18,1) e superior
    - Oracle 12c R1 (12,1) e superior
    - Oracle 11g R1 (11,1) e superior
    - Oracle 10g R1 (10,1) e superior
    - Oracle 9i R2 (9,2) e superior
    - Oracle 8i R3 (8.1.7) e superior
    - Oracle Database serviço Cloud Exadata
- Copiar dados usando autenticações Básica ou OID.
- Cópia paralela de uma origem do Oracle. Consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) para obter detalhes.

> [!Note]
> Não há suporte para o Oracle Proxy Server.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

O Integration Runtime fornece um driver da Oracle interno. Portanto, você não precisa instalar um driver manualmente ao copiar dados do e para o Oracle.

## <a name="get-started"></a>Começar

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O serviço vinculado do Oracle oferece suporte às seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância de Oracle Database. <br/>Você também pode colocar uma senha em Azure Key Vault e extrair a configuração de `password` da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. <br><br>**Tipo de conexão com suporte**: você pode usar o **Oracle Sid** ou o **nome do serviço Oracle** para identificar seu banco de dados:<br>-Se você usar o SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar o nome do serviço: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

>[!TIP]
>Se você receber um erro, "ORA-01025: parâmetro UPI fora do intervalo" e a versão do Oracle for 8i, adicione `WireProtocolMode=1` à sua cadeia de conexão. Em seguida, tente novamente.

Mais propriedades de conexão que você pode definir na cadeia de conexão por seu caso:

| Propriedade | Descrição | Valores permitidos |
|:--- |:--- |:--- |
| ArraySize |O número de bytes que o conector pode buscar em uma única viagem de ida e volta da rede. Por exemplo, `ArraySize=‭10485760‬`.<br/><br/>Valores maiores aumentam a taxa de transferência, reduzindo o número de vezes para buscar dados na rede. Valores menores aumentam o tempo de resposta, pois há menos de um atraso aguardando o servidor transmitir dados. | Um número inteiro de 1 a 4294967296 (4 GB). O valor padrão é `60000`. O valor 1 não define o número de bytes, mas indica alocar espaço para exatamente uma linha de dados. |

Para habilitar a criptografia na conexão Oracle, você tem duas opções:

-   Para usar **criptografia triple-DES (3DES) e criptografia AES (AES)** , no lado do servidor Oracle, vá para Oracle Advanced Security (OAS) e defina as configurações de criptografia. Para obter detalhes, consulte esta [documentação da Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O conector do AAD (estrutura de desenvolvimento de aplicativos) da Oracle negocia automaticamente o método de criptografia para usar aquele que você configurar no OAS ao estabelecer uma conexão com o Oracle.

-   Para usar **SSL**:

    1.  Obtenha as informações do certificado SSL. Obtenha as informações de certificado codificado em Distinguished Encoding Rules (DER) de seu certificado SSL e salve a saída (-----iniciar certificado... Encerrar o-----do certificado) como um arquivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** Extraia informações de certificado de DERcert. cer e salve a saída em CERT. txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Crie o `keystore` ou `truststore`. O comando a seguir cria o arquivo de `truststore`, com ou sem uma senha, no formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** Crie um arquivo de `truststore` PKCS12, chamado MyTrustStoreFile, com uma senha.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o arquivo de `truststore` no computador IR auto-hospedado. Por exemplo, coloque o arquivo em C:\MyTrustStoreFile.
    4.  Em Azure Data Factory, configure a cadeia de conexão Oracle com `EncryptionMethod=1` e o valor `TrustStore`/`TrustStorePassword`correspondente. Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de soluções Oracle. Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [DataSets](concepts-datasets-linked-services.md). 

Para copiar dados de e para o Oracle, defina a propriedade Type do conjunto para `OracleTable`. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como `OracleTable`. | Sim |
| schema | Nome do esquema. |Não para a origem, Sim para o sink  |
| table | Nome da tabela/exibição. |Não para a origem, Sim para o sink  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para a origem, Sim para o sink |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Oracle. Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle como fonte

>[!TIP]
>Para carregar dados do Oracle com eficiência usando o particionamento de dados, saiba mais em [cópia paralela do Oracle](#parallel-copy-from-oracle).

Para copiar dados do Oracle, defina o tipo de fonte na atividade de cópia como `OracleSource`. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como `OracleSource`. | Sim |
| oracleReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Ao habilitar a carga particionada, você precisa vincular quaisquer parâmetros de partição internos correspondentes em sua consulta. Para obter exemplos, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Oracle. <br>Os valores permitidos são: **None** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é habilitada (ou seja, não `None`), o grau de paralelismo para carregar dados simultaneamente de um Oracle Database é controlado pela configuração de [`parallelCopies`](copy-activity-performance.md#parallel-copy) na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de partição não estiver `None`. | Não |
| partitionNames | A lista de partições físicas que precisam ser copiadas. <br>Aplicar quando a opção de partição for `PhysicalPartitionsOfTable`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfTabularPartitionName` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será usado pelo particionamento de intervalo para cópia paralela. Se não for especificado, a chave primária da tabela será detectada automaticamente e usada como a coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionColumnName` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionUpbound` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionLowbound` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |

**Exemplo: copiar dados usando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle como coletor

Para copiar dados para o Oracle, defina o tipo de coletor na atividade de cópia como `OracleSink`. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como `OracleSink`. | Sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge `writeBatchSize`.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | O tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Cópia paralela do Oracle

O conector do Data Factory Oracle fornece particionamento de dados interno para copiar dados do Oracle em paralelo. Você pode encontrar opções de particionamento de dados na guia **origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua origem do Oracle para carregar dados por partições. O grau paralelo é controlado pela configuração de [`parallelCopies`](copy-activity-performance.md#parallel-copy) na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, Data Factory gerar e executar simultaneamente quatro consultas com base em sua opção de partição especificada e nas configurações, e cada consulta recuperará uma parte dos dados do Oracle Database.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados do seu Oracle Database. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, ele é redirecionado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta). nesse caso, o desempenho é melhor do que gravar em um único arquivo.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga total de uma tabela grande, com partições físicas.          | **Opção de partição**: partições físicas da tabela. <br><br/>Durante a execução, Data Factory detecta automaticamente as partições físicas e copia os dados por partições. |
| Carga total de uma tabela grande, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Se não for especificado, a coluna de chave primária será usada. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, com partições físicas. | **Opção de partição**: partições físicas da tabela.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partição**: Especifique os nomes de partição dos quais copiar dados. Se não for especificado, Data Factory detectará automaticamente as partições físicas na tabela que você especificou no conjunto de Oracle.<br><br>Durante a execução, Data Factory substitui `?AdfTabularPartitionName` pelo nome real da partição e envia ao Oracle. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>Limite **superior** da partição e limite **inferior da partição**: especifique se deseja filtrar a coluna de partição para recuperar dados somente entre o intervalo inferior e superior.<br><br>Durante a execução, Data Factory substitui `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores de cada partição e envia ao Oracle. <br>Por exemplo, se a coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com partição física**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapeamento de tipo de dados para Oracle

Quando você copia dados do e para o Oracle, os mapeamentos a seguir se aplicam. Para saber mais sobre como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados Oracle | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BArquivo |Byte[] |
| BLOB |Byte[]<br/>(com suporte apenas no Oracle 10g e superior) |
| º |String |
| CLOB |String |
| DATA |DateTime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Número |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Não há suporte para o intervalo dos tipos de dados ano a mês e intervalo de dia para segundo.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
