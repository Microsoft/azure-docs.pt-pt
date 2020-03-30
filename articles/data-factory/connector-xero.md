---
title: Copiar dados da Xero utilizando a Azure Data Factory
description: Saiba como copiar dados da Xero para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 1f6404da163e075b63a99a1d8474cdba4e064b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930889"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Copiar dados da Xero utilizando a Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Xero. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Xero é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Xero para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Xero suporta:

- [Aplicação privada](https://developer.xero.com/documentation/getting-started/api-application-types) Xero, mas não aplicação pública.
- Todas as tabelas Xero (pontos finais da API) exceto "Relatórios". 

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Xero.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Xero:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Xero** | Sim |
| anfitrião | O ponto final do servidor`api.xero.com`Xero ( ).  | Sim |
| consumidorChave | A chave do consumidor associada à aplicação Xero. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| privateKey | A chave privada do ficheiro .pem que foi gerado para a sua aplicação privada Xero, consulte Criar um par de [chaves público/privado](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Nota para **gerar o privatekey.pem com numbits de 512** usando; `openssl genrsa -out privatekey.pem 512` 1024 não é apoiado. Inclua todo o texto do ficheiro .pem, incluindo os terminadores da linha Unix (\n), consulte a amostra abaixo.<br/><br/>Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos finais da fonte de dados são encriptados usando HTTPS. O valor predefinido é true.  | Não |
| utilizaçãoVerificação de anfitriões | Especifica se o nome do anfitrião é exigido no certificado do servidor para corresponder ao nome de anfitrião do servidor ao ligar-se ao SSL. O valor predefinido é true.  | Não |
| utilizaçãoPeerVerification | Especifica se deve verificar a identidade do servidor ao ligar-se através do SSL. O valor predefinido é true.  | Não |

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

**Prove o valor-chave privado:**

Incluir todo o texto do ficheiro .pem, incluindo os terminadores da linha Unix (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Xero.

Para copiar dados da Xero, detete a propriedade do tipo do conjunto de dados para **XeroObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **XeroObject** | Sim |
| tableName | Nome da mesa. | Não (se for especificada a "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte xero.

### <a name="xero-as-source"></a>Xero como fonte

Para copiar dados da Xero, delineie o tipo de origem na atividade da cópia para **XeroSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **XeroSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM Contacts"`. | Não (se for especificado "tableName" no conjunto de dados) |

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

Note o seguinte ao especificar a consulta Xero:

- Mesas com itens complexos serão divididas em várias mesas. Por exemplo, as transações bancárias têm uma estrutura de dados complexa "LineItems", pelo que os dados da transação bancária são mapeados à mesa `Bank_Transaction` e, `Bank_Transaction_Line_Items`com `Bank_Transaction_ID` como chave estrangeira para os ligar.

- Os dados da Xero estão `Minimal` disponíveis através `Complete`de dois esquemas: (padrão) e . O esquema completo contém tabelas de chamadas pré-requisitos que requerem dados adicionais (por exemplo, coluna de ID) antes de fazer a consulta desejada.

As tabelas seguintes têm as mesmas informações no esquema Mínimo e Completo. Para reduzir o número de chamadas API, utilize o esquema mínimo (predefinição).

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
- pagamentos antecipados Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Pagamentos em excesso 
- Overpayments_Allocations 
- Pré-pagamentos 
- Prepayments_Allocations 
- Recibos 
- Receipt_Validation_Errors 
- Tracking_Categories

As seguintes tabelas só podem ser consultadas com um esquema completo:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Pessoas completas.Contacts_Contact_ 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Pagamentos Completos.Expense_Claim_ 
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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas pela atividade de cópia, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
