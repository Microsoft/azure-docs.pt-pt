---
title: Copiar dados para e da Oracle com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de origem suportada para uma base de dados Oracle ou do Oracle para lojas de sink suportado com o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 3fa7612b9e4cd8a714e60879229bd0d39349494f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441445"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para Oracle com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para uma base de dados Oracle. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de uma base de dados Oracle para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para uma base de dados Oracle. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Oracle suporta as seguintes versões de uma base de dados Oracle. Também suporta a autenticação básica ou OID:

- R1 Oracle 12c (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Servidor de proxy do Oracle não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de e para uma base de dados do Oracle que não esteja acessível ao público, terá de configurar um Runtime de integração autoalojado. Para obter mais informações sobre o runtime de integração, consulte [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md). O runtime de integração proporciona um driver de Oracle incorporado. Portanto, não precisa de instalar manualmente um driver ao copiar dados de e para Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Oracle.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para ligar à instância de base de dados Oracle. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault e obter o `password` configuração fora de cadeia de ligação. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. <br><br>**Tipo de ligação suportado**: Pode usar **SID do Oracle** ou **nome do serviço Oracle** para identificar a sua base de dados:<br>– Se utilizar o SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Se utilizar o nome do serviço: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!TIP]
>Se tiver atingido o ditado de erro "ORA 01025: Parâmetro de UPI fora do intervalo"e o Oracle é de versões 8i, adicione `WireProtocolMode=1` para sua cadeia de ligação e tente novamente.

**Para ativar a encriptação numa ligação de Oracle**, tem duas opções:

1.  Para utilizar **Encryption de Triple-DES (3DES) e Advanced Encryption Standard (AES)**, no lado do servidor Oracle, vá para Oracle Advanced Security (OAS) e configurar as definições de encriptação, consulte os detalhes [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Conector ADF Oracle negocia automaticamente o método de encriptação a utilizar um que configurar no OAS quando estabelecer ligação ao Oracle.

2.  Para utilizar **SSL**, siga os passos abaixo:

    1.  Obtenha informações do certificado de SSL. Obter as informações do certificado codificado em DER do seu certificado SSL e guarde a saída (---Begin Certificate... Terminar certificado---) como um ficheiro de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** extrair informações de certificado de DERcert.cer; em seguida, guarde a saída para cert.txt

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
    
    2.  Crie o keystore ou truststore. O comando seguinte cria o ficheiro de truststore com ou sem uma palavra-passe no formato PKCS 12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** cria um ficheiro de truststore PKCS12 denominado MyTrustStoreFile com uma palavra-passe

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o arquivo de truststore no computador do Runtime de integração autoalojado, por exemplo, em C:\MyTrustStoreFile.
    4.  No ADF, configurar a cadeia de ligação do Oracle com `EncryptionMethod=1` e correspondente `TrustStore` / `TrustStorePassword`valor, por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemplo:**

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

**Exemplo: armazenar a palavra-passe no Azure Key Vault**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados Oracle.

Para copiar dados de e para a Oracle, defina a propriedade de tipo de conjunto de dados para **OracleTable**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida **OracleTable**. | Sim |
| tableName |O nome da tabela na base de dados Oracle que o serviço ligado refere-se a. | Sim |

**Exemplo:**

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de Oracle e de sink.

### <a name="oracle-as-a-source-type"></a>Oracle como um tipo de origem

Para copiar dados do Oracle, defina o tipo de origem na atividade de cópia para **OracleSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida **OracleSource**. | Sim |
| oracleReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não |

Se não especificar "oracleReaderQuery", as colunas definidas na secção "estrutura" do conjunto de dados são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para ser executado na base de dados Oracle. Se a definição do conjunto de dados não tiver "estrutura", todas as colunas são selecionadas da tabela.

**Exemplo:**

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

### <a name="oracle-as-a-sink-type"></a>Oracle como um tipo de sink

Para copiar dados para a Oracle, defina o tipo de sink na atividade de cópia para **OracleSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida **OracleSink**. | Sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Valores permitidos são um número inteiro (número de linhas). |Não (a predefinição é 10 000) |
| writeBatchTimeout | Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera.<br/>Valores permitidos são Timespan. Um exemplo é 30: 00:00 (30 minutos). | Não |
| preCopyScript | Especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados para o Oracle em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregado. | Não |

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

## <a name="data-type-mapping-for-oracle"></a>Tipo de dados de mapeamento para Oracle

Quando copia dados de e para a Oracle, os seguintes mapeamentos são utilizados entre tipos de dados Oracle para tipos de dados intermediárias do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados Oracle | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(suportado apenas no Oracle 10g e superior) |
| CHAR |String |
| CLOB |String |
| DATA |DateTime |
| NÚMERO DE VÍRGULA FLUTUANTE |Número decimal, cadeia de caracteres (se precisão > 28) |
| NÚMERO INTEIRO |Número decimal, cadeia de caracteres (se precisão > 28) |
| LONGA |String |
| HÁ MUITO TEMPO NÃO PROCESSADOS |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NÚMERO |Número decimal, cadeia de caracteres (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO LOCAL |String |
| TIMESTAMP COM O FUSO HORÁRIO |String |
| NÚMERO INTEIRO NÃO ASSINADO |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipos de dados de intervalo ano para mês e o intervalo dia em segundo lugar não são suportados.


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
