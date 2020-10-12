---
title: Copiar dados de e para o Microsoft Access
description: Saiba como copiar dados de e para o Microsoft Access utilizando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/28/2020
ms.openlocfilehash: 00966af4e0fc83015726d86a4c7cb5724ad38633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513365"
---
# <a name="copy-data-from-and-to-microsoft-access-using-azure-data-factory"></a>Copiar dados de e para o Microsoft Access usando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Azure Data Factory para copiar dados de uma loja de dados do Microsoft Access. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Microsoft Access é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da fonte do Microsoft Access para qualquer loja de dados de lavatórios suportados ou copiar de qualquer loja de dados de origem suportada para o afundanço do Microsoft Access. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector do Microsoft Access, tem de:

- Crie um tempo de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o controlador ODBC do Microsoft Access para a loja de dados na máquina de tempo de execução de integração.

>[!NOTE]
>A versão do Microsoft Access 2016 do controlador ODBC não funciona com este conector. Utilize a versão do condutor 2013 ou 2010.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector do Microsoft Access.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao Microsoft Access:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **MicrosoftAccess** | Sim |
| conexãoStragem | O fio de ligação ODBC excluindo a parte credencial. Pode especificar a cadeia de ligação ou utilizar o sistema DSN (Data Source Name) que configura na máquina de tempo de execução de integração (ainda precisa especificar a parte credencial no serviço ligado em conformidade).<br> Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a  `password`   configuração da cadeia de ligação.Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md)   com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação utilizada para ligar à loja de dados do Microsoft Access.<br/>Os valores permitidos são: **Básico** e **Anónimo.** | Sim |
| userName | Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. | Não |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Não |
| credencial | A parte credencial de acesso da cadeia de ligação especificada no formato de valor da propriedade específica do condutor. Marque este campo como um SecureString. | Não |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "MicrosoftAccess",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados do Microsoft Access.

Para copiar dados do Microsoft Access, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MicrosoftAccessTable** | Sim |
| tableName | Nome da tabela no Microsoft Access. | Não para a fonte (se for especificada "consulta" na fonte de atividade);<br/>Sim para afundar |

**Exemplo**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Microsoft Access.

### <a name="microsoft-access-as-source"></a>Microsoft Access como fonte

Para copiar dados do Microsoft Access, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MicrosoftAccessSource** | Sim |
| consulta | Utilize a consulta personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="microsoft-access-as-sink"></a>Microsoft Access como pia

Para copiar dados para o Microsoft Access, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para: **MicrosoftAccessSink** | Sim |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br/>Os valores permitidos são: timepan. Exemplo: "00:30:00" (30 minutos). |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0 - auto detetado) |
| preCopyScript |Especifique uma consulta SQL para a Copy Activity para executar antes de escrever dados em loja de dados em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. |Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Microsoft Access output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "MicrosoftAccessSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
