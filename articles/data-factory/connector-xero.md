---
title: Copiar dados da Xero utilizando a Azure Data Factory
description: Saiba como copiar dados da Xero para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jingwang
ms.openlocfilehash: 3f8c74f36c1c441e00b808954ce7f7710d3fbd52
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879970"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Copiar dados da Xero utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Xero. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Xero é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Xero para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Xero suporta:

- Autenticação OAuth 2.0 e OAuth 1.0. Para o OAuth 1.0, o conector suporta [a aplicação privada](https://developer.xero.com/documentation/getting-started/getting-started-guide) Xero, mas não a aplicação pública.
- Todas as tabelas Xero (pontos finais da API) exceto "Relatórios".

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Xero.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Xero:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Xero** | Yes |
| conexõesProperties | Um grupo de propriedades que define como se conectar com Xero. | Yes |
| **_Em: `connectionProperties` __* | | |
| anfitrião | O ponto final do servidor Xero `api.xero.com` ().  | Yes |
| authenticationType | Os valores permitidos são `OAuth_2.0` `OAuth_1.0` e. | Yes |
| consumerKey | Para o OAuth 2.0, especifique o _ *ID do cliente** para a sua aplicação Xero.<br>Para o OAuth 1.0, especifique a chave de consumo associada à aplicação Xero.<br>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| privateKey | Para o OAuth 2.0, especifique o segredo do **cliente** para a sua aplicação Xero.<br>Para OAuth 1.0, especifique a chave privada do ficheiro .pem que foi gerado para a sua aplicação privada Xero, consulte [Criar um par de chaves público/privado](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Nota para **gerar o privatekey.pem com numbits de 512 usando** `openssl genrsa -out privatekey.pem 512` , 1024 não é suportado. Inclua todo o texto do ficheiro .pem, incluindo as terminações da linha Unix(\n), ver amostra abaixo.<br/><br>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| inquilinoId | A identificação do inquilino associada à sua aplicação Xero. Aplicável à autenticação OAuth 2.0.<br>Saiba como obter a identificação do inquilino da [Verificação dos inquilinos que você está autorizado a aceder à secção.](https://developer.xero.com/documentation/oauth2/auth-flow) | Sim para autenticação OAuth 2.0 |
| refreshToken | Aplicável à autenticação OAuth 2.0.<br/>O token de atualização OAuth 2.0 está associado à aplicação Xero e usado para refrescar o token de acesso; o sinal de acesso expira após 30 minutos. Saiba como funciona o fluxo de autorização Xero e como obter o token refresh [deste artigo.](https://developer.xero.com/documentation/oauth2/auth-flow) Para obter um token de atualização, você deve solicitar o [âmbito offline_access](https://developer.xero.com/documentation/oauth2/scopes). <br/>**Saiba limitação**: Note Xero reinicia o token de atualização depois de ser usado para aceder a atualização simbólica. Para uma carga de trabalho operacionalizada, antes de cada atividade de cópia funcionar, é necessário definir um token de atualização válido para a ADF utilizar.<br/>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim para autenticação OAuth 2.0 |
| useEncryptedEndpoints | Especifica se os pontos finais de origem de dados são encriptados usando HTTPS. O valor predefinido é true.  | No |
| useHostVerification | Especifica se o nome do anfitrião é necessário no certificado do servidor para corresponder ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é true.  | No |
| usePeerVerificação | Especifica se deve verificar a identidade do servidor ao ligar o TLS. O valor predefinido é true.  | No |

**Exemplo: Autenticação OAuth 2.0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**Exemplo: Autenticação OAuth 1.0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Valor chave privado da amostra:**

Inclua todo o texto do ficheiro .pem, incluindo as terminações da linha Unix(\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Xero.

Para copiar dados da Xero, defina a propriedade tipo do conjunto de dados para **XeroObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **XeroObject** | Yes |
| tableName | O nome da mesa. | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte Xero.

### <a name="xero-as-source"></a>Xero como fonte

Para copiar dados da Xero, desagrafe o tipo de origem na atividade da cópia para **xeroSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **XeroSource** | Yes |
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

- As mesas com itens complexos serão divididas em várias mesas. Por exemplo, as transações bancárias têm uma estrutura de dados complexa "LineItems", pelo que os dados de transação bancária são mapeados para tabela `Bank_Transaction` `Bank_Transaction_Line_Items` e, `Bank_Transaction_ID` com como chave estrangeira para os ligar.

- Os dados Xero estão disponíveis através de dois esquemas: `Minimal` (padrão) e `Complete` . O esquema completo contém tabelas de chamadas pré-requisitos que requerem dados adicionais (por exemplo, coluna de ID) antes de fazer a consulta desejada.

As tabelas a seguir têm a mesma informação no esquema Minimal and Complete. Para reduzir o número de chamadas API, utilize esquema mínimo (predefinição).

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
- Pré-pagamentos Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Pagamentos em excesso 
- Overpayments_Allocations 
- Pré-pagamentos 
- Prepayments_Allocations 
- Recibos 
- Receipt_Validation_Errors 
- Tracking_Categories

As seguintes tabelas só podem ser consultadas com esquema completo:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- pessoas Complete.Contacts_Contact_ 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Pagamentos Complete.Expense_Claim_ 
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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximos passos
Para obter uma lista de lojas de dados suportadas pela atividade de cópia, consulte [as lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
