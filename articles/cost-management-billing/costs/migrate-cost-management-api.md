---
title: Migrar das APIs do EA para as APIs do Contrato de Cliente Microsoft – Azure
description: Este artigo ajuda-o a compreender as consequências da migração de um Contrato Enterprise (EA) Microsoft para um Contrato de Cliente Microsoft.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078354"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrar das APIs do Contrato Enterprise para as APIs do Contrato de Cliente Microsoft

Este artigo ajuda-o a compreender a estrutura de dados, a API e outras diferenças de integração do sistema entre as contas do Contrato Enterprise (EA) e do Contrato de Cliente Microsoft (MCA). O Azure Cost Management suporta APIs para ambos os tipos de conta. Antes de continuar, veja o artigo [Configurar a conta de faturação para um Contrato de Cliente Microsoft](../manage/mca-setup-account.md).

As organizações com uma conta EA existente devem ler este artigo durante a configuração de uma conta MCA. Anteriormente, a renovação de uma conta EA exigia um mínimo de trabalho para passar de uma inscrição antiga para uma nova. No entanto, a migração para uma conta MCA exige um esforço adicional. O esforço adicional deve-se a alterações no subsistema de faturação subjacente, que afetam todas as APIs relacionadas com os custos e as ofertas de serviço.

## <a name="mca-apis-and-integration"></a>APIs do MCA e integração

As APIs do MCA e a nova integração permitem-lhe:

- Ter disponibilidade completa das APIs através das APIs nativas do Azure.
- Configurar várias faturas numa conta de faturação única.
- Aceder a uma API combinada com a utilização dos serviços do Azure, a utilização do Marketplace de terceiros e as compras do Marketplace.
- Ver os custos nos perfis de faturação (idêntico às inscrições) com o Azure Cost Management.
- Aceder às novas APIs para mostrar os custos, obter notificações quando os custos ultrapassarem os limiares predefinidos e exportar automaticamente os dados não processados.

## <a name="migration-checklist"></a>Lista de verificação da migração

Os itens seguintes ajudam-no a fazer a transição para as APIs do MCA.

