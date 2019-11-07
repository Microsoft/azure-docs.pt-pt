---
title: Copiar dados de SAP HANA usando Azure Data Factory
description: Saiba como copiar dados de SAP HANA para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 6b7f41f53ea743f8e3914512b40d3f69f595b7c8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680259"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados de SAP HANA usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de SAP HANA. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, análise e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de SAP HANA tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados SAP HANA para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector de SAP HANA dá suporte a:

- Copiando dados de qualquer versão do SAP HANA Database.
- Copiar dados de **modelos de informações do Hana** (como exibições analíticas e de cálculo) e **tabelas de linha/coluna**.
- Copiar dados usando a autenticação **básica** ou do **Windows** .

> [!TIP]
> Para copiar dados **em** SAP Hana armazenamento de dados, use o conector ODBC genérico. Consulte [SAP Hana coletor](connector-odbc.md#sap-hana-sink) com detalhes. Observe que os serviços vinculados para conector de SAP HANA e conector ODBC têm tipo diferente, portanto, não podem ser reutilizados.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector de SAP HANA, você precisa:

- Configure um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o driver ODBC SAP HANA no computador Integration Runtime. Você pode baixar o driver ODBC SAP HANA no [centro de download de software SAP](https://support.sap.com/swdc). Pesquise com a palavra-chave **SAP Hana Client para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector SAP HANA.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para SAP HANA serviço vinculado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SapHana** | Sim |
| connectionString | Especifique as informações necessárias para se conectar ao SAP HANA usando a **autenticação básica** ou a **autenticação do Windows**. Consulte os exemplos a seguir.<br>Na cadeia de conexão, servidor/porta é obrigatório (a porta padrão é 30015) e o nome de usuário e a senha são obrigatórios ao usar a autenticação básica. Para obter configurações avançadas adicionais, consulte [SAP Hana Propriedades de conexão ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Você também pode colocar a senha em Azure Key Vault e extrair a configuração de senha da cadeia de conexão. Consulte [armazenar credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| Usu | Especifique o nome de usuário ao usar a autenticação do Windows. Exemplo: `user@domain.com` | Não |
| palavra-passe | Especifique a senha para a conta de usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Exemplo: usar autenticação básica**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usar a autenticação do Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Se você estiver usando SAP HANA serviço vinculado com a seguinte carga, ele ainda terá suporte como está, enquanto você é sugerido para usar o novo no futuro.

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SAP HANA DataSet.

Para copiar dados de SAP HANA, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **SapHanaTable** | Sim |
| schema | Nome do esquema no banco de dados de SAP HANA. | Não (se for especificada "query" na origem de atividade) |
| tabela | Nome da tabela no banco de dados SAP HANA. | Não (se for especificada "query" na origem de atividade) |

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

Para copiar dados de SAP HANA, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **SapHanaSource** | Sim |
| consulta | Especifica a consulta SQL para ler dados da instância de SAP HANA. | Sim |
| packetSize | Especifica o tamanho do pacote de rede (em kilobytes) para dividir os dados em vários blocos. Se você tiver uma grande quantidade de dados a serem copiados, aumentar o tamanho do pacote poderá aumentar a velocidade de leitura de SAP HANA na maioria dos casos. O teste de desempenho é recomendado ao ajustar o tamanho do pacote. | Não.<br>O valor padrão é 2048 (2MB). |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estivesse usando `RelationalSource` fonte de cópia digitada, ela ainda tem suporte como está, enquanto você é sugerido para usar a nova no futuro.

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Ao copiar dados do SAP HANA, os seguintes mapeamentos são usados de tipos de dados SAP HANA para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados SAP HANA | Tipo de dados provisório do data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte []                         |
| Bintext            | String                         |
| BLOB               | Byte []                         |
| BOOL               | Minuciosa                           |
| CLOB               | String                         |
| DATA               | DateTime                       |
| VÍRGULA            | Vírgula                        |
| Clique             | Clique                         |
| BARRA              | Clique                         |
| VALORES            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Único                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Vírgula                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte []                         |
| STPOINTTYPE        | Byte []                         |
| TEXTO               | String                         |
| HORA               | Período                       |
| TINYINT            | Minuciosa                           |
| VARCHAR            | String                         |
| ESTAMPA          | DateTime                       |
| VARBINARY          | Byte []                         |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
