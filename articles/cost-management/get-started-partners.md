---
title: Introdução ao gerenciamento de custos do Azure para parceiros
description: Este artigo explica como os parceiros usam os recursos de gerenciamento de custos do Azure e como eles permitem o acesso de gerenciamento de custos para seus clientes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 6d59964013a2631430ecd7e46d1ce0f6be60a05f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802041"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introdução ao gerenciamento de custos do Azure para parceiros

O gerenciamento de custos do Azure está disponível nativamente para parceiros que integraram seus clientes a um contrato de clientes da Microsoft. Este artigo explica como os parceiros usam os recursos de [Gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management/) . Ele também descreve como os parceiros permitem o acesso de gerenciamento de custos para seus clientes. Os clientes podem usar os recursos de gerenciamento de custos quando habilitados pelo parceiro CSP.

Os parceiros CSP usam o gerenciamento de custos para:

- Entenda os custos faturados e associe os custos ao cliente, às assinaturas, aos grupos de recursos e aos serviços.
- Obtenha uma visão intuitiva dos custos do Azure na [análise de custos](quick-acm-cost-analysis.md) com recursos para analisar os custos por cliente, assinatura, grupo de recursos, recurso, medidor, serviço e muitas outras dimensões.
- Exibir os custos de recursos que têm o PEC (crédito ganho) do parceiro aplicado na análise de custo.
- Configure notificações e automação usando [orçamentos](tutorial-acm-create-budgets.md) programáticos e alertas quando os custos excederem orçamentos.
- Habilite a política de Azure Resource Manager que fornece acesso de cliente aos dados de gerenciamento de custos. Os clientes podem exibir dados de custo de consumo para suas assinaturas usando as tarifas pré- [pagas](https://azure.microsoft.com/pricing/calculator/).

Aqui está um exemplo mostrando os custos para todos os clientes.
![exemplo mostrando os custos para todos os clientes](./media/get-started-partners/customer-costs1.png)

Veja um exemplo que mostra os custos de um único cliente.
![exemplo mostrando os custos de um único cliente](./media/get-started-partners/customer-costs2.png)

Todas as funcionalidades disponíveis no gerenciamento de custos do Azure também estão disponíveis com APIs REST. Use as APIs para automatizar as tarefas de gerenciamento de custos.

## <a name="prerequisites"></a>Pré-requisitos

O gerenciamento de custos do Azure requer acesso de leitura à sua conta de cobrança ou assinatura. O acesso pode ser concedido em qualquer nível acima de seus recursos, da conta de cobrança ou de um grupo de gerenciamento para grupos de recursos individuais nos quais você gerencia seus aplicativos. Para obter mais informações sobre como habilitar e atribuir acesso ao gerenciamento de custos do Azure para uma conta de cobrança, consulte [atribuir funções e permissões de usuários](/partner-center/permissions-overview). As funções de **administrador global** e **agente de administração** podem gerenciar os custos de uma conta de cobrança.

Para exibir uma lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Como o gerenciamento de custos usa escopos

Os escopos são onde você gerencia os dados de cobrança, têm funções específicas para pagamentos, exibem faturas e realizam o gerenciamento geral da conta. As funções de cobrança e de conta são gerenciadas separadamente dos escopos usados para o gerenciamento de recursos, que usam o RBAC. Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são chamados de escopos de cobrança e escopos de RBAC, respectivamente.

Para entender escopos de cobrança e escopos de RBAC e como o gerenciamento de custos funciona com escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gerenciar custos com escopos de cobrança de locatário do parceiro

Depois de integrar seus clientes a um contrato do cliente da Microsoft, os _escopos de cobrança_ a seguir estão disponíveis em seu locatário. Use os escopos para gerenciar custos no gerenciamento de custos.

### <a name="billing-account-scope"></a>Escopo da conta de cobrança

Use o escopo da conta de cobrança para exibir os custos de imposto em todos os seus clientes e perfis de cobrança. Os custos da fatura são mostrados apenas para produtos baseados em consumo do cliente no contrato do cliente da Microsoft. No entanto, os custos de fatura são mostrados para produtos baseados em compra para clientes no contrato do cliente da Microsoft e na oferta do CSP. Atualmente, a moeda padrão para exibir os custos no escopo é de US $100,00. Os orçamentos definidos para o escopo também estão em USD.

Independentemente das diferentes moedas cobradas pelo cliente, os parceiros usam o escopo da conta de cobrança para definir orçamentos e gerenciar custos em USD em seus clientes, assinaturas, recursos e grupos de recursos.

Os parceiros também filtram os custos em uma moeda de cobrança específica entre clientes na exibição de análise de custo. Selecione a lista de **custo real** para exibir os custos em moedas de cobrança de cliente com suporte.

![Exemplo mostrando a seleção de custo real para moedas](./media/get-started-partners/actual-cost-selector.png)

Use a [exibição de custo amortizada](quick-acm-cost-analysis.md#customize-cost-views) em escopos de cobrança para exibir os custos amortizados da instância reservada em um termo de reserva.

### <a name="billing-profile-scope"></a>Escopo do perfil de cobrança

Use o escopo do perfil de cobrança para exibir os custos de imposto na moeda de cobrança em todos os seus clientes para todos os produtos e assinaturas incluídos em uma fatura. Você pode filtrar os custos em um perfil de cobrança para uma fatura específica usando o filtro **faturaid** . O filtro mostra os custos de consumo e compra de produto para uma nota fiscal específica. Você também pode filtrar os custos de um cliente específico na fatura para ver os custos de imposto prévio.

Depois de integrar os clientes a um contrato de cliente da Microsoft, você receberá uma fatura que inclui todos os encargos de todos os produtos (consumo, compras e direitos) para esses clientes no contrato do cliente da Microsoft. Quando cobrado na mesma moeda, essas notas fiscais também incluem os encargos de produtos adquiridos e de qualificação, como SaaS, Azure Marketplace e reservas para clientes que ainda estão na oferta do CSP.

Para ajudar a reconciliar os encargos em relação à fatura do cliente, o escopo do perfil de cobrança permite que você veja todos os custos acumulados para uma fatura para seus clientes. Como a fatura, o escopo mostra os custos de cada cliente no novo contrato do cliente da Microsoft. O escopo também mostra cada encargo para os produtos de direitos do cliente ainda na oferta atual do CSP.

O perfil de cobrança e os escopos de conta de cobrança são os únicos escopos aplicáveis que mostram encargos para produtos baseados em compras e de compra, como o Azure Marketplace e as compras de reserva.

Os perfis de cobrança definem as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro do Enterprise Agreement. Um perfil de cobrança é o escopo onde as notas fiscais são geradas.

Atualmente, a moeda de cobrança do cliente é a moeda padrão ao exibir os custos no escopo do perfil de cobrança. Os orçamentos definidos no escopo do perfil de cobrança estão na moeda de cobrança.

Os parceiros podem usar o escopo para reconciliar com faturas. E eles usam o escopo para definir orçamentos na moeda de cobrança para os seguintes itens:

- Fatura filtrada específica
- Cliente
- Subscrição
- Grupo de recursos
- Recurso
- Serviço do Azure
- Medidor
- ResellerMPNID

### <a name="customer-scope"></a>Escopo do cliente

Os parceiros usam o escopo para gerenciar os custos associados aos clientes que são integrados ao contrato do cliente da Microsoft. O escopo permite que os parceiros exibam os custos de imposto de um cliente específico. Você também pode filtrar os custos antes do imposto para uma assinatura, um grupo de recursos ou um recurso específico.

O escopo do cliente não inclui clientes que estão na oferta atual do CSP. O escopo inclui apenas os clientes que têm um contrato de cliente da Microsoft. Os custos de direito, não o uso do Azure, para a oferta atual do CSP oferecem aos clientes a conta de cobrança e os escopos do perfil de cobrança quando você aplica o filtro de cliente.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Acesso de parceiro a escopos de cobrança no gerenciamento de custos

Somente os usuários com funções de **administrador global** e **agente de administração** podem gerenciar e exibir os custos de contas de cobrança, perfis de cobrança e clientes diretamente no locatário do Azure do parceiro. Para obter mais informações sobre as funções do Partner Center, consulte [atribuir funções e permissões de usuários](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Habilitar o gerenciamento de custos no locatário do cliente

Os parceiros podem habilitar o acesso ao gerenciamento de custos depois que os clientes são integrados a um contrato de cliente da Microsoft. Em seguida, os parceiros podem habilitar uma política que permite aos clientes Exibir seus custos computados com taxas de varejo pagas conforme o uso. Os custos são mostrados na moeda de cobrança do cliente para seu uso consumido em escopos de grupos de recursos e assinatura RBAC.

Quando a política de visibilidade de custo é habilitada pelo parceiro, qualquer usuário com acesso Azure Resource Manager à assinatura pode gerenciar e analisar os custos de acordo com as tarifas pagas conforme o uso. Efetivamente, revendedores e clientes que têm o acesso RBAC apropriado às assinaturas do Azure podem exibir o custo.

Independentemente da política, os parceiros também podem exibir os custos se tiverem acesso à assinatura e ao grupo de recursos.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Habilitar a política para exibir os encargos de uso do Azure

Os parceiros usam as informações a seguir para habilitar a política para exibir os encargos de uso do Azure para seus clientes.

No portal do Azure, entre no locatário do parceiro e clique em **Gerenciamento de custos + cobrança**. Selecione uma conta de cobrança e clique em **clientes**. A lista de clientes está associada à conta de cobrança.

Na lista de clientes, selecione o cliente que você deseja permitir para exibir os custos.

![Selecionar clientes no gerenciamento de custos](./media/get-started-partners/customer-list.png)

Em **configurações**, clique em **políticas**.

A política de visibilidade de custo atual é mostrada para encargos de **uso do Azure** associados às assinaturas do cliente selecionado.
![política para permitir que os clientes exibam encargos pagos conforme o uso](./media/get-started-partners/cost-management-billing-policies.png)

Quando a política estiver definida como **não**, o gerenciamento de custos do Azure não estará disponível para os usuários de assinatura associados ao cliente. A menos que seja habilitado por um parceiro, a política de visibilidade de custo é desabilitada por padrão para todos os usuários de assinatura.

Quando a política de custo é definida como **Sim**, os usuários de assinatura associados ao locatário do cliente podem ver os encargos de uso em tarifas pagas conforme o uso.

Quando a política de visibilidade de custo é habilitada, todos os serviços que têm uso de assinatura mostram os custos em tarifas pagas conforme o uso. O uso de reserva é exibido com encargos zero para custos reais e amortizados. As compras e os direitos não estão associados a uma assinatura específica. Portanto, as compras não são exibidas no escopo da assinatura.

Para exibir os custos do locatário do cliente, abra gerenciamento de custos + cobrança e clique em contas de cobrança. Na lista de contas de cobrança, clique em uma conta de cobrança.

![Selecionar uma conta de cobrança](./media/get-started-partners/select-billing-account.png)

Em **cobrança**, clique em **assinaturas do Azure**e em um cliente.

![Selecionar um cliente de assinatura do Azure](./media/get-started-partners/subscriptions-select-customer.png)

Clique em **análise de custo** e comece a revisar os custos.
Análise de custo, orçamentos e alertas agora estão disponíveis para os escopos de RBAC de grupo de recursos e assinatura em custos pagos conforme o uso.

![Exibir análise de custo como um cliente ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

As exibições amortizadas e os custos reais das instâncias reservadas nos escopos do RBAC mostram zero encargos. Os custos de instância reservada são mostrados apenas em escopos de cobrança em que as compras foram feitas.

## <a name="analyze-costs-in-cost-analysis"></a>Analisar custos na análise de custo

Os parceiros podem explorar e analisar os custos na análise de custos entre clientes para um cliente específico ou para uma fatura. Os recursos filtrar e agrupar por permitem que você analise os custos por vários campos, incluindo:

| **Campo** | **Descrição** |
| --- | --- |
| PartnerTenantID | Identificador para o locatário de Azure Active Directory do parceiro |
| PartnerName | Nome do locatário do parceiro Azure Active Directory |
| CustomerTenantID | Identificador do locatário de Azure Active Directory da assinatura do cliente |
| Customer | Nome do locatário de Azure Active Directory que contém a assinatura do cliente |
| ResellerMPNID | MPNID para o revendedor associado à assinatura |
| ID da subscrição | Identificador exclusivo gerado pela Microsoft para a assinatura do Azure |
| subscriptionName | Nome da assinatura do Azure |
| billingProfileID | Identificador do perfil de cobrança. Ele agrupa os custos entre faturas em uma única moeda de cobrança entre os clientes.
| faturaid | ID da nota fiscal na nota fiscal em que a transação específica aparece |
| resourceGroup | Nome do grupo de recursos do Azure. Usado para gerenciamento do ciclo de vida de recursos. |
| partnerEarnedCreditRate | Taxa de desconto aplicada se houver um crédito de parceiro (PEC) com base no acesso de link do administrador do parceiro. |
| partnerEarnedCreditApplied | Indica se o crédito obtido do parceiro foi aplicado. |

Na exibição [análise de custo](quick-acm-cost-analysis.md) , você também pode [salvar exibições](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) e exportar dados para [arquivos CSV e png](quick-acm-cost-analysis.md#automation-and-offline-analysis).

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Exibir custos de recursos do PEC (crédito acumulado) do parceiro

No gerenciamento de custos do Azure, os parceiros podem usar a análise de custo para exibir os custos que receberam os benefícios do PEC.

No portal do Azure, entre no locatário do parceiro e selecione gerenciamento de **custos + cobrança**. Em **Gerenciamento de custos**, clique em **análise de custo**.

A exibição análise de custo mostra os custos da conta de cobrança do parceiro. Selecione o **escopo** conforme necessário para o parceiro, um cliente específico ou um perfil de cobrança para reconciliar faturas.

Em um gráfico de rosca, clique na lista suspensa e selecione **PartnerEarnedCreditApplied** para analisar os custos do PEC.

![Exemplo mostrando como exibir o crédito obtido pelo parceiro](./media/get-started-partners/cost-analysis-pec1.png)

Quando a propriedade **PartnerEarnedCreditApplied** é _verdadeira_, o custo associado tem o benefício do acesso de administrador obtido pelo parceiro.

Quando a propriedade **PartnerEarnedCreditApplied** é _false_, o custo associado não atende à qualificação necessária para o crédito. Ou, o serviço adquirido não está qualificado para crédito de parceiro ganho.

Os dados de uso do serviço normalmente levam 8-24 horas para serem exibidos no gerenciamento de custos. Para obter mais informações, consulte a [frequência de atualização de dados de uso varia](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Os créditos do PEC aparecem dentro de 48 horas a partir do tempo de acesso no gerenciamento de custos do Azure.


Você também pode agrupar e filtrar pela propriedade **PartnerEarnedCreditApplied** usando as opções **Agrupar por** . Use as opções para examinar os custos que fazem e não têm o PEC.

![Agrupar ou filtrar por crédito obtido pelo parceiro](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>APIs REST de gerenciamento de custos

Parceiros e clientes podem usar as APIs de gerenciamento de custos descritas nas seções a seguir para tarefas comuns.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>APIs de gerenciamento de custos do Azure – provedores diretos e indiretos

Os parceiros com acesso a escopos de cobrança em um locatário de parceiro podem usar as seguintes APIs para exibir os custos faturados.

As APIs no escopo da assinatura podem ser chamadas por um parceiro, independentemente da política de custo, se tiverem acesso à assinatura. Outros usuários com acesso à assinatura, como o cliente ou revendedor, podem chamar as APIs somente depois que o parceiro habilita a política de custo para o locatário do cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Para obter uma lista de contas de cobrança

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Para obter uma lista de clientes

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Para obter uma lista de assinaturas

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Para obter a política para os clientes exibirem custos

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Para definir a política para os clientes exibirem custos

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Para obter o uso do serviço do Azure para uma conta de cobrança

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Para baixar o uso do serviço do Azure de um cliente

A seguinte chamada Get é uma operação assíncrona.

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Chame o URI de `Location` retornado na resposta para verificar o status da operação. Quando o status for *concluído*, a propriedade `downloadUrl` conterá um link que você pode usar para baixar o relatório gerado.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Para obter ou baixar a folha de preços dos serviços do Azure consumidos

Primeiro, use a seguinte postagem.

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Em seguida, chame o valor da propriedade de operação assíncrona. Por exemplo:

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
A chamada Get anterior retorna o link de download que contém a folha de preços.

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Para obter os custos do cliente nos últimos dois meses, classificados por mês

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Para obter os custos de assinatura do Azure nos últimos dois meses, classificados por mês

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Para obter custos diários para o mês atual

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>Criar um orçamento para um parceiro

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>Criar um orçamento para um cliente

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>Excluir um orçamento

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>Passos seguintes
- [Comece a analisar os custos](quick-acm-cost-analysis.md) no gerenciamento de custos
- [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md) no gerenciamento de custos
