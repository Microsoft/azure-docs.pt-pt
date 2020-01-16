---
title: Controlar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft
description: Saiba como consultar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c8ea083e216331904c9d5741b97f69b7f5a8249a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991337"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Controlar o saldo do Crédito do Azure do Contrato de Cliente da Microsoft

Você pode verificar o saldo de crédito do Azure para sua conta de cobrança de um contrato de cliente da Microsoft no portal do Azure ou por meio de APIs REST.

Na conta de faturação de um Contrato de Cliente da Microsoft, os créditos são atribuídos a um perfil de faturação. Cada perfil de cobrança tem seus próprios créditos que são aplicados automaticamente aos encargos em sua fatura. Tem de ter uma função de proprietário, contribuidor, leitor ou gestor de faturas no perfil de faturação ou uma função de proprietário, contribuidor ou leitor na conta de faturação para ver o saldo do Crédito do Azure de um perfil de faturação. Para saber mais sobre as funções, veja [Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure](understand-mca-roles.md).

Este artigo aplica-se à conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso ao Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Consultar o saldo do crédito

### <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Na página escopos de cobrança, selecione a conta de cobrança para a qual você deseja acompanhar o saldo de crédito. A conta de cobrança deve ser do tipo **Microsoft Customer Agreement**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Portal do Azure lembra o último escopo de cobrança que você acessa e exibe o escopo na próxima vez que você chegar ao gerenciamento de custos + página de cobrança. Você não verá a página escopos de cobrança se tiver visitado gerenciamento de custos + cobrança anteriormente. Nesse caso, verifique se você está no [escopo certo](#check-access-to-a-microsoft-customer-agreement). Caso contrário, [mude o escopo](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) para selecionar a conta de cobrança para um contrato de cliente da Microsoft.

3. Selecione os **métodos de pagamento** do lado esquerdo e, em seguida, selecione **créditos do Azure**.

   ![Captura de tela do saldo de crédito de um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. A página créditos do Azure tem as seguintes seções:

   #### <a name="balance"></a>Saldo

   A seção de saldo exibe o resumo do saldo de crédito do Azure.

   ![Captura de tela do saldo de crédito de um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Duração               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O montante estimado de créditos que possui depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Montante de créditos desde a última fatura. Não inclui as transações pendentes |

   Quando o saldo estimado cair para 0, ser-lhe-á cobrada toda a utilização, incluindo os produtos que são elegíveis para crédito.

   #### <a name="credits-list"></a>Lista de créditos

   A seção lista de créditos exibe a lista de créditos do Azure.

   ![Captura de ecrã das listas de créditos de um Perfil de faturação](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Duração | Definição |
   |---|---|
   | Origem | A origem da aquisição do crédito |
   | Data de início | A data em que adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo atual | O saldo desde a última fatura |
   | Montante original | O montante original de crédito |
   | Estado | O estado atual do crédito. O estado pode ser ativo, utilizado, expirado ou a expirar |

   #### <a name="transactions"></a>Transações

   A seção transações exibe todas as transações que afetaram o saldo dos créditos.

   ![Captura de tela de transações de crédito para um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Duração | Definição |
   |---|---|
   | Data da transação | A data em que a transação ocorreu |
   | Descrição | Uma descrição da transação |
   | Montante| O montante da transação |
   | Saldo | O saldo após a transação |

    > [!NOTE]
    >
    > Se você não vir créditos do Azure na página métodos de pagamento, você não tem créditos ou não selecionou o escopo certo. Selecione a conta de cobrança que tem créditos ou um de seus perfis de cobrança. Para saber como alterar os escopos, consulte [alternar escopos de cobrança no portal do Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Se você estiver exibindo créditos do Azure no escopo da conta de cobrança e a conta de cobrança tiver mais de um perfil de cobrança, a página de créditos do Azure mostrará uma tabela com um resumo dos créditos do Azure para todos os perfis de cobrança. Selecione um perfil de cobrança na lista, selecione os métodos de pagamento e os créditos do Azure para exibir detalhes de um perfil de cobrança.

    ![Captura de tela da lista de crédito de uma conta de cobrança](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-apitabrest"></a>[API REST](#tab/rest)

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

Use a propriedade `displayName` do perfil de cobrança para identificar o perfil de cobrança para o qual você deseja verificar o saldo de crédito. Copie o `id` do perfil de faturação. Por exemplo, se quiser consultar o saldo do crédito do perfil de faturação **Desenvolvimento**, terá de copiar ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="get-azure-credit-balance"></a>Obter o saldo do Crédito do Azure

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

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

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

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

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Terá de transmitir uma **startDate** e uma **endDate**  para obter as transações para a duração desejada.

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

---

## <a name="how-credits-are-used"></a>Como os créditos são utilizados

Numa conta de faturação de um contrato de cliente da Microsoft, deverá utilizar os perfis de faturação para gerir as faturas e os métodos de pagamento. Mensalmente, é gerada uma fatura para cada perfil de faturação e deverá utilizar os métodos de pagamento para a pagar.

Atribui os créditos que adquiriu a um perfil de faturação. Quando uma fatura é gerada para o perfil de faturação, os créditos são automaticamente aplicados ao total dos custos para calcular o montante que precisa de pagar. Paga o montante restante através dos seus métodos de pagamento, como cheque, transferência bancária ou cartão de crédito.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não estão abrangidos pelos Créditos do Azure

 Os produtos seguintes não são abrangidos pelos Créditos do Azure. Ser-lhe-á cobrada a utilização destes produtos, independentemente do saldo de crédito:

- Canonical
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
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Compreender a conta de faturação do Contrato de Cliente da Microsoft](../understand/mca-overview.md)
- [Compreender os termos na fatura do Contrato de Cliente da Microsoft](../understand/mca-understand-your-invoice.md)