- Familiarize-se com a nova [conta de faturação do Contrato de Cliente Microsoft](../understand/mca-overview.md).
- Determine quais as APIs que utiliza e veja quais são substituídas na secção seguinte.
- Familiarize-se com as [Azure Resource Manager REST APIs](/rest/api/azure) (APIs REST do Azure Resource Manager).
- Se ainda não estiver a utilizar as APIs do Azure Resource Manager, veja [Register your client app with Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad) (Registar a aplicação de cliente no Azure Active Directory).
- Atualize o código de programação para [utilizar a autenticação do Azure Active Directory](/rest/api/azure/#create-the-request).
- Atualize o código de programação para substituir as chamadas às APIs do EA por chamadas às APIs do MCA.
- Atualize o processamento de erros para utilizar os novos códigos de erro.
- Analise as ofertas de integração adicionais, como a Cloudyn e o Power BI, para outras ações necessárias.

## <a name="ea-apis-replaced-with-mca-apis"></a>APIs do EA substituídas pelas APIs do MCA

As APIs do EA utilizam uma chave de API para autenticação e autorização. As APIs do MCA utilizam a autenticação do Azure Active Directory.

| Objetivo | API do EA | API do MCA |
| --- | --- | --- |
| Saldo e créditos | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilização (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilização (JSON) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilização do Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Períodos de faturação | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Folha de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAs/faturaçãoProfiles/pricesheet/default/download formato=json \| csv Microsoft.Billing/billingAcounts/.../billingProfiles/.../faturações/.../faturas/... /pricesheet/default/download format=json \| csv Microsoft.Billing/billingAccounts/.. /faturaçãoProfiles/.. /fornecedores/Microsoft.Consumption/pricesheets/download  |
| Compras de Reservas | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Recomendações da reserva | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilização da reserva | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> A utilização dos serviços do Azure e do Marketplace de terceiros está disponível com as [API de Detalhes de Utilização](/rest/api/consumption/usagedetails).

As seguintes APIs estão disponíveis para as contas de faturação MCA:

| Objetivo | API do Contrato de Cliente Microsoft (MCA) |
| --- | --- |
| Contas de faturação<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Perfis de faturação<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Secções de faturação<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faturas | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Subscrições de faturação | {scope}/billingSubscriptions |

<sup>2</sup> As APIs devolvem listas de objetos, que representam âmbitos, onde funcionam as experiências do Cost Management no portal do Azure e nas APIs. Para obter mais informações sobre os âmbitos do Cost Management, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

Se utilizar algumas APIs do EA existentes, precisará de as atualizar para suportar as contas de faturação MCA. A tabela seguinte mostra as outras alterações da integração:

| Objetivo | Oferta antiga | Oferta nova |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Conector e pacote de conteúdos do [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Conector do Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs para obter o saldo e créditos

A API [Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) (Obter Resumo do Saldo) dá-lhe um resumo mensal do seguinte:

- Saldos
- Novas compras
- Custos dos serviços do Azure Marketplace
- Ajustes
- Custos de utilização excedida de serviço

Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST).

A API Get Balance Summary (Obter Resumo do Saldo) é substituída pela API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Para obter os saldos disponíveis com a API Available Balance (Saldo Disponível):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs para obter os custos e a utilização

Obtenha uma discriminação diária dos custos da utilização dos serviços do Azure, da utilização do Marketplace de terceiros e de outras compras do Marketplace com as seguintes APIs. As seguintes APIs foram unidas para a utilização dos serviços do Azure e do Marketplace de terceiros. As APIs antigas são substituídas pela API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Adiciona as compras do Marketplace, que anteriormente só eram apresentadas no resumo do saldo até à data.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) (Obter detalhes da utilização/transferência)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) (Obter detalhes da utilização/submeter)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) (Obter detalhes da utilização/usagedetails)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) (Obter detalhes da utilização/usagedetailsbycustomdate)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) (Obter custos da loja do marketplace/marketplacecharges)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) (Obter custos da loja do marketplace/marketplacechargesbycustomdat)

Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST).

Todas as APIs anteriores são substituídas pela API Consumption/Usage Details (Consumo/Detalhes da Utilização).

Para obter os detalhes da utilização com a API Usage Details (Detalhes da Utilização):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A API Usage Details (Detalhes da Utilização), tal como todas as APIs do Cost Management, está disponível para vários âmbitos. Para obter os custos faturados, como tradicionalmente receberia ao nível da inscrição, utilize o âmbito do perfil de faturação.  Para obter mais informações sobre os âmbitos do Cost Management, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

| Tipo | Formato do ID |
| --- | --- |
| Conta de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Perfil de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscrição | `/subscriptions/{subscriptionId}` |
| Grupo de recursos | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Utilize os seguintes parâmetros querystring para atualizar o código de programação.

| Parâmetros antigos | Parâmetros novos |
| --- | --- |
| `billingPeriod={billingPeriod}` | Não suportado |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

O corpo da resposta também foi alterado.

Corpo da resposta antigo:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Corpo da resposta novo:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

O nome da propriedade que contém a matriz de registos de utilização foi alterado de dados para _valores_. Cada registo tinha uma lista não hierárquica das propriedades detalhadas. No entanto, agora em cada registo todos os detalhes estão numa propriedade aninhada denominada _properties_, exceto para as etiquetas. A nova estrutura é consistente com as outras APIs do Azure. Os nomes de algumas propriedades foram alterados. A tabela seguinte mostra as propriedades correspondentes.

