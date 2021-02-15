---
title: Copiar dados de e para lojas de dados ODBC usando Azure Data Factory
description: Saiba como copiar dados de e para lojas de dados ODBC utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 9b73e10b0ed539879e9a32d3961b6375828cc153
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389625"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados de e para lojas de dados ODBC usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-odbc-connector.md)
> * [Versão atual](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para uma loja de dados ODBC. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector ODBC é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da fonte ODBC para qualquer loja de dados de lavatórios suportados ou copiar de qualquer loja de dados de origem suportada para o lavatório ODBC. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector ODBC suporta a cópia de/para **quaisquer lojas de dados compatíveis com ODBC** usando a autenticação **Básica** ou **Anónima.** É necessário **um condutor ODBC de 64 bits.** Para a pia ODBC, a ADF suporta a versão ODBC 2.0.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector ODBC, tem de:

- Crie um tempo de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o controlador ODBC de 64 bits para a loja de dados na máquina de tempo de execução de integração.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector ODBC.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à ODBC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Odbc** | Yes |
| conexãoStragem | A cadeia de ligação excluindo a parte credencial. Pode especificar a cadeia de ligação com padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , ou utilizar o sistema DSN (Data Source Name) que configura na máquina de tempo de execução de integração `"DSN=<name of the DSN on IR machine>;"` (ainda precisa especificar a parte credencial no serviço ligado em conformidade).<br>Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes.| Yes |
| authenticationType | Tipo de autenticação utilizada para ligar à loja de dados ODBC.<br/>Os valores permitidos são: **Básico** e **Anónimo.** | Yes |
| userName | Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. | No |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| credencial | A parte credencial de acesso da cadeia de ligação especificada no formato de valor da propriedade específica do condutor. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marque este campo como um SecureString. | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Yes |

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

**Exemplo 2: utilização da autenticação anónima**

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados ODBC.

Para copiar dados de/para loja de dados compatíveis com ODBC, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **OdbcTable** | Yes |
| tableName | Nome da tabela na loja de dados ODBC. | Não para a fonte (se for especificada "consulta" na fonte de atividade);<br/>Sim para afundar |

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

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte ODBC.

### <a name="odbc-as-source"></a>ODBC como fonte

Para copiar dados da loja de dados compatíveis com o ODBC, as seguintes propriedades são suportadas na secção fonte de **origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **OdbcSource** | Yes |
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

Se estava a usar `RelationalSource` uma fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

### <a name="odbc-as-sink"></a>ODBC como pia

Para copiar dados para a loja de dados compatível com ODBC, desaperte o tipo de pia na atividade da cópia para **o OdbcSink**. As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para: **OdbcSink** | Yes |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br/>Os valores permitidos são: timepan. Exemplo: "00:30:00" (30 minutos). |No |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0 - auto detetado) |
| preCopyScript |Especifique uma consulta SQL para a Copy Activity para executar antes de escrever dados em loja de dados em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. |No |

> [!NOTE]
> Para "writeBatchSize", se não estiver definido (detetado automaticamente), a atividade de cópia deteta primeiro se o condutor suporta as operações do lote, e define-o para 10000 se o fizer, ou o definir para 1 se não o fizer. Se definir explicitamente o valor que não seja 0, a atividade de cópia honra o valor e falha no tempo de execução se o condutor não suportar as operações do lote.

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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade

Para resolver problemas de conexão, utilize o separador **de Diagnóstico** do Gestor de Configuração de Tempo de Execução de **Integração**.

1. Gestor de **configuração de tempo de execução de integração de lançamento**.
2. Mude para o **separador Diagnósticos.**
3. Na secção "Ligação de Teste", selecione o **tipo** de loja de dados (serviço ligado).
4. Especifique a **cadeia de ligação** que é utilizada para ligar à loja de dados, escolha a **autenticação** e introduza o **nome de utilizador,** **palavra-passe** e/ou **credenciais**.
5. Clique **na ligação de teste** para testar a ligação à loja de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
