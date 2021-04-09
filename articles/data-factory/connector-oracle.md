---
title: Copie dados de e para a Oracle utilizando a Azure Data Factory
description: Saiba como copiar dados de lojas de origem suportadas para uma base de dados Oracle, ou da Oracle para lojas de lavatórios suportadas, utilizando a Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9e6be88af13d5dd7ddceba32ec08cab54ca5e3a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587292"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copie os dados de e para a Oracle utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de e para uma base de dados oracle. Baseia-se na [visão geral](copy-activity-overview.md)da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Oracle é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma base de dados oracle para qualquer loja de dados de lavatórios suportados. Também pode copiar dados de qualquer data de origem suportada para uma base de dados Oracle. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Oracle suporta:

- As seguintes versões de uma base de dados oracle:
    - Oráculo 19c R1 (19.1) e superior
    - Oráculo 18c R1 (18.1) e superior
    - Oráculo 12c R1 (12.1) e superior
    - Oráculo 11g R1 (11.1) e superior
    - Oráculo 10g R1 (10.1) e superior
    - Oráculo 9i R2 (9.2) e superior
    - Oráculo 8i R3 (8.1.7) e superior
    - Serviço De Exadata de Nuvem de Nuvem de Banco oracle
- Cópia paralela de uma fonte do Oráculo. Consulte a cópia paralela da secção [Oráculo](#parallel-copy-from-oracle) para obter mais detalhes.

> [!Note]
> O servidor de procuração da Oracle não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

O tempo de integração proporciona um condutor Oráculo embutido. Portanto, não é necessário instalar manualmente um controlador quando copiar dados de e para a Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Oracle.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

O serviço ligado à Oracle suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **o Oráculo.** | Yes |
| conexãoStragem | Especifica a informação necessária para ligar à placa da Base de Dados Oráculo. <br/>Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no Cofre da Chave Azure](store-credentials-in-key-vault.md) com mais detalhes. <br><br>**Tipo de ligação suportado**: Pode utilizar **o Oracle SID** ou o Oracle Service **Name** para identificar a sua base de dados:<br>- Se utilizar SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Se utilizar o Nome de Serviço: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Para opções avançadas de ligação nativa oracle, pode optar por adicionar uma entrada no [TNSNAMES. O ficheiro ORA](http://www.orafaq.com/wiki/Tnsnames.ora) no servidor Oracle e no serviço ligado ADF Oracle, opte por utilizar o tipo de ligação Oracle Service Name e configurar o nome de serviço correspondente. | Yes |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |No |

>[!TIP]
>Se tiver um erro, "ORA-01025: Parâmetro UPI fora do alcance", e a sua versão Oracle é 8i, adicione `WireProtocolMode=1` à sua cadeia de ligação. Então tente de novo.

Se tiver várias instâncias Oráculos para o cenário de failover, pode criar o serviço ligado à Oracle e preencher o anfitrião principal, porta, nome de utilizador, palavra-passe, etc., e adicionar uma nova "**propriedades de conexão adicionais**" com o nome de propriedade como `AlternateServers` e valor como - não perca os `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` suportes e preste atenção aos cólons ( `:` ) como separador. Como exemplo, o seguinte valor dos servidores alternativos define dois servidores de base de dados alternativos para falha de ligação: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Mais propriedades de conexão que pode definir em cadeia de ligação por sua caixa:

| Propriedade | Descrição | Valores permitidos |
|:--- |:--- |:--- |
| Tamanho arraysize |O número de bytes que o conector pode obter numa única viagem de ida e volta de rede. Por exemplo, `ArraySize=‭10485760‬` .<br/><br/>Os valores maiores aumentam a produção reduzindo o número de vezes para recolher dados através da rede. Valores menores aumentam o tempo de resposta, uma vez que há menos atrasos à espera que o servidor transmita dados. | Um inteiro de 1 a 4294967296 (4 GB). O valor predefinido é `60000`. O valor 1 não define o número de bytes, mas indica a atribuição de espaço para exatamente uma linha de dados. |

Para ativar a encriptação na ligação Oracle, tem duas opções:

-   Para utilizar **a Encriptação Triple-DES (3DES) e o Padrão avançado de encriptação (AES)**, no lado do servidor Oracle, vá à Oracle Advanced Security (OAS) e configufique as definições de encriptação. Para mais detalhes, consulte esta [documentação do Oráculo.](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) O conector Oracle Application Development Framework (ADF) negoceia automaticamente o método de encriptação para utilizar o que configura na OAS ao estabelecer uma ligação à Oráculo.

-   Para utilizar **o TLS:**

    1.  Obtenha a informação do certificado TLS/SSL. Obtenha as regras de codificação de codificação distinguidas (DER)do seu certificado TLS/SSL e guarde a saída (----- Certificado de Início ... Certificado final -----) como ficheiro de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** Extrair informações de cert do DERcert.cer e, em seguida, guardar a saída para cert.txt.

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
    
    2.  Construir o `keystore` `truststore` ou. O seguinte comando cria o `truststore` ficheiro, com ou sem senha, em formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** Crie um ficheiro PKCS12, `truststore` chamado MyTrustStoreFile, com uma palavra-passe.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o `truststore` ficheiro na máquina de infravermelhos auto-hospedada. Por exemplo, coloque o ficheiro em C:\MyTrustStoreFile.
    4.  Na Azure Data Factory, configuure a cadeia de ligação Oráculo com `EncryptionMethod=1` e o `TrustStore` / `TrustStorePassword` valor correspondente. Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

**Exemplo: armazenar senha em Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Oracle. Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md). 

Para copiar dados de e para a Oracle, defina a propriedade tipo do conjunto de dados para `OracleTable` . As seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para `OracleTable` . | Yes |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| table | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
| tableName | Nome da tabela/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não para a fonte, sim para a pia |

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