| Propriedade antiga | Propriedade nova | Notas |
| --- | --- | --- |
| AccountId | N/D | O criador da subscrição não é controlado. Utilize invoiceSectionId (idêntico a departmentId). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/D | O criador da subscrição não é controlado. Utilize invoiceSectionName (idêntico a departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Tenha em atenção que estas propriedades são opostas. Se isAzureCreditEnabled for verdadeiro, ChargesBilledSeparately dever ser falso. |
| ConsumedQuantity | quantidade | &nbsp; |
| ConsumedService | consumedService | Os valores exatos da cadeia podem ser diferentes. |
| ConsumedServiceId | Nenhum | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date e usageStartDate | data | &nbsp;  |
| Dia | Nenhum | Analisa o dia a partir da data. |
| DepartmentId | invoiceSectionId | Os valores exatos diferem. |
| DepartmentName | invoiceSectionName | Os valores exatos da cadeia podem ser diferentes. Configure as secções da fatura para corresponderem aos departamentos, se necessário. |
| ExtendedCost e Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | Nenhum | &nbsp;  |
| Localização | localização | &nbsp;  |
| MeterCategory | meterCategory | Os valores exatos da cadeia podem ser diferentes. |
| MeterId | meterId | Os valores exatos da cadeia são diferentes. |
| MeterName | meterName | Os valores exatos da cadeia podem ser diferentes. |
| MeterRegion | meterRegion | Os valores exatos da cadeia podem ser diferentes. |
| MeterSubCategory | meterSubCategory | Os valores exatos da cadeia podem ser diferentes. |
| Mensal | Nenhum | Analisa o mês a partir da data. |
| Nome da Oferta | Nenhum | Utilize publisherName e productOrderName. |
| OfferId | Nenhum | &nbsp;  |
| Número da Encomenda | Nenhum | &nbsp;  |
| PartNumber | Nenhum | Utilize meterId e productOrderName para identificar exclusivamente os preços. |
| Nome do Plano | productOrderName | &nbsp;  |
| Produto | Produto |   |
| ProductId | productId | Os valores exatos da cadeia são diferentes. |
| Nome do Editor | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Os valores exatos da cadeia são diferentes. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Nenhum | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/D | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Os valores exatos da cadeia podem ser diferentes. |
| ServiceTier | meterSubCategory | Os valores exatos da cadeia podem ser diferentes. |
| StoreServiceIdentifier | N/D | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Etiquetas | etiquetas | A propriedade tags aplica-se ao objeto raiz, não à propriedade das propriedades aninhadas. |
| UnitOfMeasure | unitOfMeasure | Os valores exatos da cadeia são diferentes. |
| usageEndDate | data | &nbsp;  |
| Anual | Nenhum | Analisa o ano a partir da data. |
| (nova) | billingCurrency | Moeda utilizada para a cobrança. |
| (nova) | billingProfileId | ID único do perfil de faturação (idêntico à inscrição). |
| (nova) | billingProfileName | Nome do perfil de faturação (idêntico à inscrição). |
| (nova) | chargeType | Permite diferenciar a utilização dos serviços do Azure, a utilização do Marketplace e as compras. |
| (nova) | invoiceId | ID exclusivo da fatura. Vazio para o mês atual aberto. |
| (nova) | publisherType | Tipo de editor das compras. Vazio para utilização. |
| (nova) | serviceFamily | Tipo de compra. Vazio para utilização. |
| (nova) | servicePeriodEndDate | Data de fim do serviço comprado. |
| (nova) | servicePeriodStartDate | Data de início do serviço comprado. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API Billing Periods (Períodos de Faturação) substituído pela API Invoices (Faturas)

As contas de faturação MCA não utilizam períodos de faturação. Em vez disso, utilizam as faturas para associar os custos a períodos específicos de faturação. A API [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) (Períodos de Faturação) é substituída pela API Invoices (Faturas). Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST).

Para obter as faturas com a API Invoices (Faturas):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>APIs Price Sheet (Folha de Preços)

Esta secção explica as APIs Price Sheet (Folha de Preços) existentes e apresenta recomendações para mover para a API Price Sheet (Folha de Preços) para Contratos de Cliente Microsoft. Também explica a API Price Sheet (Folha de Preços) para os Contratos de Cliente Microsoft e explica os campos nas folhas de preços. As APIs [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) (Obter folha de preços do Enterprise) e [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) (Obter períodos de faturação do Enterprise) são substituídos pela API Price Sheet (Folha de Preços) para os Contratos de Cliente Microsoft (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). A nova API suporta os formatos JSON e CSV, nos formatos REST assíncronos. Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST).

