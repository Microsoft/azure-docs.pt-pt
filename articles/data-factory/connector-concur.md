---
title: Copiar dados da Concur utilizando a Azure Data Factory (Pré-visualização)
description: Saiba como copiar dados da Concur para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jingwang
ms.openlocfilehash: 974682aa9e9bebd6e2d17237decd8389174ff28d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383675"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Copiar dados da Concur utilizando a Azure Data Factory (Pré-visualização)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Concur. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

> [!IMPORTANT]
> Este conector encontra-se atualmente em pré-visualização. Pode experimentar e dar-nos feedback. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Concur é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Concur para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!NOTE]
> A conta do parceiro não é suportada atualmente.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Concur.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Concur:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Concur** | Yes |
| conexõesProperties | Um grupo de propriedades que define como se conectar a Concur. | Yes |
| ***Em `connectionProperties` :*** | | |
| authenticationType | Os valores permitidos são `OAuth_2.0_Bearer` e `OAuth_2.0` (legado). A opção de autenticação OAuth 2.0 funciona com a antiga API Concur, que estava depreciada desde fevereiro de 2017. | Yes |
| anfitrião | O ponto final do servidor Concur, por `implementation.concursolutions.com` exemplo.  | Yes |
| baseUrl | O URL base da URL de autorização do seu Concur. | Sim para `OAuth_2.0_Bearer` autenticação |
| clientId | ID do cliente de aplicação fornecido pela Concur App Management.  | Yes |
| segredo de clientes | O segredo do cliente correspondente à identificação do cliente. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim para `OAuth_2.0_Bearer` autenticação |
| nome de utilizador | O nome de utilizador que utiliza para aceder ao serviço Concur. | Yes |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador que forneceu no campo do nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Especifica se os pontos finais de origem de dados são encriptados usando HTTPS. O valor predefinido é true.  | No |
| useHostVerification | Especifica se deve exigir que o nome do anfitrião no certificado do servidor corresponda ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é true.  | No |
| usePeerVerificação | Especifica se deve verificar a identidade do servidor ao ligar o TLS. O valor predefinido é true.  | No |

**Exemplo:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**Exemplo (legado):**

Note que o seguinte é um modelo de serviço vinculado legado sem `connectionProperties` e usando `OAuth_2.0` a autenticação.

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas por conjunto de dados Concur.

Para copiar dados da Concur, defina a propriedade tipo do conjunto de dados para **ConcurObject**. Não existe nenhuma propriedade específica de tipo adicional neste tipo de conjunto de dados. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **ConcurObject** | Yes |
| tableName | O nome da mesa. | Não (se for especificada "consulta" na fonte de atividade) |


**Exemplo**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte Concur.

### <a name="concursource-as-source"></a>ConcurSource como fonte

Para copiar dados da Concur, desaprote o tipo de origem na atividade de cópia **para o ConcurSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **ConcurSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM Opportunities where Id = xxx "`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
