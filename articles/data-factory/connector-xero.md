---
title: Copiar dados do Xero com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
description: Saiba como copiar dados do Xero para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
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
ms.openlocfilehash: 1ac8b4577b50ad9daa8d8da3cdb79120b961f55b
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089059"
---
# <a name="copy-data-from-xero-using-azure-data-factory-preview"></a>Copiar dados do Xero com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do Xero. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em pré-visualização. Pode experimentá-lo e fornecer comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Xero tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Pode copiar dados do Xero para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Xero suporta:

- Xero [particular do aplicativo](https://developer.xero.com/documentation/getting-started/api-application-types) mas aplicativos não pública.
- Todos os Xero tabelas (pontos finais da API), exceto "Relatórios". 

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Xero.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Xero:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Xero** | Sim |
| host | O ponto final do servidor Xero (`api.xero.com`).  | Sim |
| consumerKey | A chave de consumidor associada à aplicação Xero. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| privateKey | A chave privada a partir do ficheiro. pem que foi gerado para a sua aplicação privada Xero, consulte [criar um par de chaves públicas/privadas](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Tenha em atenção a **gerar privatekey.pem com numbits 512** usando `openssl genrsa -out privatekey.pem 512`; 1024 não é suportada. Incluem todo o texto do ficheiro. pem, incluindo o endings(\n) de linha de Unix, consulte o exemplo abaixo.<br/><br/>Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade de origem de dados são encriptados através de HTTPS. O valor predefinido é true.  | Não |
| useHostVerification | Especifica se o nome do anfitrião é necessário no certificado do servidor de acordo com o nome de anfitrião do servidor ao ligar-se através de SSL. O valor predefinido é true.  | Não |
| usePeerVerification | Especifica se pretende verificar a identidade do servidor ao ligar-se através de SSL. O valor predefinido é true.  | Não |

**Exemplo:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Valor de chave privada de exemplo:**

Incluem todo o texto do ficheiro. pem, incluindo o endings(\n) de linha do Unix.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Xero.

Para copiar dados do Xero, defina a propriedade de tipo de conjunto de dados para **XeroObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **XeroObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Xero.

### <a name="xero-as-source"></a>Xero como origem

Para copiar dados do Xero, defina o tipo de origem na atividade de cópia para **XeroSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **XeroSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Contacts"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Quando especificar a consulta do Xero, tenha em atenção o seguinte:

- As tabelas com itens complexos serão divididas a várias tabelas. Por exemplo, transações bancárias tem uma estrutura de dados complexos "Itensdelinha", para que os dados de transação bancária são mapeados para a tabela `Bank_Transaction` e `Bank_Transaction_Line_Items`, com `Bank_Transaction_ID` como chave estrangeira para ligá-las em conjunto.

- Dados do Xero estão disponíveis por meio de dois esquemas: `Minimal` (predefinição) e `Complete`. O esquema completo contém tabelas de chamada de pré-requisitos que dados adicionais (por exemplo, a coluna de ID) antes de efetuar a consulta pretendida.

As tabelas seguintes têm as mesmas informações no esquema mínimo e completo. Para reduzir o número de chamadas de API, utilize o esquema mínima (predefinição).

- Bank_Transactions
- Contact_Groups 
- Contactos 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Faturas 
- Invoices_Credit_Notes
- Pagamentos antecipados de Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Pagamentos a maior 
- Overpayments_Allocations 
- Pagamentos antecipados 
- Prepayments_Allocations 
- Recibos 
- Receipt_Validation_Errors 
- Tracking_Categories

As tabelas seguintes só podem ser consultadas com esquema concluída:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Pessoas Complete.Contacts_Contact_ 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ pagamentos 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de arquivos de dados suportados a atividade de cópia, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