### <a name="billing-enterprise-apis"></a>APIs Billing Enterprise (Faturação do Enterprise)

Utilizava as APIs Billing Enterprise (Faturação do Enterprise) com as inscrições Enterprise para obter as informações sobre os preços e o período de faturação. Os tokens Web do Azure Active Directory eram utilizados para autenticação e autorização.

Para obter os preços aplicáveis da Inscrição Enterprise especificada com as APIs de Price Sheet (Folha de Preços) e Billing Period (Período de Faturação):

| Método | URI do pedido |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API Price Sheet (Folha de Preços) para Contratos de Cliente Microsoft

Utilize a API Price Sheet (Folha de Preços) para os Contratos de Cliente Microsoft para ver os preços de todos os serviços de consumo do Azure Consumption e do Marketplace. Os preços apresentados para o perfil de faturação aplicam-se a todas as subscrições que pertencem ao perfil de faturação.

Utilize a API Price Sheet (Folha de Preços) para ver todos os dados da Folha de Preços dos serviços do Azure Consumption no formato CSV:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Utilize a API Price Sheet (Folha de Preços) para ver todos os dados da Folha de Preços dos serviços do Azure Consumption no formato JSON:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Utilize a API para apresentar a folha de preços de toda a conta. No entanto, também pode obter uma versão condensada da folha de preços no formato PDF. O resumo inclui os serviços de consumo do Azure Consumption e do Marketplace que são faturados para uma fatura específica. A fatura é identificada pelo {invoiceId}, que é idêntico ao **Número da Fatura** apresentado nos ficheiros PDF de Resumo da Fatura. Eis um exemplo.

