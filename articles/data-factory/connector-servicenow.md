---
title: Copiar dados do ServiceNow
description: Saiba como copiar dados do ServiceNow para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926176"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados do ServiceNow usando a Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do ServiceNow. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector ServiceNow é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do ServiceNow para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector ServiceNow.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ServiceNow:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **ServiceNow** | Sim |
| endpoint | O ponto final do servidor`http://<instance>.service-now.com`ServiceNow ( ).  | Sim |
| authenticationType | O tipo de autenticação a utilizar. <br/>Os valores permitidos são: **Básico,** **OAuth2** | Sim |
| o nome de utilizador | O nome do utilizador usado para ligar ao servidor ServiceNow para autenticação Basic e OAuth2.  | Sim |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador para autenticação Basic e OAuth2. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| clientId | O ID do cliente para autenticação OAuth2.  | Não |
| clienteSecret | O segredo do cliente para a autenticação OAuth2. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| useEncryptedEndpoints | Especifica se os pontos finais da fonte de dados são encriptados usando HTTPS. O valor predefinido é true.  | Não |
| utilizaçãoVerificação de anfitriões | Especifica se deve exigir o nome de anfitrião no certificado do servidor para corresponder ao nome de anfitrião do servidor ao ligar-se ao SSL. O valor predefinido é true.  | Não |
| utilizaçãoPeerVerification | Especifica se deve verificar a identidade do servidor ao ligar-se através do SSL. O valor predefinido é true.  | Não |

**Exemplo:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados ServiceNow.

Para copiar dados do ServiceNow, detete a propriedade do tipo do conjunto de dados para **ServiceNowObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **ServiceNowObject** | Sim |
| tableName | Nome da mesa. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte do ServiceNow.

### <a name="servicenow-as-source"></a>ServiçoAgora como fonte

Para copiar dados do ServiceNow, delineie o tipo de origem na atividade de cópia para **ServiceNowSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **ServiceNowSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Não (se for especificado "tableName" no conjunto de dados) |

Note o seguinte ao especificar o esquema e a coluna para o ServiceNow em consulta e **consulte [as dicas](#performance-tips) **de Desempenho sobre a implicação do desempenho da cópia .

- **Schema:** especifique `Actual` o `Display` esquema como ou na consulta ServiceNow, que pode `sysparm_display_value` encará-lo como o parâmetro de como verdadeiro ou falso quando chamar [AFIs repousantes](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)de ServiceNow . 
- **Coluna:** o nome da `Actual` coluna para `[column name]_value`o valor real `Display` em esquema `[column name]_display_value`é , enquanto para o valor de exibição em esquema é . Note que o nome da coluna precisa de mapa para o esquema que está a ser usado na consulta.

**Consulta da amostra:** 
 `SELECT col_value FROM Actual.alm_asset` OR 
`SELECT col_display_value FROM Display.alm_asset`

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Sugestões de desempenho

### <a name="schema-to-use"></a>Schema para usar

ServiceNow tem 2 esquemas diferentes, um é **"Real"** que devolve dados reais, o outro é **"Display"** que devolve os valores de visualização dos dados. 

Se tiver um filtro na sua consulta, utilize um esquema "Real" que tenha um melhor desempenho de cópia. Ao consultar contra o esquema "Real", o ServiceNow suporta de forma nativa o filtro ao recolher os dados para apenas devolver o conjunto de resultados filtrado, enquanto quando consulta ndo esquema "Display", a ADF recupera todos os dados e aplica o filtro internamente.

### <a name="index"></a>Índice

O índice de tabela ServiceNow pode ajudar a melhorar o desempenho da consulta, consulte criar [um índice](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html)de tabela .

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
