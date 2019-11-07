---
title: Copiar dados do quadrado usando Azure Data Factory (versão prévia)
description: Saiba como copiar dados do quadrado para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.openlocfilehash: b7f83e8e7690180dbed04572f2e2699f02d5fed7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680135"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Copiar dados do quadrado usando Azure Data Factory (versão prévia)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do quadrado. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em visualização. Você pode experimentá-lo e nos enviar comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector quadrado tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)


Você pode copiar dados do quadrado para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector quadrado.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado quadrado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **Square** | Sim |
| hospedeira | A URL da instância quadrada. (ou seja, mystore.mysquare.com)  | Sim |
| clientId | A ID do cliente associada ao seu aplicativo quadrado.  | Sim |
| clientSecret | O segredo do cliente associado ao seu aplicativo quadrado. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| RedirectUri | A URL de redirecionamento atribuída no painel do aplicativo quadrado. (ou seja, http:\//localhost: 2500)  | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade da fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não |
| useHostVerification | Especifica se deve ser necessário o nome do host no certificado do servidor para corresponder ao nome do host do servidor ao se conectar via SSL. O valor padrão é true.  | Não |
| usePeerVerification | Especifica se a identidade do servidor deve ser verificada ao se conectar via SSL. O valor padrão é true.  | Não |

**Exemplo:**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "host" : "mystore.mysquare.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "redirectUri" : "http://localhost:2500"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de conjuntos.

Para copiar dados de um quadrado, defina a propriedade Type do conjunto como **squareobject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **squareobject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem quadrada.

### <a name="square-as-source"></a>Quadrado como fonte

Para copiar dados do quadrado, defina o tipo de origem na atividade de cópia como **Square**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Square** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Business"`. | Não (se "TableName" no DataSet for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
