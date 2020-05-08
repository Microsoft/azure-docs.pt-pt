---
title: Copiar dados de fontes da ODBC utilizando a Fábrica de Dados Azure
description: Saiba como copiar dados de fontes OData para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 71b05d8607c174dbe9298a1c02f4927ed2218374
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891423"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados de e para as lojas de dados da ODBC utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-odbc-connector.md)
> * [Versão atual](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para uma loja de dados ODBC. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector ODBC é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da fonte da ODBC para qualquer loja de dados de sink suportado, ou copiar de qualquer loja de dados de origem suportada para a pia ODBC. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector ODBC suporta a cópia de dados de/para **quaisquer lojas de dados compatíveis com ODBC** utilizando a autenticação **Básica** ou **Anónima.** É necessário um **condutor odBC de 64 bits.** Para a pia ODBC, a ADF suporta a versão 2.0 da Versão 2.0 da ADF.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector ODBC, é necessário:

- Criar um Tempo de Integração Auto-hospedado. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.
- Instale o controlador ODBC de 64 bits para a loja de dados na máquina De integração Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector ODBC.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à ODBC:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Odbc** | Sim |
| conexãoString | A corda de ligação excluindo a parte credencial. Pode especificar a cadeia de `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`ligação com padrões como , ou utilizar o sistema DSN (Data Source Name) com o qual configura na máquina de tempo de funcionação de integração `"DSN=<name of the DSN on IR machine>;"` (ainda precisa especificar a parte credencial no serviço ligado em conformidade).<br>Também pode colocar uma palavra-passe no `password` Cofre de Chaves Azure e retirar a configuração da cadeia de ligação.Consulte [as credenciais da Loja no Cofre](store-credentials-in-key-vault.md) de Chaves Azure com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação utilizada para se ligar à loja de dados ODBC.<br/>Os valores permitidos são: **Básico** e **Anónimo.** | Sim |
| userName | Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. | No |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | No |
| credencial | A parte credencial de acesso da cadeia de ligação especificada no formato de valor de propriedade específico do condutor. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marque este campo como um SecureString. | No |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

**Exemplo 1: utilização da autenticação básica**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
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

**Exemplo 2: utilização da autenticação Anónima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da ODBC.

Para copiar dados de/para a loja de dados compatíveis com o ODBC, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **OdbcTable** | Sim |
| tableName | Nome da tabela na loja de dados ODBC. | Não para fonte (se for especificada a "consulta" na fonte de atividade);<br/>Sim para afundar |

**Exemplo**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Se estiver `RelationalTable` a utilizar o conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte da ODBC.

### <a name="odbc-as-source"></a>ODBC como fonte

Para copiar dados da loja de dados compatíveis com a ODBC, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **OdbcSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estiver `RelationalSource` a usar a fonte dactilografada, continua a ser suportada como está, enquanto é sugerido que utilize o novo para a frente.

### <a name="odbc-as-sink"></a>ODBC como pia

Para copiar dados para uma loja de dados compatível com o ODBC, delineie o tipo de pia na atividade de cópia para **OdbcSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para: **OdbcSink** | Sim |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair.<br/>Os valores permitidos são: timepan. Exemplo: "00:30:00" (30 minutos). |No |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (padrão é 0 - auto detetado) |
| preCopyScript |Especifique uma consulta SQL para a Atividade de Cópia executar antes de escrever dados na loja de dados em cada execução. Pode usar esta propriedade para limpar os dados pré-carregados. |No |

> [!NOTE]
> Para "writeBatchSize", se não for definido (detetado automaticamente), a atividade de cópia deteta primeiro se o condutor suporta as operações do lote, e o define para 10000 se o fizer, ou o define para 1 se não o fizer. Se definir explicitamente o valor diferente de 0, a atividade de cópia honra o valor e falha no tempo de funcionação se o condutor não apoiar as operações do lote.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade

Para resolver problemas de ligação, utilize o separador **de diagnóstico** do Gestor de Configuração do Tempo de **Integração**.

1. Gestor **de configuração**de tempo de execução de integração de lançamento.
2. Mude para o separador **Diagnósticos.**
3. Na secção "Ligação de Teste", selecione o **tipo** de loja de dados (serviço ligado).
4. Especifique a cadeia de **ligação** que é utilizada para se ligar ao depósito de dados, escolha a **autenticação** e introduza o nome do **utilizador,** **palavra-passe,** e/ou **credenciais**.
5. Clique na **ligação test** para testar a ligação à loja de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