![Imagem de exemplo a mostrar o Número da Fatura que corresponde ao InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Para ver as informações da fatura com a API Price Sheet (Folha de Preços) no formato CSV:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para ver as informações da fatura com a API Price Sheet (Folha de Preços) no formato JSON:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Também pode ver a estimativa de preços de qualquer serviço de consumo do Azure Consumption ou do Marketplace no atual período de serviço ou ciclo de faturação aberto.

Para ver os preços estimados dos serviços de consumo com a API Price Sheet (Folha de Preços) no formato CSV:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para ver os preços estimados dos serviços de consumo com a API Price Sheet (Folha de Preços) no formato JSON:

| Método | URI do pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

As APIs Price Sheet (Folha de Preços) do Contrato de Cliente Microsoft são *APIs REST assíncronas*. As respostas das APIs foram alteradas comparativamente às APIs síncronas mais antigas. O corpo da resposta das APIs também foi alterado.

#### <a name="old-response-body"></a>Corpo da resposta antigo

Veja a seguir um exemplo de resposta da API REST síncrona:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Corpo da resposta novo

As APIs suportam o formato [assíncrono REST do Azure](../../azure-resource-manager/management/async-operations.md). Se chamar a API com GET, receberá a resposta seguinte:

```
No Response Body

HTTP Status 202 Accepted
```

Os seguintes cabeçalhos são enviados com a localização da saída:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Faça outra chamada GET para a localização. A resposta à chamada GET é idêntica até que a operação atinja um estado de falha ou de conclusão. Quando concluída, a resposta à localização da chamada GET devolve o URL de transferência. Como se a operação fosse executada ao mesmo tempo. Eis um exemplo:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

O cliente também pode fazer uma chamada GET para `Azure-AsyncOperation`. O ponto final devolve o estado da operação.

A tabela seguinte mostra os campos na API Enterprise Get price sheet (Obter folha de preços do Enterprise) mais antiga. Inclui os campos correspondentes na nova folha de preços para os Contratos de Cliente Microsoft:

| Propriedade antiga | Propriedade nova | Notas |
| --- | --- | --- |
| billingPeriodId  | _Não aplicável_ | Não aplicável. Para os Contratos de Cliente Microsoft, a fatura e a folha de preços associada substituíram o conceito de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Os valores exatos da cadeia podem ser diferentes. |
| includedQuantity  | includedQuantity | Não aplicável aos serviços nos Contratos de Cliente Microsoft. |
| partNumber  | _Não aplicável_ | Em vez disso, utilize uma combinação de productOrderName (idêntico a offerID) e meterID. |
| unitPrice  | unitPrice | O preço unitário é aplicável aos serviços consumidos no Contrato de Cliente Microsoft. |
| currencyCode  | pricingCurrency | Os Contratos de Cliente Microsoft possuem representações na moeda do preço e moeda da fatura. O currencyCode corresponde a pricingCurrency nos Contratos de Cliente Microsoft. |
| offerID | productOrderName | Em vez de OfferID, pode utilizar productOrderName, mas não é idêntico a OfferID. No entanto, a propriedade productOrderName e o medidor determinam os preços nos Contratos de Cliente Microsoft relacionados com meterId e OfferID em inscrições legadas. |

## <a name="consumption-price-sheet-api-operations"></a>Operações da API Consumption Price Sheet (Folha de Preços de Consumo)

Nos Contratos Enterprise, utilizou a API Consumption Price Sheet (Folha de Preços de Consumo) nas operações [Get](/rest/api/consumption/pricesheet/get) (Obter) e [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) (Obter por Período de Faturação) para um âmbito por subscriptionId ou por período de faturação. A API utiliza a autenticação da Gestão de Recursos do Azure.

Para obter as informações da Folha de Preços para um âmbito com a API Price Sheet (Folha de Preços):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para obter as informações da Folha de Preços por período de faturação com a API Price Sheet (Folha de Preços):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Em vez dos pontos finais da API acima indicados, utilize os que se seguem para os Contratos de Cliente Microsoft:

**API Price Sheet (Folha de Preços) para Contratos de Cliente Microsoft (API REST assíncrona)**

Esta API destina-se aos Contratos de Cliente Microsoft e oferece atributos adicionais.

**Folha de preços para um perfil de faturação numa conta de faturação**

Esta é a API existente. Foi atualizada para apresentar a folha de preços de um perfil de faturação numa conta de faturação.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Folha de Preços para um âmbito por conta de faturação

A autenticação do Azure Resource Manager é utilizada quando obtém a Folha de Preços no âmbito da inscrição numa conta de faturação.

Para obter a Folha de Preços da conta de inscrição numa conta de faturação:

| Método | URI do pedido |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Para um Contrato de Cliente Microsoft, utilize as informações da seguinte secção. Apresenta as propriedades dos campos utilizadas para os Contratos de Cliente Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Folha de Preços para um âmbito de perfil de faturação numa conta de faturação

A API Price Sheet by billing account (Folha de Preços por conta de faturação) atualizada obtém a Folha de Preços no formato CSV. Para obter a Folha de Preços no âmbito do perfil de faturação para um MCA:

| Método | URI do pedido |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

No âmbito da inscrição do EA, a resposta e as propriedades da API são idênticas. As propriedades correspondem às mesmas propriedades do MCA.

A tabela seguinte apresenta as propriedades mais antigas das [Azure Resource Manager Price Sheet APIs](/rest/api/consumption/pricesheet) (APIs de Folha de Preços do Azure Resource Manager) e as propriedades novas equivalentes.

| Antiga Propriedade da API Azure Resource Manager Price Sheet (Folha de Preços do Azure Resource Manager)  | Nova Propriedade da API Azure Resource Manager Price Sheet (Folha de Preços do Contrato de Cliente Microsoft)   | Description |
| --- | --- | --- |
| ID do Medidor | _meterId_ | Identificador exclusivo do medidor. Idêntico a meterID. |
| Nome do medidor | meterName | Nome do medidor. O medidor representa o recurso implementável dos serviços do Azure. |
| Categoria do medidor  | serviço | Nome da categoria de classificação do medidor. Idêntico ao do serviço na Folha de Preços do Contrato de Cliente Microsoft. Os valores exatos da cadeia são diferentes. |
| Subcategoria do medidor | meterSubCategory | Nome da categoria de subclassificação do medidor. Com base na classificação da diferenciação das definições de funcionalidades de alto nível no serviço. Por exemplo, Base de Dados SQL Básica vs. Base de Dados SQL Standard. |
| Região do medidor | meterRegion | &nbsp;  |
| Unidade | _Não aplicável_ | Pode ser analisada em unitOfMeasure. |
| Unidade de medida | unitOfMeasure | &nbsp;  |
| Número de peça | _Não aplicável_ | Em vez do número de peça, utilize productOrderName e MeterID para identificar de forma exclusiva o preço num perfil de faturação. Os campos são listados na fatura do MCA em vez do número de peça nas faturas do MCA. |
| Preço unitário | unitPrice | Preço unitário do Contrato de Cliente Microsoft. |
| Código de moeda | pricingCurrency | Os Contratos de Cliente Microsoft representam os preços na moeda do preço e na moeda da fatura. O código de moeda é idêntico a pricingCurrency nos Contratos de Cliente Microsoft. |
| Quantidade incluída | includedQuantity | Não é aplicável aos serviços nos Contratos de Cliente Microsoft. Apresentada com valores iguais a zero. |
|  ID da oferta  | productOrderName | Em vez de OfferID, utilize productOrderName. Não é idêntico a OfferID. No entanto, productOrderName e o medidor determinam os preços nos Contratos de Cliente Microsoft. Relacionado com meterId e OfferID nas inscrições legadas. |

O preço dos Contratos de Cliente Microsoft é definido de forma diferente do preço dos contratos Enterprise. O preço dos serviços na inscrição Enterprise é exclusivo do produto, do número de peça, do medidor e da oferta. O número de peça não é utilizado nos Contratos de Cliente Microsoft.

O preço dos serviços do Azure Consumption, que faz parte de um Contrato de Cliente Microsoft, é exclusivo de productOrderName e de meterID. Representam o medidor dos serviços e o plano do produto.

Para obter a reconciliação entre a folha de preços e a utilização na API Usage Details (Detalhes da Utilização), pode utilizar productOrderName e meterID.

Os utilizadores que tenham direitos de gestor de faturas, leitor, contribuidor e proprietário do perfil de faturação podem transferir a folha de preços.

As folhas de preço incluem preços para serviços cujo preço é baseado na utilização. Os serviços incluem o consumo do Azure e o consumo do Marketplace. O preço mais recente no final de cada período de serviço é bloqueado e aplicado à utilização num único período de serviço. Para os serviços de consumo do Azure, o período de serviço corresponde normalmente a um mês de calendário.

### <a name="retired-price-sheet-api-fields"></a>Campos retirados da API Price Sheet (Folha de Preços)

Os seguintes campos não estão disponíveis nas APIs Price Sheet (Folha de Preços) do Contrato de Cliente Microsoft ou correspondem a campos idênticos.

|Campo retirado| Description|
|---|---|
| billingPeriodId | Não aplicável. Corresponde a InvoiceId no MCA. |
| offerID | Não aplicável. Corresponde a productOrderName no MCA. |
| meterCategory  | Não aplicável. Corresponde a Serviço no MCA. |
| unit | Não aplicável. Pode ser analisada em unitOfMeasure. |
| currencyCode | Idêntico a pricingCurrency no MCA. |
| meterLocation | Idêntico a meterRegion no MCA. |
| partNumber partnumber | Não aplicável porque o número de peça não está listado nas faturas do MCA. Em vez do número de peça, utilize a combinação meterId e productOrderName para identificar de forma exclusiva os preços. |
| totalIncludedQuantity | Não aplicável. |
| pretaxStandardRate  | Não aplicável. |

## <a name="reservation-instance-charge-api-replaced"></a>Substituição da API Reservation Instance Charge (Custo de Instância Reservada)

Pode obter as transações de faturação das compras de reservas com a API [Reserved Instance Charge](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Custo de Instância Reservada). A nova API inclui todas as compras, incluindo as ofertas do Marketplace de terceiros. Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST). A API Reserved Instance Charge (Custo de Instância Reservada) é substituída pela API Transactions (Transações).

