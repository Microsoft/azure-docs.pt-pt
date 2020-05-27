---
title: Copiar dados do DB2 utilizando a Azure Data Factory
description: Saiba como copiar dados do DB2 para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 3c65ed7e5fa6bb1652791eee75d4caa4c9c5f1ca
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873643"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados DB2. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de base de dados DB2 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados DB2 para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector DB2 suporta as seguintes plataformas e versões IBM DB2 com arquitetura de base de dados relacional distribuída (DRDA) SQL Access Manager (SQLAM) versão 9, 10 e 11.  Utiliza o protocolo DDM/DRDA.

* IBM DB2 para z/OS 12.1
* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

>[!TIP]
>O conector DB2 é construído em cima do Microsoft OLE DB Provider para DB2. Para resolver problemas com os erros do conector DB2, consulte [os Códigos](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)de Erro do Fornecedor de Dados .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Funcionamento de Integração fornece um controlador DB2 incorporado, pelo que não necessita de instalar manualmente qualquer controlador ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector DB2.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao DB2:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Db2** | Yes |
| conexãoString | Especifique as informações necessárias para se ligar à instância DB2.<br/> Também pode colocar palavra-passe no Cofre de Chaves Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. | Yes |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |No |

Propriedades típicas dentro da cadeia de ligação:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| servidor |Nome do servidor DB2. Pode especificar o número de porta seguindo o nome do servidor delimitado pelo cólon, por exemplo. `server:port`<br>O conector DB2 utiliza o protocolo DDM/DRDA e, por padrão, utiliza a porta 50000 se não for especificado. A porta que a sua base de dados DB2 específica utiliza pode ser diferente com base na versão e nas suas definições, por exemplo, para DB2 LUW a porta predefinida é de 50000, para AS400 a porta predefinida é de 446 ou 448 quando o TLS ativado. Consulte os seguintes documentos DB2 sobre como a porta está configurada tipicamente: [DB2 z/OS,](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html) [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm), e [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Yes |
| base de dados |Nome da base de dados DB2. |Yes |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados DB2.<br/>O valor permitido é: **Básico**. |Yes |
| o nome de utilizador |Especifique o nome do utilizador para se ligar à base de dados DB2. |Yes |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Yes |
| pacoteColeção | Especifique em que os pacotes necessários são criados automaticamente pela ADF ao consultar a base de dados. Se isto não estiver definido, a Data Factory utiliza o nome de utilizador {user} como valor predefinido. | No |
| certificadoNome Comum | Quando utilizar a encriptação Secure Sockets Layer (SSL) ou Transport Layer Security (TLS), deve introduzir um valor para o nome comum do Certificado. | No |

> [!TIP]
> Se receber uma mensagem de erro que indique , a razão é que `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` um pacote necessário não é criado para o utilizador. Por padrão, a ADF tentará criar um pacote sob a sua designação como o utilizador que usou para ligar ao DB2. Especifique a propriedade de recolha de pacotes para indicar onde pretende que a ADF crie os pacotes necessários ao consultar a base de dados.

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

Se estava a utilizar o serviço ligado ao DB2 com a seguinte carga útil, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

**Carga útil anterior:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas por conjunto de dados DB2.

Para copiar dados do DB2, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **Db2Table** | Yes |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| table | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` e para nova carga de `table` trabalho. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar o conjunto de `RelationalTable` dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **Db2Source** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estiver a usar `RelationalSource` a fonte dactilografada, continua a ser suportada como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para DB2

Ao copiar dados do DB2, os seguintes mapeamentos são utilizados desde tipos de dados DB2 para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de base de dados DB2 | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Binário |Byte[] |
| Blobs |Byte[] |
| Char |Cadeia |
| Estação Clob |Cadeia |
| Data |Datetime |
| DB2DynArray |Cadeia |
| DbClob |Cadeia |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Gráfico |Cadeia |
| Número inteiro |Int32 |
| LongVarBinary |Byte[] |
| LongVarchar |Cadeia |
| Longvargraphic |Cadeia |
| Numérico |Decimal |
| Real |Único |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| VarBinary |Byte[] |
| Rio Varchar |Cadeia |
| Vargraphic |Cadeia |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
