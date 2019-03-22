---
title: Copiar dados de origens ODBC com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de origens de OData para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: f14c8f8ef9f0e59ac35dd7346bf37cc07f2cfb19
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58163859"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados entre arquivos de dados ODBC com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-odbc-connector.md)
> * [Versão atual](connector-odbc.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para um arquivo de dados ODBC. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de origem ODBC para qualquer arquivo de dados de sink suportados ou copiar de qualquer arquivo de dados de origem suportada para o sink ODBC. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector ODBC suporta a cópia de dados de/para **quaisquer arquivos de dados compatível com ODBC** usando **básica** ou **anónimo** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector do ODBC, terá de:

- Configure um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o controlador ODBC para o arquivo de dados na máquina do Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do ODBC.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do ODBC:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **Odbc** | Sim |
| connectionString | A cadeia de ligação, excluindo a parte de credencial. Pode especificar a cadeia de ligação com o padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, ou usar o sistema de DSN (nome da origem de dados) que configurou na máquina do Integration Runtime com `"DSN=<name of the DSN on IR machine>;"` (precisa ainda especificar a parte da credencial no serviço ligado em conformidade).<br>Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md).| Sim |
| authenticationType | Tipo de autenticação utilizado para ligar ao arquivo de dados ODBC.<br/>Valores permitidos são: **Básica** e **anônimo**. | Sim |
| userName | Especifique o nome de utilizador se estiver a utilizar autenticação básica. | Não |
| palavra-passe | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| credencial | A parte de credencial de acesso da cadeia de ligação especificada no formato de valores de propriedade específicos de driver. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marca esse campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: usando a autenticação básica**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**Exemplo 2: utilizar a autenticação anónima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do ODBC.

Para copiar dados de/para o arquivo de dados compatível com ODBC, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela no arquivo de dados ODBC. | Não para a origem (se for especificado "consultar" na origem de atividade);<br/>Sim para o sink |

**Exemplo**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem ODBC.

### <a name="odbc-as-source"></a>ODBC como origem

Para copiar dados de arquivo de dados compatível com ODBC, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC como sink

Para copiar dados para o arquivo de dados compatível com ODBC, defina o tipo de sink na atividade de cópia para **OdbcSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **OdbcSink** | Sim |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera.<br/>Valores permitidos são: intervalo de tempo. Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Valores permitidos são: número inteiro (número de linhas). |Não (a predefinição é 0 - detetado automaticamente) |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados no arquivo de dados em cada execução. Pode utilizar esta propriedade para limpar os dados carregados previamente. |Não |

> [!NOTE]
> Para "writeBatchSize", se não estiver definido (detetado automaticamente), atividade de cópia primeiro Deteta se o controlador suporta operações de lote e defina-o como 10000 se assim for ou definido como 1, se não. Se definir explicitamente o valor diferente de 0, a atividade de cópia respeita o valor e falha no tempo de execução, se o controlador não suporta operações de lote.

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

## <a name="ibm-informix-source"></a>IBM Informix source

Pode copiar dados da base de dados IBM Informix através do conector genérico do ODBC.

Configure o Integration Runtime autoalojado num computador com acesso ao seu arquivo de dados. O Runtime de integração utiliza o controlador ODBC para Informix para ligar ao arquivo de dados. Portanto, instale o controlador se não estiver já instalado na mesma máquina. Por exemplo, pode utilizar o controlador "Controlador ODBC do IBM INFORMIX (64-bit)". Ver [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar a origem do Informix numa solução de fábrica de dados, verifique se o Runtime de integração pode ligar-se ao armazenamento de dados com as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ODBC ligado para ligar um arquivo de dados IBM Informix para uma fábrica de dados do Azure, conforme mostrado no exemplo a seguir:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

Leia o artigo desde o início para uma visão geral detalhada da utilização de dados ODBC armazena como arquivos de dados de origem/sink de uma operação de cópia.

## <a name="microsoft-access-source"></a>Origem do Microsoft Access

Pode copiar dados da base de dados do Microsoft Access através do conector genérico do ODBC.

Configure o Integration Runtime autoalojado num computador com acesso ao seu arquivo de dados. O Runtime de integração utiliza o controlador ODBC do Microsoft Access para ligar ao arquivo de dados. Portanto, instale o controlador se não estiver já instalado na mesma máquina. Ver [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar a origem do Microsoft Access numa solução de fábrica de dados, verifique se o Runtime de integração pode ligar-se ao armazenamento de dados com as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ODBC ligado para ligar uma base de dados do Microsoft Access para um Azure data factory, conforme mostrado no exemplo a seguir:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

Leia o artigo desde o início para uma visão geral detalhada da utilização de dados ODBC armazena como arquivos de dados de origem/sink de uma operação de cópia.

## <a name="sap-hana-sink"></a>Sink de SAP HANA

>[!NOTE]
>Para copiar dados de arquivo de dados do SAP HANA, consulte nativo [conector do SAP HANA](connector-sap-hana.md). Para copiar dados para SAP HANA, siga essa instrução para utilizar o conector do ODBC. Portanto, não pode ser reutilizada observe que os serviços ligados para o conector do SAP HANA e o conector do ODBC são com tipo diferente.
>

Pode copiar dados para a base de dados do SAP HANA com o conector do ODBC genérico.

Configure o Integration Runtime autoalojado num computador com acesso ao seu arquivo de dados. O Runtime de integração utiliza o controlador ODBC do SAP HANA para ligar ao arquivo de dados. Portanto, instale o controlador se não estiver já instalado na mesma máquina. Ver [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar o sink de SAP HANA numa solução de fábrica de dados, verifique se o Runtime de integração pode ligar-se ao armazenamento de dados com as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ODBC ligado para ligar um arquivo de dados do SAP HANA para uma fábrica de dados do Azure, conforme mostrado no exemplo a seguir:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

Leia o artigo desde o início para uma visão geral detalhada da utilização de dados ODBC armazena como arquivos de dados de origem/sink de uma operação de cópia.

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade

Para resolver problemas de ligação, utilize o **diagnóstico** separador de **Gestor de configuração do Runtime de integração**.

1. Inicie **Gestor de configuração do Runtime de integração**.
2. Mude para o **diagnóstico** separador.
3. Na secção "Testar ligação", selecione o **tipo** de dados armazenar (serviço ligado).
4. Especifique a **cadeia de ligação** que é utilizado para ligar ao arquivo de dados, escolha a **autenticação** e introduza **nome de utilizador**, **palavra-passe**, e/ou **credenciais**.
5. Clique em **Testar ligação** para testar a ligação ao arquivo de dados.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
