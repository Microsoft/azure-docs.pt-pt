---
title: Controlar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft
description: Saiba como consultar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375548"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Controlar o saldo do Crédito do Azure do Contrato de Cliente da Microsoft

Pode consultar o saldo do Crédito do Azure da sua conta de faturação do Contrato de Cliente da Microsoft no portal do Azure. 

Utilize os créditos para pagar os custos que são elegíveis para créditos. Ser-lhe-á cobrado quando utiliza produtos que não são elegíveis para créditos ou a sua utilização exceder o saldo do crédito. Para obter mais informações, veja [Produtos que não estão abrangidos pelos créditos do Azure](#products-that-arent-covered-by-azure-credits).

Na conta de faturação de um Contrato de Cliente da Microsoft, os créditos são atribuídos a um perfil de faturação. Cada perfil de faturação tem os seus próprios créditos. Tem de ter uma função de proprietário, contribuidor, leitor ou gestor de faturas no perfil de faturação ou uma função de proprietário, contribuidor ou leitor na conta de faturação para ver o saldo do Crédito do Azure de um perfil de faturação. Para saber mais sobre as funções, veja [Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure](billing-understand-mca-roles.md).

Este artigo aplica-se a uma conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Consultar o saldo do crédito no portal do Azure

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **Créditos do Azure** no lado esquerdo. Dependendo do seu acesso, pode ser preciso selecionar uma conta de faturação ou um perfil de faturação e, em seguida, selecionar **Créditos do Azure**.

4. Os Créditos do Azure apresentam as seguintes informações:

   ![Captura de ecrã do saldo do crédito e das transações de um perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O montante estimado de créditos que possui depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Montante de créditos desde a última fatura. Não inclui as transações pendentes |
   | Transações       | Transações de faturação que afetaram o saldo do Crédito do Azure |

   Quando o saldo estimado cair para 0, ser-lhe-á cobrada toda a utilização, incluindo os produtos que são elegíveis para crédito.

6. Selecione **Lista de créditos** para ver a lista de créditos do perfil de faturação. A lista de créditos disponibiliza as seguintes informações:

   ![Captura de ecrã das listas de créditos de um Perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Origem | A origem da aquisição do crédito |
   | Data de início | A data em que adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo atual | O saldo desde a última fatura |
   | Montante original | O montante original de crédito |
   | Estado | O estado atual do crédito. O estado pode ser ativo, utilizado, expirado ou a expirar |

## <a name="check-your-credit-balance-programmatically"></a>Consultar o saldo do crédito através de programação

Pode utilizar as APIs de [Faturação do Azure](https://docs.microsoft.com/rest/api/billing/) e de [Consumo](https://docs.microsoft.com/rest/api/consumption/) para, através de programação, obter o saldo do crédito da sua conta de faturação.

Os exemplos mostrados abaixo utilizam as APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-billing-profiles-you-have-access-to"></a>Localizar os perfis de faturação a que tem acesso

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
A resposta das APIs devolve uma lista de contas de faturação e respetivos perfis de faturação.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Utilize a propriedade `displayName` do perfil de faturação para identificar o perfil de faturação para o qual pretende consultar o saldo do crédito. Copie o `id` do perfil de faturação. Por exemplo, se quiser consultar o saldo do crédito do perfil de faturação **Desenvolvimento**, terá de copiar ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="get-azure-credit-balance"></a>Obter o saldo do Crédito do Azure 

Faça o pedido a seguir ao substituir `<billingProfileId>` por `id`, copiado do primeiro passo (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

A resposta das APIs devolve o saldo estimado e o atual para o perfil de faturação.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nome do elemento  | Descrição                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | O montante estimado de créditos que possui depois de considerar todas as transações faturadas e pendentes. |
| `currentBalance`   | O montante de créditos desde a última fatura. Não inclui as transações pendentes.    |
| `pendingCreditAdjustments`      | Os ajustes como os reembolsos que ainda não foram faturados.  |
| `expiredCredit`      |  O crédito que expirou desde a última fatura.  |
| `pendingEligibleCharges`  | Os custos elegíveis para crédito que ainda não foram faturados.   |

### <a name="get-list-of-credits"></a>Obter a lista de créditos

Faça o pedido a seguir ao substituir `<billingProfileId>` por `id`, copiado do primeiro passo (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
A resposta das APIs devolve as listas de créditos do Azure de um perfil de faturação.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | Descrição                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | O montante original do crédito. |
| `closedBalance`   | O saldo desde a última fatura.    |
| `source`      | A origem que define quem adquiriu o crédito. |
| `startDate`      |  A data em que o crédito passou a estar ativo.  |
| `expirationDate`  | A data em que o crédito expira.   |
| `poNumber`  | O número da nota de encomenda da fatura em que o crédito foi faturado.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Obter transações que afetaram o saldo do crédito

Faça o pedido a seguir ao substituir `<billingProfileId>` por `id`, copiado do primeiro passo (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Terá de transmitir uma **startDate** e uma **endDate**  para obter as transações para a duração desejada.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
A resposta das APIs devolve todas as transações que afetaram o saldo de crédito do perfil de faturação.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | Descrição                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | A data em que a transação ocorreu. |
| `description` | A descrição da transação. |
| `adjustments`   | Os ajustes de crédito da transação.    |
| `creditExpired`      | O montante de crédito que expirou. |
| `charges`      |  Os custos da transação.  |
| `closedBalance`  | O saldo após a transação.   |
| `eventType`  | O tipo de transação.   |
| `invoiceNumber`  | O número da fatura em que a transação é faturada. Estará vazio para a transação pendente.   |

## <a name="how-credits-are-used"></a>Como os créditos são utilizados

Numa conta de faturação de um contrato de cliente da Microsoft, deverá utilizar os perfis de faturação para gerir as faturas e os métodos de pagamento. Mensalmente, é gerada uma fatura para cada perfil de faturação e deverá utilizar os métodos de pagamento para a pagar.

Atribui os créditos que adquiriu a um perfil de faturação. Quando uma fatura é gerada para o perfil de faturação, os créditos são automaticamente aplicados ao total dos custos para calcular o montante que precisa de pagar. Paga o montante restante através dos seus métodos de pagamento, como cheque, transferência bancária ou cartão de crédito.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não estão abrangidos pelos Créditos do Azure

 Os produtos seguintes não são abrangidos pelos Créditos do Azure. Ser-lhe-á cobrada a utilização destes produtos, independentemente do saldo de crédito:

- Canónico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Utilizador Registado
- OpenLogic
- Utilizador Registado do Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (Mensal)
- Visual Studio Enterprise (Anual)
- Visual Studio Professional (Mensal)
- Visual Studio Professional (Anual)
- Produtos do Azure Marketplace
- Planos de suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Compreender a conta de faturação do Contrato de Cliente da Microsoft](billing-mca-overview.md)
- [Compreender os termos na fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