Esta secção fornece uma lista de propriedades suportadas pela fonte e pia do Oráculo. Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades,](concepts-pipelines-activities.md)consulte Pipelines . 

### <a name="oracle-as-source"></a>Oráculo como fonte

>[!TIP]
>Para carregar os dados da Oracle de forma eficiente utilizando a partilha de dados, saiba mais com [cópia paralela da Oracle](#parallel-copy-from-oracle).

Para copiar os dados da Oracle, desagrafe o tipo de origem na atividade da cópia para `OracleSource` . As seguintes propriedades são suportadas na secção **de origem** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para `OracleSource` . | Yes |
| OracleReaderQuery | Utilize a consulta SQL personalizada para ler dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando ativar a carga partida, tem de ligar os parâmetros de partição incorporados correspondentes na sua consulta. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | No |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados da Oracle. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente dados de uma base de dados Oráculo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | No |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` estiver. | No |
| partitionNames | A lista de divisórias físicas que precisam de ser copiadas. <br>Aplicar quando a opção de partição for `PhysicalPartitionsOfTable` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfTabularPartitionName` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | No |
| partitionColumnName | Especificar o nome da coluna de origem **no tipo inteiro** que será utilizado por partição de gama para cópia paralela. Se não for especificada, a chave primária da tabela é detetada automaticamente e utilizada como coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem,  `?AdfRangePartitionColumnName` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | No |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfRangePartitionUpbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | No |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [Oráculo.](#parallel-copy-from-oracle) | No |

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

Para copiar os dados para a Oracle, desa um tipo de pia na atividade da cópia para `OracleSink` . As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para `OracleSink` . | Yes |
| escreverBatchSize | Insere dados na tabela SQL quando o tamanho do tampão chegar `writeBatchSize` .<br/>Os valores permitidos são Inteiros (número de linhas). |Não (o padrão é 10.000) |
| escreverBatchTimeout | O tempo de espera para o funcionamento do inserção do lote deve ser concluído antes do tempo de se esgotar.<br/>Os valores permitidos são o Timespan. Um exemplo é 00:30:00 (30 minutos). | No |
| preCopyScript | Especifique uma consulta SQL para que a atividade da cópia seja executada antes de escrever dados na Oracle em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. | No |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

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

## <a name="parallel-copy-from-oracle"></a>Cópia paralela da Oráculo

O conector Oracle da Fábrica de Dados fornece partição de dados incorporada para copiar dados da Oracle em paralelo. Pode encontrar opções de partição de dados no separador **'Fonte'** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando ativa a cópia dividida, a Data Factory executa consultas paralelas contra a sua fonte Oráculo para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua base de dados Oráculo.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados a partir da base de dados da Oracle. São sugeridas configurações para diferentes cenários. Ao copiar dados para a loja de dados baseada em ficheiros, é recomerado para escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

| Scenario                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande, com divisórias físicas.          | **Opção de partição**: Divisórias físicas da tabela. <br><br/>Durante a execução, a Data Factory deteta automaticamente as divisórias físicas e copia dados por partições. |
| Carga completa da mesa grande, sem divisórias físicas, enquanto com uma coluna de inteiro para partição de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição. Se não for especificada, utiliza-se a coluna-chave primária. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, com divisórias físicas. | **Opção de partição**: Divisórias físicas da tabela.<br>**Consulta:** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Nome da partição**: Especifique o(s) nome de partição para copiar dados de. Se não for especificado, a Data Factory deteta automaticamente as divisórias físicas na tabela especificadas no conjunto de dados Oráculo.<br><br>Durante a execução, a Data Factory `?AdfTabularPartitionName` substitui-se pelo nome de partição real e envia para o Oráculo. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem divisórias físicas, enquanto com uma coluna inteiro para partição de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição. Pode dividir-se contra a coluna com o tipo de dados inteiro.<br>**Limite superior** e **partição inferior**: Especifique se pretende filtrar contra coluna de partição para obter dados apenas entre a gama inferior e superior.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui, `?AdfRangePartitionUpbound` e com o nome da coluna real e `?AdfRangePartitionLowbound` intervalos de valor para cada partição, e envia para o Oráculo. <br>Por exemplo, se a coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópias paralelas como 4, a Data Factory recupera dados por 4 divisórias. Os seus IDs estão entre [1,20], [21, 40], [41, 60], e [61, 80], respectivamente. |

> [!TIP]
> Ao copiar dados de uma tabela não dividida, pode utilizar a opção de partição "Dynamic range" para dividir contra uma coluna inteiro. Se os seus dados de origem não tiverem este tipo de coluna, pode aproveitar [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) função na consulta de origem para gerar uma coluna e usá-la como coluna de partição.

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

**Exemplo: consulta com partição de gama dinâmica**

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

## <a name="data-type-mapping-for-oracle"></a>Mapeamento do tipo de dados para o oráculo

Quando copia dados de e para a Oracle, aplicam-se os seguintes mapeamentos. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados oráculo | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(apenas suportado no Oráculo 10g ou superior) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLUTUAR |Decimal, String (se a precisão > 28) |
| INTEGER |Decimal, String (se a precisão > 28) |
| Longo |String |
| LONGO CRU |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NÚMERO (p,s) |Decimal, String (se p > 28) |
| NÚMERO sem precisão e escala |Double (Duplo) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| CARIMBO DE DATA/HORA |DateTime |
| TIMETAMP COM FUSO HORÁRIO LOCAL |String |
| TIMETAMP COM FUSO HORÁRIO |String |
| INTEIRO NÃO ASSINADO |Número |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipos de dados INTERVALO DE ANO A MÊS e INTERVALO DIA A SEGUNDO não são suportados.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
