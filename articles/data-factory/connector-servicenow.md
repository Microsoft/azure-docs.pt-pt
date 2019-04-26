---
title: Copiar dados do ServiceNow com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do ServiceNow para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 234b78a97c2663121d0d585154695887a58b9522
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203419"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados do ServiceNow com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do ServiceNow. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de ServiceNow para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do ServiceNow.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do ServiceNow:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **ServiceNow** | Sim |
| endpoint | O ponto final do servidor do ServiceNow (`http://<instance>.service-now.com`).  | Sim |
| authenticationType | O tipo de autenticação a utilizar. <br/>Valores permitidos são: **Basic**, **OAuth2** | Sim |
| o nome de utilizador | O nome de utilizador utilizado para ligar ao servidor do ServiceNow para a autenticação básica e OAuth2.  | Sim |
| palavra-passe | A palavra-passe correspondente ao nome de usuário para autenticação básica e OAuth2. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| clientId | O ID de cliente para a autenticação OAuth2.  | Não |
| clientSecret | O segredo do cliente para a autenticação OAuth2. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| useEncryptedEndpoints | Especifica se os pontos de extremidade de origem de dados são encriptados através de HTTPS. O valor predefinido é verdadeiro.  | Não |
| useHostVerification | Especifica se exige o nome de anfitrião no certificado do servidor de acordo com o nome de anfitrião do servidor ao ligar-se através de SSL. O valor predefinido é verdadeiro.  | Não |
| usePeerVerification | Especifica se pretende verificar a identidade do servidor ao ligar-se através de SSL. O valor predefinido é verdadeiro.  | Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do ServiceNow.

Para copiar dados do ServiceNow, defina a propriedade de tipo de conjunto de dados para **ServiceNowObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **ServiceNowObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como origem

Para copiar dados do ServiceNow, defina o tipo de origem na atividade de cópia para **ServiceNowSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **ServiceNowSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Não (se for especificado "tableName" no conjunto de dados) |

Tenha em atenção o seguinte ao especificar o esquema e na coluna do ServiceNow na consulta, e **consultar [sugestões de desempenho](#performance-tips) no implicação de desempenho de cópia**.

- **Esquema:** especifique o esquema `Actual` ou `Display` da consulta de ServiceNow, o que pode analisá-los como parâmetro da `sysparm_display_value` como VERDADEIRO ou falso quando chamar [APIs restful do ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Coluna:** o nome da coluna para o valor real sob `Actual` esquema é `[column name]_value`, enquanto para o valor de apresentação sob `Display` esquema é `[column name]_display_value`. Tenha em atenção o nome da coluna tem de mapear para o esquema a ser utilizado na consulta.

**Consulta de exemplo:**
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

### <a name="schema-to-use"></a>Esquema a utilizar

ServiceNow tem 2 esquemas diferentes, um é **"Real"** que retorna dados reais, o outro é **"Exibir"** que retorna os valores de apresentação de dados. 

Se tiver um filtro na sua consulta, utilize o esquema de "Real" que tem copiar melhor o desempenho. Ao consultar em relação ao esquema de "Real", ServiceNow suportam nativamente o filtro ao obter os dados para devolver apenas o conjunto filtrado de resultados, ao passo que ao consultar o esquema de "Apresentação", o ADF recuperar todos os dados e aplicar filtro internamente.

### <a name="index"></a>Índice

Índice de tabela do ServiceNow pode ajudar a melhorar o desempenho de consulta, consulte [criar um índice de tabela](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
