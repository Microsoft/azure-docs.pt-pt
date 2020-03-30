---
title: Copiar dados de fontes do Microsoft Access
description: Saiba como copiar dados de fontes do Microsoft Access para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: d6110065e28b8f179cd1d113107fb3508e1c3e44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892553"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Copiar dados de e para as lojas de dados do Microsoft Access utilizando a Azure Data Factory

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma loja de dados do Microsoft Access. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Microsoft Access é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da fonte do Microsoft Access para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector Microsoft Access, é necessário:

- Criar um Tempo de Integração Auto-hospedado. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.
- Instale o controlador Microsoft Access ODBC para a loja de dados na máquina De integração Runtime.

>[!NOTE]
>A versão do Microsoft Access 2016 do controlador ODBC não funciona com este conector. Utilize a versão do condutor 2013 ou 2010.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Microsoft Access.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Microsoft Access:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **MicrosoftAccess** | Sim |
| conexãoString | A cadeia de ligação ODBC excluindo a parte credencial. Pode especificar a cadeia de ligação ou utilizar o sistema DSN (Data Source Name) que configura na máquina de tempo de funcionação de integração (ainda precisa especificar a parte credencial no serviço ligado em conformidade).<br> Também pode colocar uma palavra-passe no `password` Cofre de Chaves Azure e retirar a configuração da cadeia de ligação.Consulte [as credenciais da Loja no Cofre](store-credentials-in-key-vault.md) de Chaves Azure com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação utilizada para se ligar à loja de dados do Microsoft Access.<br/>Os valores permitidos são: **Básico** e **Anónimo.** | Sim |
| userName | Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. | Não |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| credencial | A parte credencial de acesso da cadeia de ligação especificada no formato de valor de propriedade específico do condutor. Marque este campo como um SecureString. | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

**Exemplo:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do Microsoft Access.

Para copiar dados do Microsoft Access, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MicrosoftAccessTable** | Sim |
| tableName | Nome da tabela no Microsoft Access. | Não para fonte (se for especificada a "consulta" na fonte de atividade);<br/>Sim para afundar |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Microsoft Access.

### <a name="microsoft-access-as-source"></a>Acesso ao Microsoft como fonte

Para copiar dados da loja de dados compatível com o Microsoft Access, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **MicrosoftAccessSource** | Sim |
| consulta | Use a consulta personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
