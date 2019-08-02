---
title: Copiar dados de e para o Oracle usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para um Oracle Database ou do Oracle para repositórios de coletor com suporte, usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 0a71c7ffe9040c3002b1f5378ce298a047554b15
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640203"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um banco de dado Oracle. Ele se baseia na [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dado de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para um Oracle Database. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Oracle oferece suporte a:

- As seguintes versões de um banco de dados Oracle:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)
- Copiar dados usando autenticações Básica ou OID.
- Cópia paralela de uma origem do Oracle. Consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) para obter detalhes.

> [!Note]
> Não há suporte para o Oracle Proxy Server.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de e para um Oracle Database que não é acessível publicamente, você precisa configurar um [Integration Runtime de hospedagem interna](create-self-hosted-integration-runtime.md). O Integration Runtime fornece um driver da Oracle interno. Portanto, você não precisa instalar um driver manualmente ao copiar dados do e para o Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O serviço vinculado do Oracle oferece suporte às seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância de Oracle Database. <br/>Marque este campo como um `SecureString` para armazená-lo com segurança em data Factory. Você também pode colocar uma senha em Azure Key Vault e extrair a `password` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. <br><br>**Tipo de conexão com suporte**: Você pode usar o **Oracle Sid** ou o **nome do serviço Oracle** para identificar seu banco de dados:<br>-Se você usar o SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar o nome do serviço:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar o tempo de execução de integração auto-hospedado ou o tempo de execução de integração do Azure (se o armazenamento de dados estiver publicamente acessível). Se não for especificado, essa propriedade usará o tempo de execução de integração do Azure padrão. |Não |

>[!TIP]
>Se você receber um erro, "ORA-01025: Parâmetro UPI fora do intervalo ", e sua versão do Oracle é 8i, `WireProtocolMode=1` adicione à sua cadeia de conexão. Em seguida, tente novamente.

Para habilitar a criptografia na conexão Oracle, você tem duas opções:

