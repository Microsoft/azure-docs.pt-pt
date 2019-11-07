---
title: Copiar dados da nuvem de serviço Oracle usando o Azure Data Factory (versão prévia)
description: Saiba como copiar dados do Oracle Service Cloud para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.openlocfilehash: 2b2d41d7ef2c42fea90555d2b24f6d055712bfc5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680528"
---
# <a name="copy-data-from-oracle-service-cloud-using-azure-data-factory-preview"></a>Copiar dados da nuvem de serviço Oracle usando o Azure Data Factory (versão prévia)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Oracle Service Cloud. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em visualização. Você pode experimentá-lo e fornecer comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de nuvem de serviço Oracle tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Oracle Service Cloud para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector do Oracle Service Cloud.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de nuvem do serviço Oracle:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **OracleServiceCloud** | Sim |
| hospedeira | A URL da instância de nuvem do serviço Oracle.  | Sim |
| o nome de utilizador | O nome de usuário que você usa para acessar o Oracle Service Cloud Server.  | Sim |
| palavra-passe | A senha correspondente ao nome de usuário que você forneceu na chave username. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade da fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não |
| useHostVerification | Especifica se deve ser necessário o nome do host no certificado do servidor para corresponder ao nome do host do servidor ao se conectar via SSL. O valor padrão é true.  | Não |
| usePeerVerification | Especifica se a identidade do servidor deve ser verificada ao se conectar via SSL. O valor padrão é true.  | Não |

**Exemplo:**

```json
{
    "name": "OracleServiceCloudLinkedService",
    "properties": {
        "type": "OracleServiceCloud",
        "typeProperties": {
            "host" : "<host>",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de serviços de nuvem do serviço Oracle.

Para copiar dados da nuvem de serviços da Oracle, defina a propriedade Type do DataSet como **OracleServiceCloudObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **OracleServiceCloudObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "OracleServiceCloudDataset",
    "properties": {
        "type": "OracleServiceCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OracleServiceCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de nuvem do serviço Oracle.

### <a name="oracle-service-cloud-as-source"></a>Nuvem de serviço Oracle como fonte

Para copiar dados do Oracle Service Cloud, defina o tipo de fonte na atividade de cópia como **OracleServiceCloudSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **OracleServiceCloudSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromOracleServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OracleServiceCloud input dataset name>",
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
                "type": "OracleServiceCloudSource",
                "query": "SELECT * FROM MyTable"
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
