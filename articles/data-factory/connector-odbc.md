---
title: Copiar dados de fontes ODBC usando Azure Data Factory
description: Saiba como copiar dados de fontes OData para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: 6513cfc5432e969fc53aa72b075af194a064d178
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892144"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados de e para armazenamentos de dados ODBC usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-odbc-connector.md)
> * [Versão atual](connector-odbc.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um armazenamento de dados ODBC. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector ODBC tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados da origem ODBC para qualquer armazenamento de dados de coletor com suporte ou copiar de qualquer armazenamento de dados de origem com suporte para o coletor ODBC. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector ODBC dá suporte à cópia de dados de/para **quaisquer armazenamentos de dados compatíveis com ODBC** usando a autenticação **básica** ou **anônima** . Um **driver ODBC de 64 bits** é necessário.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector ODBC, você precisa:

- Configure um Integration Runtime auto-hospedado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o driver ODBC de 64 bits para o armazenamento de dados no computador Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector ODBC.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do ODBC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **ODBC** | Sim |
| connectionString | A cadeia de conexão excluindo a parte da credencial. Você pode especificar a cadeia de conexão com um padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`ou usar o DSN do sistema (nome da fonte de dados) configurado na máquina Integration Runtime com `"DSN=<name of the DSN on IR machine>;"` (você ainda precisa especificar a parte da credencial no serviço vinculado adequadamente).<br>Você também pode colocar uma senha em Azure Key Vault e efetuar pull do `password` configuração fora da cadeia de conexão. Consulte [armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC.<br/>Os valores permitidos são: **básico** e **anônimo**. | Sim |
| userName | Especifique o nome de usuário se você estiver usando a autenticação básica. | Não |
| palavra-passe | Especifique a senha para a conta de usuário especificada para o nome do usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| credencial | A parte de credencial de acesso da cadeia de conexão especificada no formato de valor de propriedade específico do driver. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marque este campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: usando a autenticação básica**

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

**Exemplo 2: usando a autenticação anônima**

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de ODBC.

Para copiar dados de/para o armazenamento de dados compatível com ODBC, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **odbctable** | Sim |
| tableName | Nome da tabela no repositório de dados ODBC. | Não para origem (se "consulta" na origem da atividade for especificada);<br/>Sim para o coletor |

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

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem ODBC.

### <a name="odbc-as-source"></a>ODBC como fonte

Para copiar dados do armazenamento de dados compatível com ODBC, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **odbcname** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

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

Se você estivesse usando `RelationalSource` fonte tipada, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo no futuro.

### <a name="odbc-as-sink"></a>ODBC como coletor

Para copiar dados para o armazenamento de dados compatível com ODBC, defina o tipo de coletor na atividade de cópia como **OdbcSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como: **OdbcSink** | Sim |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: TimeSpan. Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0-detectado automaticamente) |
| preCopyScript |Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no repositório de dados em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. |Não |

> [!NOTE]
> Para "writeBatchSize", se não for definido (detectado automaticamente), a atividade de cópia detectará primeiro se o driver dá suporte a operações em lote e o definirá como 10000, se tiver, ou defini-lo como 1 se não for. Se você definir explicitamente o valor diferente de 0, a atividade de cópia respeitará o valor e falhará em tempo de execução se o driver não oferecer suporte a operações em lote.

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

## <a name="sap-hana-sink"></a>Coletor de SAP HANA

>[!NOTE]
>Para copiar dados do armazenamento de dados SAP HANA, consulte [conector de SAP Hana](connector-sap-hana.md)nativo. Para copiar dados para SAP HANA, siga esta instrução para usar o conector ODBC. Observe que os serviços vinculados para conector de SAP HANA e conector ODBC têm tipo diferente, portanto, não podem ser reutilizados.
>

Você pode copiar dados para SAP HANA banco de dado usando o conector ODBC genérico.

Configure um Integration Runtime auto-hospedado em um computador com acesso ao seu armazenamento de dados. O Integration Runtime usa o driver ODBC para SAP HANA para se conectar ao armazenamento de dados. Portanto, instale o driver se ele ainda não estiver instalado no mesmo computador. Consulte a seção [pré-requisitos](#prerequisites) para obter detalhes.

Antes de usar o coletor de SAP HANA em uma solução de Data Factory, verifique se o Integration Runtime pode se conectar ao armazenamento de dados usando as instruções na seção [solucionar problemas de conectividade](#troubleshoot-connectivity-issues) .

Crie um serviço vinculado de ODBC para vincular um armazenamento de dados de SAP HANA a uma data factory do Azure, conforme mostrado no exemplo a seguir:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

Leia o artigo desde o início para obter uma visão geral detalhada do uso de armazenamentos de dados ODBC como armazenamento de dados de origem/coletor em uma operação de cópia.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Solucionar problemas de conectividade

Para solucionar problemas de conexão, use a guia **diagnóstico** do **Integration Runtime Configuration Manager**.

1. Iniciar **Configuration Manager de Integration Runtime**.
2. Alterne para a guia **diagnóstico** .
3. Na seção "testar conexão", selecione o **tipo** de armazenamento de dados (serviço vinculado).
4. Especifique a **cadeia de conexão** usada para se conectar ao armazenamento de dados, escolha a **autenticação** e insira **o nome de usuário**, a **senha**e/ou **as credenciais**.
5. Clique em **testar conexão** para testar a conexão com o armazenamento de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
