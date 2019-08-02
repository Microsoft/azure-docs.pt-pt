---
title: Copiar dados do serviço Web do Amazon Marketplace com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
description: Saiba como copiar dados do serviço Web do Amazon Marketplace para arquivos de dados de sink suportado utilizando uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: 3b4f1cfe4dbd15f25b4fab92a4bd3b7aee309cb2
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720843"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-preview"></a>Copiar dados do serviço Web do Amazon Marketplace com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do serviço Web do Amazon Marketplace. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em pré-visualização. Pode experimentá-lo e envie-nos comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de serviço da Web do Amazon Marketplace para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do serviço de Web do Amazon Marketplace.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do serviço Web do Amazon Marketplace ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AmazonMWS** | Sim |
| endpoint | O ponto final do servidor Amazon MWS, (ou seja, mws.amazonservices.com)  | Sim |
| marketplaceID | O Amazon Marketplace ID para obter dados a partir de. Para recuperar dados de vários IDs de Marketplace, separá-los com uma vírgula (`,`). (ou seja, A2EUQ1WTGCTBG2)  | Sim |
| sellerID | O ID do vendedor Amazon.  | Sim |
| mwsAuthToken | O token de autenticação do Amazon MWS. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| accessKeyId | O acesso à chave ID utilizado para aceder aos dados.  | Sim |
| secretKey | A chave secreta usada para acessar dados. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade de origem de dados são encriptados através de HTTPS. O valor predefinido é verdadeiro.  | Não |
| useHostVerification | Especifica se exige o nome de anfitrião no certificado do servidor de acordo com o nome de anfitrião do servidor ao ligar-se através de SSL. O valor predefinido é verdadeiro.  | Não |
| usePeerVerification | Especifica se pretende verificar a identidade do servidor ao ligar-se através de SSL. O valor predefinido é verdadeiro.  | Não |

**Exemplo:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do serviço Web do Amazon Marketplace.

Para copiar dados do serviço Web do Amazon Marketplace, defina a propriedade de tipo de conjunto de dados para **AmazonMWSObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AmazonMWSObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Amazon Marketplace Web Service.

### <a name="amazon-mws-as-source"></a>MWS Amazon como origem

Para copiar dados do serviço Web do Amazon Marketplace, defina o tipo de origem na atividade de cópia para **AmazonMWSSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AmazonMWSSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