Para obter as transações das compras de reservas com a API Transactions (Transações):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Substituição das APIs Recommendations (Recomendações)

As APIs Reserved Instance Purchase Recommendations (Recomendações de Compras de Instância Reservada) apresentam a utilização das máquinas virtuais ao longo dos últimos 7, 30 ou 60 dias. As APIs também apresentam recomendações de compras de reservas. Estas incluem:

- API [Shared Reserved Instance Recommendation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) (Recomendação de Instância Reservada Partilhada)
- API [Single Reserved Instance Recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) (Recomendação de Instância Reservada Única)

Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST). As APIs de recomendações de reserva listadas anteriormente são substituídas pela API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Para obter as recomendações de reserva com a API Reservation Recommendations (Recomendações de Reserva):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Substituição das APIs Reservation Usage (Utilização de Reservas)

Pode obter a utilização da reserva numa inscrição com a API Reserved Instance Usage (Utilização de Instâncias Reservadas). Se existir mais de uma instância reservada numa inscrição, também poderá obter a utilização de todas as compras de instâncias reservadas com esta API.

Estas incluem:

- [Detalhes de utilização de instâncias reservadas](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Reserved Instance Usage Summary (Resumo da Utilização de Instâncias Reservadas)](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Todas as APIs Consumption (Consumo) são substituídas pelas APIs nativas do Azure que utilizam o Azure Active Directory para autenticação e autorização. Para obter mais informações sobre as chamadas de APIs REST do Azure, veja [Getting started with REST](/rest/api/azure/#create-the-request) (Introdução ao REST). As APIs de recomendações de reserva listadas anteriormente são substituídas pelas APIs [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Para obter os detalhes da reserva com a API Reservation Details (Detalhes da Reserva):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Para obter os resumos da reserva com a API Reservation Summaries (Resumos da Reserva):

| Método | URI do pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Migrar da Cloudyn para o Cost Management

As organizações que utilizam a [Cloudyn](https://cloudyn.com) devem começar a utilizar a [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) para quaisquer necessidades de gestão de custos. O Cost Management está disponível no portal do Azure sem integração e com uma latência de oito horas. Para obter mais informações, veja a [Documentação do Cost Management](../index.yml).

Com o Azure Cost Management, pode:

- Ver os custos ao longo do tempo num orçamento predefinido. Analisar padrões de custos diários para identificar e parar anomalias a nível de gastos. Discriminar os custos por etiquetas, grupo de recursos, serviços e localizações.
- Criar orçamentos para definir limites de utilização e custos e ser notificado quando se aproximar de limiares importantes. Configurar a automação com grupos de ação para acionar eventos personalizados e impor limites restritivos segundo as suas condições.
- Otimizar o custo e a utilização com recomendações do Assistente do Azure. Descobrir otimizações de compras com reservas, reduzir as máquinas virtuais pouco utilizadas e eliminar recursos não utilizados para se manter dentro dos orçamentos.
- Agendar a exportação diária dos dados de custos e utilização para publicar um ficheiro CSV na conta de armazenamento. Automatizar a integração com sistemas externos para manter os dados de faturação sincronizados e atualizados.

## <a name="power-bi-integration"></a>Integração do Power BI

Também pode utilizar o Power BI para a criação de relatórios de custos. O [conector do Azure Cost Management](/power-bi/desktop-connect-azure-cost-management) para o Power BI Desktop pode ser utilizado para criar relatórios poderosos e personalizados que o ajudem a compreender melhor os seus gastos do Azure. O conector do Azure Cost Management atualmente suporta clientes com um Contrato de Cliente Microsoft ou um Contrato Enterprise (EA).

## <a name="next-steps"></a>Passos seguintes

- Leia a [Documentação do Cost Management](../index.yml) para saber como monitorizar e controlar as despesas do Azure. Ou, se quiser otimizar a utilização dos recursos com o Cost Management.