-   Para usar **criptografia triple-DES (3DES) e criptografia AES (AES)** , no lado do servidor Oracle, vá para Oracle Advanced Security (OAS) e defina as configurações de criptografia. Para obter detalhes, consulte esta [documentação da Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O conector do AAD (estrutura de desenvolvimento de aplicativos) da Oracle negocia automaticamente o método de criptografia para usar aquele que você configurar no OAS ao estabelecer uma conexão com o Oracle.

-   Para usar **SSL**:

    1.  Obtenha as informações do certificado SSL. Obtenha as informações de certificado codificado em Distinguished Encoding Rules (DER) de seu certificado SSL e salve a saída (-----iniciar certificado... Encerrar o-----do certificado) como um arquivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Example:** Extraia informações de certificado de DERcert. cer e salve a saída em CERT. txt.

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
    
    2.  Crie o `keystore` ou `truststore`o. O comando a seguir cria `truststore` o arquivo, com ou sem uma senha, no formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Example:** Crie um arquivo `truststore` PKCS12, chamado MyTrustStoreFile, com uma senha.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o `truststore` arquivo no computador ir auto-hospedado. Por exemplo, coloque o arquivo em C:\MyTrustStoreFile.
    4.  Em Azure data Factory, configure a cadeia de conexão Oracle `EncryptionMethod=1` com e o `TrustStore`valor correspondente /. `TrustStorePassword` Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Example:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
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

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de soluções Oracle. Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os [](concepts-datasets-linked-services.md), consulte DataSets. 

Para copiar dados de e para o Oracle, defina a propriedade Type do conjunto como `OracleTable`. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida `OracleTable`como. | Sim |
| tableName |O nome da tabela no banco de dados Oracle ao qual o serviço vinculado se refere. | Sim |

**Example:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Oracle. Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, [](concepts-pipelines-activities.md)consulte pipelines. 

### <a name="oracle-as-a-source-type"></a>Oracle como um tipo de origem

> [!TIP]
>
> Para carregar dados do Oracle com eficiência usando o particionamento de dados, consulte [cópia paralela do Oracle](#parallel-copy-from-oracle).

Para copiar dados do Oracle, defina o tipo de fonte na atividade de cópia `OracleSource`como. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida `OracleSource`como. | Sim |
| oracleReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Ao habilitar a carga particionada, você precisa vincular quaisquer parâmetros de partição internos correspondentes em sua consulta. Para obter exemplos, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Oracle. <br>Valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição está habilitada (ou seja `None`, não), também [`parallelCopies`](copy-activity-performance.md#parallel-copy) define a configuração na atividade de cópia. Isso determina o grau paralelo para carregar simultaneamente os dados de um Oracle Database. Por exemplo, você pode definir isso como 4. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de partição `None`não estiver. | Não |
| partitionNames | A lista de partições físicas que precisam ser copiadas. <br>Aplicar quando a opção de partição `PhysicalPartitionsOfTable`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfTabularPartitionName` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será usado pelo particionamento de intervalo para cópia paralela. Se não for especificado, a chave primária da tabela será detectada automaticamente e usada como a coluna de partição. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionColumnName` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionUpbound` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| PartitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |

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

### <a name="oracle-as-a-sink-type"></a>Oracle como um tipo de coletor

Para copiar dados para o Oracle, defina o tipo de coletor na atividade de `OracleSink`cópia como. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida `OracleSink`como. | Sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge `writeBatchSize`.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | O tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. | Não |

**Example:**

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

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua origem do Oracle para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance.md#parallel-copy) configuração na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gerar e executar quatro consultas simultaneamente com base na opção de partição e nas configurações especificadas. Cada consulta recupera uma parte dos dados do banco de dado Oracle.

É uma boa ideia habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados de seu banco de dados Oracle. Veja a seguir as configurações sugeridas para cenários diferentes:

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga total de uma tabela grande, com partições físicas.          | **Opção de partição**: Partições físicas da tabela. <br><br/>Durante a execução, Data Factory detecta automaticamente as partições físicas e copia os dados por partições. |
| Carga total de uma tabela grande, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Se não for especificado, a coluna de chave primária será usada. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, com partições físicas. | **Opção de partição**: Partições físicas da tabela.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partição**: Especifique os nomes de partição dos quais copiar dados. Se não for especificado, Data Factory detectará automaticamente as partições físicas na tabela que você especificou no conjunto de Oracle.<br><br>Durante a execução, data Factory `?AdfTabularPartitionName` substitui pelo nome real da partição e envia para o Oracle. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>Limite **superior** da partição e limite **inferior**da partição: Especifique se deseja filtrar a coluna de partição para recuperar dados somente entre o intervalo inferior e superior.<br><br>Durante a execução, data Factory `?AdfRangePartitionColumnName`substitui `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores de cada partição e envia ao Oracle. <br>Por exemplo, se a coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

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

Quando você copia dados do e para o Oracle, os mapeamentos a seguir se aplicam. Para saber mais sobre como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte Mapeamentos de [tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados Oracle | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BARQUIVO |Byte[] |
| BLOB |Byte[]<br/>(com suporte apenas no Oracle 10g e superior) |
| º |Cadeia |
| CLOB |Cadeia |
| DATE |DateTime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| LONG |Cadeia |
| LONG RAW |Byte[] |
| NCHAR |Cadeia |
| NCLOB |Cadeia |
| NUMBER |Decimal, String (se precisão > 28) |
| NVARCHAR2 |Cadeia |
| RAW |Byte[] |
| ROWID |Cadeia |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Cadeia |
| TIMESTAMP WITH TIME ZONE |Cadeia |
| UNSIGNED INTEGER |Número |
| VARCHAR2 |Cadeia |
| XML |Cadeia |

> [!NOTE]
> Não há suporte para o intervalo dos tipos de dados ano a mês e intervalo de dia para segundo.


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
