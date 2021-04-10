---
title: Copiar dados de e para IBM Informix usando Azure Data Factory
description: Saiba como copiar dados de e para a IBM Informix utilizando uma atividade de cópia num oleoduto Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: af907704862e80a2747ac064b98242a1d9d7edb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588873"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Copiar dados de e para IBM Informix usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma loja de dados IBM Informix. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Informix é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da fonte do Informix para qualquer loja de dados de sumidouro suportado, ou copiar de qualquer loja de dados de origem suportada para a pia Informix. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector Informix, tem de:

- Crie um tempo de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o controlador Informix ODBC para a loja de dados na máquina de tempo de execução de integração. Para a instalação e configuração do condutor, consulte o artigo [do Guia do Condutor Informix ODBC](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) no Centro de Conhecimento ibm para obter mais detalhes ou contacte a equipa de suporte da IBM para obter orientação de instalação do condutor.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Informix.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Informix:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Informix** | Yes |
| conexãoStragem | O fio de ligação ODBC excluindo a parte credencial. Pode especificar a cadeia de ligação ou utilizar o sistema DSN (Data Source Name) que configura na máquina de tempo de execução de integração (ainda precisa especificar a parte credencial no serviço ligado em conformidade). <br> Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes.| Yes |
| authenticationType | Tipo de autenticação utilizada para ligar à loja de dados Informix.<br/>Os valores permitidos são: **Básico** e **Anónimo.** | Yes |
| userName | Especifique o nome do utilizador se estiver a utilizar a autenticação Básica. | No |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| credencial | A parte credencial de acesso da cadeia de ligação especificada no formato de valor da propriedade específica do condutor. Marque este campo como um SecureString. | No |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Yes |

**Exemplo:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados Informix.

Para copiar dados da Informix, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **InformixTable** | Yes |
| tableName | Nome da tabela no Informix. | Não para a fonte (se for especificada "consulta" na fonte de atividade);<br/>Sim para afundar |

**Exemplo**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de imóveis suportados por fonte da Informix.

### <a name="informix-as-source"></a>Informix como fonte

Para copiar dados da Informix, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **InformixSource** | Yes |
| consulta | Utilize a consulta personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix como pia

Para copiar dados para a Informix, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para: **InformixSink** | Yes |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br/>Os valores permitidos são: timepan. Exemplo: "00:30:00" (30 minutos). |No |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0 - auto detetado) |
| preCopyScript |Especifique uma consulta SQL para a Copy Activity para executar antes de escrever dados em loja de dados em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. |No |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
