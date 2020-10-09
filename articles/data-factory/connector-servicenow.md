---
title: Copiar dados do ServiceNow
description: Saiba como copiar dados do ServiceNow para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
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
ms.openlocfilehash: bc48f651a1adb099017e8f47d9fa6bcfa8078fa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415341"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados do ServiceNow utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do ServiceNow. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector ServiceNow é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do ServiceNow para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não é necessário instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector ServiceNow.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao ServiceNow:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **ServiceNow** | Sim |
| endpoint | O ponto final do servidor ServiceNow `http://<instance>.service-now.com` ().  | Sim |
| authenticationType | O tipo de autenticação a utilizar. <br/>Os valores permitidos são: **Básico,** **OAuth2** | Sim |
| nome de utilizador | O nome de utilizador utilizado para ligar ao servidor ServiceNow para autenticação Básica e OAuth2.  | Sim |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador para autenticação Básica e OAuth2. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| clientId | A identificação do cliente para a autenticação OAuth2.  | Não |
| segredo de clientes | O segredo do cliente para a autenticação da OAuth2. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Não |
| useEncryptedEndpoints | Especifica se os pontos finais de origem de dados são encriptados usando HTTPS. O valor predefinido é true.  | Não |
| useHostVerification | Especifica se deve exigir que o nome do anfitrião no certificado do servidor corresponda ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é true.  | Não |
| usePeerVerificação | Especifica se deve verificar a identidade do servidor ao ligar o TLS. O valor predefinido é true.  | Não |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados Do ServiceNow.

Para copiar dados do ServiceNow, defina a propriedade tipo do conjunto de dados para **o ServiceNowObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **ServiceNowObject** | Sim |
| tableName | O nome da mesa. | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como fonte

Para copiar dados do ServiceNow, deteta o tipo de origem na atividade de cópia ao **ServiceNowSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **ServiceNowSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Não (se for especificado "tableName" no conjunto de dados) |

Note o seguinte ao especificar o esquema e a coluna para ServiceNow em consulta, e **consulte as dicas de [desempenho](#performance-tips) sobre a implicação do desempenho da cópia**.

- **Schema:** especifique o esquema como `Actual` ou na consulta `Display` ServiceNow, que pode encurá-lo como o parâmetro de `sysparm_display_value` como verdadeiro ou falso ao chamar [APIs repousante](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)ao ServiceNow . 
- **Coluna:** o nome da coluna para o valor real sob `Actual` esquema é , `[column name]_value` enquanto para o valor do visor sob `Display` esquema é `[column name]_display_value` . Note que o nome da coluna precisa de mapa para o esquema que está a ser usado na consulta.

**Consulta de amostras:** 
 `SELECT col_value FROM Actual.alm_asset` OU 
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

### <a name="schema-to-use"></a>Esquema para usar

O ServiceNow tem 2 esquemas diferentes, um é **"Real"** que devolve dados reais, o outro é **"Display"** que devolve os valores de exibição dos dados. 

Se tiver um filtro na sua consulta, utilize o esquema "Real" que tenha um melhor desempenho de cópia. Ao consultar o esquema "Real", o ServiceNow suporta de forma nativa o filtro ao recolher os dados para apenas devolver o resultado filtrado, enquanto ao consultar o esquema "Display", a ADF recupera todos os dados e aplica o filtro internamente.

### <a name="index"></a>Índice

O índice de tabela ServiceNow pode ajudar a melhorar o desempenho da consulta, consulte para [criar um índice de tabela](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
