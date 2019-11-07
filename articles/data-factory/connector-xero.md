---
title: Copiar dados do Xero usando o Azure Data Factory
description: Saiba como copiar dados do Xero para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: d52e536170c649cbc84b6c6dce92afb76ffe3125
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680013"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Copiar dados do Xero usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Xero. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Xero tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Xero para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do Xero dá suporte a:

- [Aplicativo privado](https://developer.xero.com/documentation/getting-started/api-application-types) Xero, mas não aplicativo público.
- Todas as tabelas Xero (pontos de extremidade de API), exceto "Reports". 

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Xero.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Xero:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **Xero** | Sim |
| hospedeira | O ponto de extremidade do servidor Xero (`api.xero.com`).  | Sim |
| consumerKey | A chave do consumidor associada ao aplicativo Xero. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| privateKey | A chave privada do arquivo. pem que foi gerado para seu aplicativo particular Xero, consulte [criar um par de chaves pública/privada](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Observação para **gerar o PrivateKey. pem com numbits de 512** usando `openssl genrsa -out privatekey.pem 512`; 1024 não tem suporte. Inclua todo o texto do arquivo. PEM, incluindo as terminações de linha UNIX (\n), consulte o exemplo abaixo.<br/><br/>Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos de extremidade da fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não |
| useHostVerification | Especifica se o nome do host é necessário no certificado do servidor para corresponder ao nome do host do servidor ao se conectar por SSL. O valor padrão é true.  | Não |
| usePeerVerification | Especifica se a identidade do servidor deve ser verificada ao se conectar via SSL. O valor padrão é true.  | Não |

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

Inclua todo o texto do arquivo. PEM, incluindo as terminações de linha UNIX (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Xero.

Para copiar dados do Xero, defina a propriedade Type do conjunto de dado como **XeroObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **XeroObject** | Sim |
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do Xero.

### <a name="xero-as-source"></a>Xero como fonte

Para copiar dados do Xero, defina o tipo de origem na atividade de cópia como **XeroSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **XeroSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Contacts"`. | Não (se "TableName" no DataSet for especificado) |

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

Observe o seguinte ao especificar a consulta Xero:

- As tabelas com itens complexos serão divididas em várias tabelas. Por exemplo, transações bancárias têm uma estrutura de dados complexa "LineItems", portanto, os dados da transação bancária são mapeados para a tabela `Bank_Transaction` e `Bank_Transaction_Line_Items`, com `Bank_Transaction_ID` como chave estrangeira para vinculá-los.

- Os dados do Xero estão disponíveis por meio de dois esquemas: `Minimal` (padrão) e `Complete`. O esquema completo contém tabelas de chamada de pré-requisito que exigem dados adicionais (por exemplo, a coluna de ID) antes de fazer a consulta desejada.

As tabelas a seguir têm as mesmas informações no esquema mínimo e completo. Para reduzir o número de chamadas de API, use o esquema mínimo (padrão).

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
- Invoices_ pagamentos antecipados 
- Invoices_Overpayments 
- Manual_Journals 
- Pagamentos 
- Overpayments_Allocations 
- Pagamentos antecipados 
- Prepayments_Allocations 
- Recebimentos 
- Receipt_Validation_Errors 
- Tracking_Categories

As tabelas a seguir só podem ser consultadas com o esquema completo:

- Concluído. Bank_Transaction_Line_Items 
- Concluído. Bank_Transaction_Line_Item_Tracking 
- Concluído. Contact_Group_Contacts 
- Concluído. Contacts_Contact_ pessoas 
- Concluído. Credit_Note_Line_Items 
- Concluído. Credit_Notes_Line_Items_Tracking 
- Concluído. Expense_Claim_ pagamentos 
- Concluído. Expense_Claim_Receipts 
- Concluído. Invoice_Line_Items 
- Concluído. Invoices_Line_Items_Tracking
- Concluído. Manual_Journal_Lines 
- Concluído. Manual_Journal_Line_Tracking 
- Concluído. Overpayment_Line_Items 
- Concluído. Overpayment_Line_Items_Tracking 
- Concluído. Prepayment_Line_Items 
- Concluído. Prepayment_Line_Item_Tracking 
- Concluído. Receipt_Line_Items 
- Concluído. Receipt_Line_Item_Tracking 
- Concluído. Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte pela atividade de cópia, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
