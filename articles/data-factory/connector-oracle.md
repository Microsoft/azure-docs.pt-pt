---
title: Copiar dados de e para a Oracle utilizando a Azure Data Factory
description: Saiba como copiar dados de lojas de origem suportada para uma base de dados da Oracle, ou da Oracle para lojas de sumidouro suportadas, utilizando a Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 874c685491774e2a318ae0a8b7394945a51b2f7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244514"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oráculo utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como usar a atividade de cópia na Azure Data Factory para copiar dados de e para uma base de dados da Oracle. Baseia-se na visão geral da atividade da [cópia.](copy-activity-overview.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Oracle é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma base de dados da Oracle para qualquer loja de dados suportada. Também pode copiar dados de qualquer loja de dados de origem suportada para uma base de dados da Oracle. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Oracle suporta:

- As seguintes versões de uma base de dados da Oracle:
    - Oráculo 18c R1 (18.1) e superior
    - Oráculo 12c R1 (12.1) e superior
    - Oráculo 11g R1 (11.1) e superior
    - Oráculo 10g R1 (10.1) e superior
    - Oráculo 9i R2 (9.2) e superior
    - Oráculo 8i R3 (8.1.7) e superior
    - Serviço de Exadata cloud cloud da Oracle
- Cópia paralela de uma fonte oráculo. Consulte a [cópia paralela da](#parallel-copy-from-oracle) secção Oracle para obter mais detalhes.

> [!Note]
> O servidor proxy da Oracle não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

O tempo de integração proporciona um condutor oráculo embutido. Portanto, não é necessário instalar manualmente um controlador quando copia dados de e para a Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Oracle.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

O serviço ligado ao Oracle suporta as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **Oráculo**. | Sim |
| conexãoString | Especifica as informações necessárias para se ligar à instância da Base de Dados Oracle. <br/>Também pode colocar uma palavra-passe no Cofre `password` de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. <br><br>Tipo de **ligação suportada:** Pode utilizar o **Oracle SID** ou **o Oracle Service Name** para identificar a sua base de dados:<br>- Se utilizar sid:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Se utilizar o Nome de Serviço:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Para opções avançadas de ligação nativa oracle, pode optar por adicionar uma entrada em [TNSNAMES. O](http://www.orafaq.com/wiki/Tnsnames.ora) ficheiro ORA no servidor Oracle e no serviço ligado ao Oracle, opte por utilizar o tipo de ligação Oracle Service Name e configurar o nome de serviço correspondente. | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, é utilizado o tempo de execução de integração azure padrão. |Não |

>[!TIP]
>Se tiver um erro, "ORA-01025: Parâmetro UPI fora de alcance", `WireProtocolMode=1` e a sua versão Oracle é 8i, adicione à sua cadeia de ligação. Então tente de novo.

Mais propriedades de ligação que pode definir na cadeia de ligação por seu caso:

| Propriedade | Descrição | Valores permitidos |
|:--- |:--- |:--- |
| Tamanho de matriz |O número de bytes que o conector pode obter numa única viagem de ida e volta em rede. Por exemplo, `ArraySize=‭10485760‬`.<br/><br/>Valores maiores aumentam a sua entrada reduzindo o número de vezes para recolher dados em toda a rede. Os valores mais pequenos aumentam o tempo de resposta, uma vez que há menos atraso à espera que o servidor transmita dados. | Um inteiro de 1 a 4294967296 (4 GB). O valor `60000`predefinido é . O valor 1 não define o número de bytes, mas indica a atribuição de espaço para exatamente uma linha de dados. |

Para permitir a encriptação na ligação Oracle, tem duas opções:

-   Para utilizar **a encriptação Triple-DES (3DES) e advanced Encryption Standard (AES)**, no lado do servidor Oracle, vá ao Oracle Advanced Security (OAS) e configure as definições de encriptação. Para mais detalhes, consulte esta documentação do [Oráculo.](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) O conector Oracle Application Development Framework (ADF) negoceia automaticamente o método de encriptação para utilizar o que configura na OAS ao estabelecer uma ligação à Oráculo.

-   Para utilizar **o SSL:**

    1.  Obtenha a informação do certificado SSL. Obtenha as distintas regras de codificação (DER) codificadas informações do certificado do seu certificado SSL, e guarde a saída (----- Certificado iniciar... Certificado final -----) como ficheiro de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** Extrair informações cert de DERcert.cer e, em seguida, guardar a saída para cert.txt.

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
    
    2.  Construa `keystore` `truststore`o ou . O seguinte comando `truststore` cria o ficheiro, com ou sem senha, no formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** Crie um ficheiro `truststore` PKCS12, chamado MyTrustStoreFile, com uma palavra-passe.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque `truststore` o ficheiro na máquina de infravermelhos auto-hospedada. Por exemplo, coloque o ficheiro em C:\MyTrustStoreFile.
    4.  Na Azure Data Factory, configure `EncryptionMethod=1` a cadeia `TrustStore` / `TrustStorePassword`de ligação Oracle e o valor correspondente. Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

**Exemplo: palavra-passe de loja no Cofre de Chaves Azure**

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
## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados oracle. Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md). 

Para copiar dados de e para a Oracle, `OracleTable`detete a propriedade do tipo do conjunto de dados para . As seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de `OracleTable`dados deve ser definida para . | Sim |
| schema | Nome do esquema. |Não para a fonte, sim para afundar  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para afundar  |
| tableName | Nome da mesa/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `schema` `table`carga de trabalho, use e . | Não para a fonte, sim para afundar |

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

Esta secção fornece uma lista de propriedades suportadas pela fonte do Oráculo e pia. Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oráculo como fonte

>[!TIP]
>Para carregar os dados da Oracle de forma eficiente utilizando a partilha de dados, saiba mais com [a cópia paralela da Oracle](#parallel-copy-from-oracle).

Para copiar dados da Oracle, delineie `OracleSource`o tipo de origem na atividade da cópia para . As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de `OracleSource`atividade de cópia deve ser definida para . | Sim |
| oracleReaderQuery | Utilize a consulta SQL personalizada para ler dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando ativa a carga divisória, tem de ligar os parâmetros correspondentes de partição incorporados na sua consulta. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | Não |
| partilhaOpOp | Especifica as opções de partilha de dados utilizadas para carregar dados da Oracle. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** , e **DynamicRange**.<br>Quando uma opção de partição `None`é ativada (isto é, não), o grau de [`parallelCopies`](copy-activity-performance.md#parallel-copy) paralelismo para carregar simultaneamente dados de uma base de dados da Oracle é controlado pela definição da atividade da cópia. | Não |
| partiçãoDefinições | Especifique o grupo das definições para a partilha de dados. <br>Aplicar quando a opção `None`de partição não for . | Não |
| nomes de partição | A lista de divisórias físicas que precisa de ser copiada. <br>Aplicar quando a `PhysicalPartitionsOfTable`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfTabularPartitionName` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro** que será utilizado por divisórias de alcance para cópia paralela. Se não especificada, a chave principal da tabela é detetada automaticamente e utilizada como coluna de partição. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionColumnName` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | Não |
| partiçãoUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionUpbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | Não |
| partiçãoLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se utilizar uma consulta para recuperar os `?AdfRangePartitionLowbound` dados de origem, ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | Não |

**Exemplo: copiar dados utilizando uma consulta básica sem partição**

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

### <a name="oracle-as-sink"></a>Oráculo como pia

Para copiar dados para a Oracle, delineie o tipo de pia na atividade da cópia para `OracleSink`. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de `OracleSink`atividade sicopiadora deve ser definida para . | Sim |
| escreverBatchSize | Insere os dados na tabela SQL `writeBatchSize`quando o tamanho do tampão atinge .<br/>Os valores permitidos são Inteiros (número de linhas). |Não (o padrão é de 10.000) |
| escreverBatchTimeout | O tempo de espera para a operação de inserção do lote esteja concluído antes de sair.<br/>Os valores permitidos são Timespan. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para que a atividade da cópia seja executada antes de escrever dados no Oracle em cada execução. Pode usar esta propriedade para limpar os dados pré-carregados. | Não |

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

## <a name="parallel-copy-from-oracle"></a>Cópia paralela do Oráculo

O conector Data Factory Oracle fornece a partilha de dados incorporados para copiar dados da Oracle em paralelo. Pode encontrar opções de partilha de dados no separador **Fonte** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Oráculo para carregar dados por divisórias. O grau paralelo é [`parallelCopies`](copy-activity-performance.md#parallel-copy) controlado pela regulação da atividade da cópia. Por exemplo, se `parallelCopies` definir para quatro, data Factory gera simultaneamente e executa quatro consultas com base na sua opção e configurações especificadas de partição, e cada consulta recupera uma parte dos dados da sua base de dados Oracle.

É sugerido que permita cópias paralelas com partilha de dados especialmente quando carrega uma grande quantidade de dados da sua base de dados Oracle. As seguintes são configurações sugeridas para diferentes cenários. Ao copiar dados para uma loja de dados baseada em ficheiros, é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga total de mesa grande, com divisórias físicas.          | **Opção de partição**: Divisórias físicas da mesa. <br><br/>Durante a execução, a Data Factory deteta automaticamente as divisórias físicas e copia dados por divisórias. |
| Carga total de mesa grande, sem divisórias físicas, enquanto com uma coluna inteiro para partição de dados. | **Opções de partição**: Partição dinâmica.<br>**Coluna de partição**: Especifique a coluna utilizada para os dados da partilha. Se não especificada, a coluna principal da tecla é utilizada. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, com divisórias físicas. | **Opção de partição**: Divisórias físicas da mesa.<br>**Consulta:** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partilha**: Especifique o nome ou os nomes de partição para copiar dados de. Se não especificado, a Data Factory deteta automaticamente as divisórias físicas na tabela especificada no conjunto de dados oracle.<br><br>Durante a execução, `?AdfTabularPartitionName` data Factory substitui com o nome real da partição, e envia para a Oracle. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, sem divisórias físicas, enquanto com uma coluna inteiro para a partilha de dados. | **Opções de partição**: Partição dinâmica.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna utilizada para os dados da partilha. Pode dividir-se contra a coluna com o tipo de dados inteiros.<br>**Divisória superior ligada** e **divisória inferior:** Especifique se pretende filtrar contra a coluna de divisórias para recuperar dados apenas entre a gama inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Data Factory substitui, e com o nome real da coluna e gamas de valor para cada partição, e envia para a Oráculo. <br>Por exemplo, se a sua coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com cópia paralela definida como 4, data Factory recupera dados por 4 divisórias. As suas identificações estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

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

**Exemplo: consulta com partição dinâmica de gama**

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

## <a name="data-type-mapping-for-oracle"></a>Mapeamento de tipo de dados para Oráculo

Quando copia dados de e para a Oracle, aplicam-se os seguintes mapeamentos. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados oráculo | Data Factory tipo de dados provisórios |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(apenas suportado no Oráculo 10g ou superior) |
| CHAR |Cadeia |
| CLOB |Cadeia |
| DATA |DateTime |
| BOIA |Decimal, Corda (se precisão > 28) |
| INTEGER |Decimal, Corda (se precisão > 28) |
| Longo |Cadeia |
| LONG RAW |Byte[] |
| NCHAR |Cadeia |
| NCLOB |Cadeia |
| NÚMERO |Decimal, Corda (se precisão > 28) |
| NVARCHAR2 |Cadeia |
| CRU |Byte[] |
| ROWID |Cadeia |
| CARIMBO TEMPORAL |DateTime |
| CARIMBO DE TEMPO COM FUSO HORÁRIO LOCAL |Cadeia |
| CARIMBO DE TEMPO COM FUSO HORÁRIO |Cadeia |
| INTEIRO NÃO ASSINADO |Número |
| VARCHAR2 |Cadeia |
| XML |Cadeia |

> [!NOTE]
> Os tipos de dados INTERVAL ANO A MÊS e INTERVAL DAY To SECOND não são suportados.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
