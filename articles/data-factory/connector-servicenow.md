---
title: Copiar dados do ServiceNow usando o Azure Data Factory
description: Saiba como copiar dados do ServiceNow para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 933b12f852fcbcc20e50f3c89d597bbe6b84bd8e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680222"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados do ServiceNow usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do ServiceNow. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do ServiceNow tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do ServiceNow para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do ServiceNow.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do ServiceNow:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **ServiceNow** | Sim |
| endpoint | O ponto de extremidade do servidor ServiceNow (`http://<instance>.service-now.com`).  | Sim |
| authenticationType | O tipo de autenticação a ser usado. <br/>Os valores permitidos são: **Basic**, **OAuth2** | Sim |
| o nome de utilizador | O nome de usuário usado para se conectar ao servidor ServiceNow para autenticação básica e OAuth2.  | Sim |
| palavra-passe | A senha correspondente ao nome de usuário para autenticação básica e OAuth2. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| clientId | A ID do cliente para autenticação OAuth2.  | Não |
| clientSecret | O segredo do cliente para autenticação OAuth2. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| useEncryptedEndpoints | Especifica se os pontos de extremidade da fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não |
| useHostVerification | Especifica se deve ser necessário o nome do host no certificado do servidor para corresponder ao nome do host do servidor ao se conectar via SSL. O valor padrão é true.  | Não |
| usePeerVerification | Especifica se a identidade do servidor deve ser verificada ao se conectar via SSL. O valor padrão é true.  | Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de conjuntos do ServiceNow.

Para copiar dados do ServiceNow, defina a propriedade Type do conjunto de dado como **ServiceNowObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **ServiceNowObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "query" na origem de atividade) |

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como fonte

Para copiar dados do ServiceNow, defina o tipo de fonte na atividade de cópia como **ServiceNowSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **ServiceNowSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Não (se "TableName" no DataSet for especificado) |

Observe o seguinte ao especificar o esquema e a coluna para o ServiceNow no Query e **consulte as [dicas de desempenho](#performance-tips) na implicação de desempenho de cópia**.

- **Esquema:** especifique o esquema como `Actual` ou `Display` na consulta do servicenow, que você pode examinar como o parâmetro de `sysparm_display_value` como verdadeiro ou falso ao chamar as [APIs RESTful do ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Coluna:** o nome da coluna para o valor real em `Actual` esquema é `[column name]_value`, enquanto para o valor de exibição em `Display` esquema é `[column name]_display_value`. Observe que o nome da coluna precisa de MAP para o esquema que está sendo usado na consulta.

**Consulta de exemplo:** 
`SELECT col_value FROM Actual.alm_asset` ou 
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

### <a name="schema-to-use"></a>Esquema a ser usado

O ServiceNow tem dois esquemas diferentes, um é **"real"** , que retorna dados reais, o outro é **"display"** , que retorna os valores de exibição dos dados. 

Se você tiver um filtro em sua consulta, use o esquema "real" que tem melhor desempenho de cópia. Ao consultar o esquema "real", o ServiceNow oferece suporte nativo ao filtro ao buscar os dados para retornar apenas o ResultSet filtrado, enquanto ao consultar o esquema de "exibição", o ADF recupera todos os dados e aplica o filtro internamente.

### <a name="index"></a>Índice

O índice de tabela do ServiceNow pode ajudar a melhorar o desempenho da consulta, consulte [criar um índice de tabela](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
