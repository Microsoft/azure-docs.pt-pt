---
title: Migrar do contrato Enterprise para APIs de contrato de cliente da Microsoft – Azure | Documentos da Microsoft
description: Este artigo ajuda-o a compreender as conseqüências de migrar um Microsoft Enterprise Agreement (EA) para um contrato de cliente da Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 9f5ea30c458954a3e87b9575b5632fbfb0df97b8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318603"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrar do contrato Enterprise para APIs de contrato de cliente da Microsoft

Este artigo ajuda-o a compreender a estrutura de dados, a API e outras diferenças de integração do sistema entre contas do Enterprise Agreement (EA) e o contrato de cliente da Microsoft (MCA). O Azure Cost Management suporta APIs para ambos os tipos de conta. Reveja os [configurar a conta de cobrança para](../billing/billing-mca-setup-account.md) artigo de contrato de cliente da Microsoft antes de continuar.

As organizações com uma conta existente do EA, devem rever este artigo em conjunto com a configuração de uma conta MCA. Anteriormente, a renovação de uma conta EA necessário algum trabalho mínimo para mover de uma inscrição antiga para um novo. No entanto, a migração para uma conta MCA exige esforço adicional. Esforço adicional é devido a alterações no subsistema de faturação subjacente, que afetam todas as APIs relacionadas com o custo e ofertas de serviço.

## <a name="mca-apis-and-integration"></a>APIs de MCA e integração

APIs de MCA e a nova integração permitem-lhe:

- Ter disponibilidade completa de API através de APIs nativas do Azure.
- Configure vários faturas numa única conta de faturação.
- Aceda a uma API combinada com a utilização do serviço do Azure, o uso do Marketplace de terceiros e compras do Marketplace.
- Ver os custos em toda a faturação perfis (o mesmo que inscrições) com o Azure Cost Management.
- Aceder às APIs de novo para mostrar os custos, será notificado quando os custos excedem os limiares predefinidos e exportar dados não processados automaticamente.

## <a name="migration-checklist"></a>Lista de verificação da migração

A ajuda de itens seguintes que faça a transição para MCA APIs.

- Familiarize-se com o novo [contrato de cliente da Microsoft conta de faturação](../billing/billing-mca-overview.md).
- Determine quais APIs utiliza e ver quais os que são substituídos na secção seguinte.
- Familiarize-se com [APIs de REST do Azure Resource Manager](/rest/api/azure).
- Se estiver a utilizar APIs do Azure Resource Manager, ainda não estiver [registar a sua aplicação de cliente com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Atualizar qualquer código de programação para [autenticação de utilização do Azure AD](/rest/api/azure/#create-the-request).
- Atualize qualquer código de programação para substituir as chamadas à API de EA com chamadas de API de MCA.
- Atualize o tratamento de erros a utilizar novos códigos de erro.
- Reveja as ofertas de integração adicional, como Cloudyn e o Power BI, para outros necessária ação.

## <a name="ea-apis-replaced-with-mca-apis"></a>APIs de EA substituído com as APIs de MCA

APIs de EA utilizar uma chave de API para autenticação e autorização. APIs de MCA utilizam a autenticação do Azure AD.

| Objetivo | API DE EA | API DE MCA |
| --- | --- | --- |
| Saldo e créditos | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilização (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilização (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilização do Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Períodos de faturação | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Folha de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Compras de reserva | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Recomendações de reserva | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilização de reserva | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> serviço do azure e a utilização do Marketplace de terceiros estão disponíveis com o [API de detalhes de utilização](/rest/api/consumption/usagedetails).

As seguintes APIs estão disponíveis para contas de faturas MCA:

| Objetivo | API de contrato (MCA) de cliente da Microsoft |
| --- | --- |
| Contas de faturação<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Perfis de faturação<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Secções de nota fiscal<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faturas | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Subscrições de faturação | {scope}/billingSubscriptions |

<sup>2</sup> APIs devolvem listas de objetos, que são âmbitos, em que ocorre com o Cost Management no portal do Azure e operam de APIs. Para obter mais informações sobre âmbitos de gestão de custos, veja [entender e trabalhar com âmbitos](understand-work-scopes.md).

Se utilizar qualquer APIs existentes do EA, terá de atualizá-las para oferecer suporte a contas de faturas MCA. A tabela a seguir mostra outras alterações de integração:

| Objetivo | Oferta antiga | Nova oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gestão de Custos do Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Informações sobre consumo do Microsoft](/power-bi/desktop-connect-azure-consumption-insights) conector e pacote de conteúdos | [Aplicação do Microsoft Azure consumo Insights Power BI](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) e [ conector Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs para obter o saldo e créditos

O [obtenha o resumo de saldo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API fornece um resumo mensal de:

- Saldos
- Novas compras
- Encargos de serviços do Azure Marketplace
- Ajustes
- Custos de utilização excessiva de serviço

Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

A API de resumo do saldo de obter é substituída pela Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API.

Para obter os saldos disponíveis com a API de saldo disponíveis:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs para obter o custo e a utilização

Obtenha uma análise detalhada diária dos custos de utilização do serviço do Azure, a utilização do Marketplace de terceiros e a outras compras do Marketplace com as seguintes APIs. As seguintes APIs separadas foram mescladas para serviços do Azure e o uso do Marketplace de terceiros. As APIs antigas são substituídas pelos [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Ele adiciona as compras no Marketplace, que foram anteriormente só mostra o saldo resumo até à data.

- [Obter detalhes de utilização/transferência](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter a submeter/detalhes de utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de utilização/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter detalhes de utilização/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter o custo de armazenamento de marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Obter o custo de armazenamento de marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

Todas as APIs anteriores são substituídas pela API de detalhes do consumo/utilização.

Para obter detalhes de utilização com a API de detalhes de utilização:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A API de detalhes de utilização, tal como acontece com todas as APIs de gestão de custos, está disponível em vários âmbitos. Para os custos de faturadas, conforme receberia tradicionalmente ao nível da inscrição, utilize o âmbito de perfil de faturação.  Para obter mais informações sobre âmbitos de gestão de custos, veja [entender e trabalhar com âmbitos](understand-work-scopes.md).

| Type | Formato de ID |
| --- | --- |
| Conta de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Perfil de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscrição | `/subscriptions/{subscriptionId}` |
| Grupo de recursos | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Utilize os seguintes parâmetros de cadeia de consulta para atualizar qualquer código de programação.

| Parâmetros antigos | Novos parâmetros |
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

Alterar o nome de propriedade que contém a matriz de registos de utilização dos dados para _valores_. Cada registo utilizado para ter uma lista simples de propriedades detalhadas. No entanto, cada registo agora todos os detalhes estão agora numa propriedade aninhada chamada _propriedades_, exceto etiquetas. A nova estrutura é consistente com as outras APIs do Azure. Alguns nomes de propriedade foram alterados. A tabela seguinte mostra as propriedades correspondentes.

| Propriedade antiga | Nova propriedade | Notas |
| --- | --- | --- |
| Id da Conta | N/A | Não é acompanhado o criador de subscrição. Utilize invoiceSectionId (mesmo que departmentId). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/A | Não é acompanhado o criador de subscrição. Utilize invoiceSectionName (mesmo que departmentName). |
| Informações Adicionais | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observe que essas propriedades são opposites. Se isAzureCreditEnabled for verdadeira, ChargesBilledSeparately teria de ser falso. |
| Quantidade Consumida | quantidade | &nbsp; |
| Serviço Consumido | consumedService | Valores de cadeia exacta poderão diferir. |
| Id do Serviço Consumido | Nenhuma | &nbsp; |
| Centro de Custo | costCenter | &nbsp; |
| Data e usageStartDate | date | &nbsp;  |
| Dia | Nenhuma | Analisa o dia da data. |
| Id do Departamento | invoiceSectionId | Valores exatos são diferentes. |
| Nome do Departamento | invoiceSectionName | Valores de cadeia exacta poderão diferir. Configure secções de nota fiscal, de acordo com os departamentos, se necessário. |
| ExtendedCost e custo | costInBillingCurrency | &nbsp;  |
| Id da Instância | resourceId | &nbsp;  |
| É Taxa Periódica | Nenhuma | &nbsp;  |
| Localização | localização | &nbsp;  |
| Categoria do Medidor | meterCategory | Valores de cadeia exacta poderão diferir. |
| Id do Medidor | meterId | Valores de cadeia exacta diferentes. |
| Nome do Medidor | meterName | Valores de cadeia exacta poderão diferir. |
| Região do Medidor | meterRegion | Valores de cadeia exacta poderão diferir. |
| Sub-categoria do Medidor | meterSubCategory | Valores de cadeia exacta poderão diferir. |
| Mês | Nenhuma | Analisa o mês da data. |
| Nome da Oferta | Nenhuma | Utilize publisherName e productOrderName. |
| OfferId | Nenhuma | &nbsp;  |
| Número de Encomenda | Nenhuma | &nbsp;  |
| PartNumber | Nenhuma | Utilize meterId e productOrderName para identificar exclusivamente os preços. |
| Nome do Plano | productOrderName | &nbsp;  |
| Produto | Produto |   |
| Id do Produto | productId | Valores de cadeia exacta diferentes. |
| Nome do Publicador | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Valores de cadeia exacta diferentes. |
| Localização do Recurso | resourceLocation | &nbsp;  |
| Id da Localização do Recurso | Nenhuma | &nbsp;  |
| Taxa de Recursos | effectivePrice | &nbsp;  |
| Id de Administrador de Serviço | N/A | &nbsp;  |
| Informações de Serviço 1 | serviceInfo1 | &nbsp;  |
| Informações de Serviço 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Valores de cadeia exacta poderão diferir. |
| ServiceTier | meterSubCategory | Valores de cadeia exacta poderão diferir. |
| Identificador de Serviço da Loja | N/A | &nbsp;  |
| Guid de Subscrição | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Nome da Subscrição | subscriptionName | &nbsp;  |
| Etiquetas | etiquetas | A propriedade tags aplica-se ao objeto de raiz, não para a propriedade de propriedades aninhada. |
| Unidade de Medida | unitOfMeasure | Valores de cadeia exacta diferentes. |
| usageEndDate | date | &nbsp;  |
| Ano | Nenhuma | Analisa o ano a contar da data. |
| (novo) | billingCurrency | Moeda utilizada para a cobrança. |
| (novo) | billingProfileId | ID exclusivo para o perfil de faturação (igual a inscrição). |
| (novo) | billingProfileName | Nome do perfil de faturação (igual a inscrição). |
| (novo) | chargeType | Utilize para diferenciar a utilização do serviço do Azure, a utilização de Marketplace e compras. |
| (novo) | invoiceId | ID exclusivo da nota fiscal. Vazio para o mês atual, abra. |
| (novo) | publisherType | Tipo de publicador para compras. Vazio para utilização. |
| (novo) | serviceFamily | Tipo de compra. Vazio para utilização. |
| (novo) | servicePeriodEndDate | Data de fim para o serviço adquirida. |
| (novo) | servicePeriodStartDate | Data para o serviço adquirida de início. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Substituído pela API de notas fiscais de API de períodos de faturação

Contas de faturas MCA não use períodos de faturação. Em vez disso, eles usam notas fiscais para os custos de âmbito para períodos de faturação específicos. O [API de períodos de faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) é substituído pela API de notas fiscais. Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

Para obter notas fiscais com a API de notas fiscais:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>APIs de folha de preços

Esta seção discute as APIs de folha de preços existente e fornece recomendações para mover para a API da folha de preços para contratos de cliente da Microsoft. Ele também discute a API da folha de preços para contratos de cliente da Microsoft e explica os campos em folhas de preços. O [Enterprise obter a folha de preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Enterprise obter períodos de faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) APIs são substituídas pela API da folha de cálculo do preço para contratos de cliente da Microsoft (Microsoft.Billing/billingAccounts/billingProfiles / folha de preços). A nova API suporta formatos CSV e JSON, nos formatos REST assíncronos. Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Enterprise APIs de faturação

APIs de faturação de Enterprise com inscrições de empresas que utilizou para obter informações de período de faturação e de preço. Autenticação e autorização utilizado tokens de web do Azure Active Directory.

Para obter preços aplicáveis para a inscrição de Enterprise especificados com a folha de preços e as APIs do período de faturação:

| Método | URI de pedido |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API de folha de preços para contratos com o cliente da Microsoft

Utilize a API de folha de preços para contratos de cliente da Microsoft para ver os preços para todos os serviços de consumo de consumo do Azure e o Marketplace. Os preços apresentados para o perfil de faturação aplicam-se a todas as subscrições que pertencem ao perfil de faturação.

Utilize a API da folha de preços para ver todos os dados de folha de preços de serviços de consumo do Azure no formato CSV:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Utilize a API da folha de preços para ver todos os dados de folha de preços de serviços de consumo do Azure no formato JSON:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Com a API devolve a folha de preços para toda a conta. No entanto, também pode obter uma versão condensada da folha de preços no formato PDF. O resumo inclui o consumo do Azure e o Marketplace serviços de consumo que são faturados para uma nota fiscal específica. A nota fiscal é identificada pelo {invoiceId}, que é igual a **número da nota fiscal** mostra os ficheiros PDF de resumo de nota fiscal. Eis um exemplo.

![Imagem de exemplo que mostra o número de nota fiscal que corresponde à InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Para ver informações de nota fiscal com a API da folha de preços no formato CSV:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para ver informações de nota fiscal com a API da folha de preços no formato JSON:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Também pode ver os preços estimados para qualquer serviço de consumo de consumo do Azure ou do Marketplace no ciclo de faturação aberto atual ou o período de serviço.

Para ver os preços estimados para os serviços de consumo com a API da folha de preços no formato CSV:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para ver os preços estimados para os serviços de consumo com a API da folha de preços no formato JSON:

| Método | URI de pedido |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

São as APIs de folha de cálculo do preço de contrato do Microsoft Customer *APIs de REST assíncronas*. As respostas para as APIs mudou das APIs síncronas mais antigas. O corpo da resposta da API também foi alterado.

#### <a name="old-response-body"></a>Corpo da resposta antigo

Eis um exemplo da resposta de REST API síncrona:

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

#### <a name="new-response-body"></a>Novo corpo da resposta

As APIs suportam os [assíncrona de REST do Azure](../azure-resource-manager/resource-manager-async-operations.md) formato. Chamar a API de utilização do GET e receber a seguinte resposta:

```
No Response Body

HTTP Status 202 Accepted
```

Os seguintes cabeçalhos são enviados com a localização de saída:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Tornar GET outra chamada para a localização. A resposta para a chamada GET é o mesmo até a operação de atinge um Estado de conclusão ou falha. Quando concluída, a resposta para o local de chamada GET devolve o URL de transferência. Como se a operação foi executada ao mesmo tempo. Segue-se um exemplo:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

O cliente também pode efetuar uma chamada GET para o `Azure-AsyncOperation`. O ponto final devolve o estado para a operação.

A tabela seguinte mostra os campos na API de folha de preços obter empresarial mais antiga. Ele inclui campos correspondentes na tabela de preços novos contratos de cliente da Microsoft:

| Propriedade antiga | Nova propriedade | Notas |
| --- | --- | --- |
| billingPeriodId  | _Não aplicável_ | Não aplicável. Para contratos de cliente da Microsoft, a nota fiscal e a folha de preços associadas substituíram o conceito de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Valores de cadeia exacta poderão diferir. |
| includedQuantity  | includedQuantity | Não é aplicável para serviços em contratos de cliente da Microsoft. |
| partNumber  | _Não aplicável_ | Em alternativa, utilize uma combinação de productOrderName (mesmo que offerId) e meterid. |
| unitPrice  | unitPrice | Preço unitário é aplicável serviços consumidos contratos de cliente da Microsoft. |
| currencyCode  | pricingCurrency | Contratos de cliente da Microsoft têm representações de preços na moeda do preço e moeda de faturação. O currencyCode corresponde a pricingCurrency em contratos de cliente da Microsoft. |
| offerId | productOrderName | Em vez de OfferId, pode usar productOrderName, mas não é igual a OfferId. No entanto, productOrderName e medidor determinam um preço em contratos de cliente da Microsoft relacionado com meterId e Offerid no inscrições herdadas. |

## <a name="consumption-price-sheet-api-operations"></a>Operações de API da folha de preços de consumo

Para Enterprise contratos, utilizou a API de folha de preços de consumo [Obtenha](/rest/api/consumption/pricesheet/get) e [obter por período de faturação](/rest/api/consumption/pricesheet/getbybillingperiod) operações para um âmbito ao subscriptionId ou de um período de faturação. A API utiliza a autenticação de gestão de recursos do Azure.

Para obter as informações de folha de preços para um âmbito com a API da folha de preços:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para obter informações de folha de preços, período com a API da folha de preços de faturação:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Em vez dos pontos de extremidade de API acima, utilize as seguintes para contratos de cliente da Microsoft:

**API de folha de preços para contratos de cliente da Microsoft (API de REST assíncrona)**

Esta API está para contratos de cliente da Microsoft e fornece atributos adicionais.

**Folha de preços para um âmbito de perfil de faturação numa conta de cobrança**

Esta API é a API existente. Ele foi atualizado para fornecer a folha de preços para um perfil de faturação numa conta de cobrança.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Folha de preços para um âmbito por conta de faturação

Autenticação do Azure Resource Manager é usada quando obtém a folha de preços no âmbito da inscrição numa conta de faturação.

Para obter a folha de preços a conta de inscrição numa conta de faturação:

| Método | URI de pedido |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Para um contrato de cliente da Microsoft, utilize as informações na secção seguinte. Ele fornece as propriedades de campo utilizadas para contratos do Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Folha de preços para um âmbito de perfil de faturação numa conta de cobrança

A folha de preços atualizadas por conta de cobrança API obtém a folha de preços no formato CSV. Para obter a folha de preços no âmbito do perfil da faturação para um MCA:

| Método | URI de pedido |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

No âmbito de inscrição de âmbito do EA, a resposta da API e as propriedades são idênticas. As propriedades correspondem às mesmas propriedades MCA.

As propriedades mais antigas [APIs de folha de cálculo do preço do Azure Resource Manager](/rest/api/consumption/pricesheet) , estando as mesmas propriedades de novo na tabela seguinte.

| Antigo do Azure Resource Manager preço folha propriedade API  | Nova propriedade de API de folha de preços do Microsoft cliente contrato   | Descrição |
| --- | --- | --- |
| ID do Medidor | _meterId_ | Identificador exclusivo para o medidor. Mesmo que meterId. |
| Nome do medidor | meterName | Nome do medidor. Medidor representa o recurso implementável do serviço do Azure. |
| Categoria do medidor  | serviço | Nome da categoria de classificação para o medidor. Mesmo que o serviço na folha de preços de contrato de cliente do Microsoft. Valores de cadeia exacta diferentes. |
| Subcategoria do medidor | meterSubCategory | Nome da categoria de subclassification medidor. Com base na classificação de diferenciação entre do conjunto de funcionalidades de alto nível no serviço. Por exemplo, básico SQL DB vs BD do SQL Standard. |
| Região do medidor | meterRegion | &nbsp;  |
| Unidade | _Não aplicável_ | Pode ser analisado a partir unitOfMeasure. |
| Unidade de medida | unitOfMeasure | &nbsp;  |
| Número de peça | _Não aplicável_ | Em vez de partNumber, use productOrderName e MeterId para identificar exclusivamente o preço de um perfil de faturação. Campos estão listados da nota fiscal MCA em vez de partNumber no MCA faturas. |
| Preço unitário | unitPrice | Preço unitário de contrato de cliente da Microsoft. |
| Código de moeda | pricingCurrency | Contratos de cliente da Microsoft representam os preços na moeda de preços e moeda de faturação. Código de moeda é o mesmo que o pricingCurrency em contratos de cliente da Microsoft. |
| Quantidade incluída | includedQuantity | Não é aplicável aos serviços em contratos de cliente da Microsoft. Mostrar com valores de zero. |
|  ID da Oferta  | productOrderName | Em vez de OfferId, use productOrderName. Não são as mesmas como OfferId, no entanto o medidor e productOrderName determinam um preço em contratos de cliente da Microsoft. Relacionado com meterId e Offerid no inscrições herdadas. |

O preço de contratos de cliente da Microsoft é definido diferente contratos Enterprise. O preço para os serviços na inscrição empresarial é exclusivo para o produto, PartNumber, medidor e oferta. O PartNumber não utilizado numa contratos de cliente da Microsoft.

O preço do serviço de consumo do Azure que faz parte de um contrato de cliente da Microsoft é exclusivo para productOrderName e meterId. Eles representam o medidor de serviço e o plano de produto.

Para reconciliar entre a folha de preços e a utilização da API de detalhes de utilização, pode usar o productOrderName e meterId.

Os utilizadores com faturação proprietário, Contribuidor, leitor de perfil e direitos de Gestor de nota fiscal podem transferir a folha de preços.

A folha de preços inclui os preços para serviços cujo preço baseia-se na utilização. Os serviços incluem o consumo do Azure e o consumo do Marketplace. O preço mais recente no final de cada período de serviço é bloqueado e é aplicado a utilização de um ponto de serviço único. Para os serviços de consumo do Azure, o período de serviço é normalmente um mês de calendário.

### <a name="retired-price-sheet-api-fields"></a>Campos de API da folha de preços extintos

Os campos seguintes não são disponíveis no contrato de preço folha APIs da Microsoft dos clientes ou ter os mesmos campos.

|Campo extinto| Descrição|
|---|---|
| billingPeriodId | Não aplicável. Corresponde a InvoiceId para MCA. |
| offerId | Não aplicável. Corresponde a productOrderName no MCA. |
| meterCategory  | Não aplicável. Corresponde ao serviço no MCA. |
| unidade | Não aplicável. Pode ser analisado a partir unitOfMeasure. |
| currencyCode | Mesmo que o pricingCurrency no MCA. |
| meterLocation | Mesmo que o meterRegion no MCA. |
| partNumber partnumber | Não aplicável porque o número de peça não estiver listado no MCA faturas. Em vez de partnumber, utilize a combinação de meterId e productOrderName para identificar exclusivamente os preços. |
| totalIncludedQuantity | Não aplicável. |
| pretaxStandardRate  | Não aplicável. |

## <a name="reservation-instance-charge-api-replaced"></a>API de custo de instância de reserva substituído

Pode obter a faturação transações para compras de reserva com o [API de custo de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). A nova API inclui todas as compras, incluindo ofertas do Marketplace de terceiros. Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). A API de custo de instância reservada é substituída pela API de transações.

Para obter a reserva de transações de compra com a API de transações:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Recomendações de APIs substituídas

APIs de recomendações de compra de instância reservada fornecem a utilização da máquina virtual ao longo dos últimos 7, 30 ou 60 dias. As APIs também fornecem recomendações de compra de reserva. Eles incluem:

- [Partilhado a API de recomendações de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API de recomendações de instância reservada único](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). As recomendações de reserva APIs listadas anteriormente são substituídas pelos [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Para obter recomendações de reserva com a API de recomendações de reserva:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>APIs de utilização de reserva substituído

Pode obter a utilização de reserva de uma inscrição com a API de utilização de instância reservada. Se existir mais do que uma instância reservada numa inscrição, também pode obter a utilização de todas as compras da instância reservada utilizarem esta API.

Eles incluem:

- [Detalhes de utilização de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Resumo de utilização de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary)

Todas as APIs de consumo são substituídas pelos APIs nativas do Azure que utilize o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar APIs de REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). As recomendações de reserva APIs listadas anteriormente são substituídas pelos [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) APIs.

Para obter detalhes de reserva com a API de detalhes de reserva:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Para obter os resumos de reserva com a API de resumos de reserva:

| Método | URI de pedido |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Mover da Cloudyn para gestão de custos

As organizações que usam [Cloudyn](https://cloudyn.com) deve começar a utilizar [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) para quaisquer necessidades de gestão de custos. Gestão de custos está disponível no portal do Azure com nenhuma integração e uma latência de oito horas. Para obter mais informações, consulte a [documentação de gestão de custos](index.yml).

Com o Azure Cost Management, pode:

- Ver os custos ao longo do tempo em relação a um orçamento predefinido. Analise, diariamente, padrões de custo para identificar e parar a anomalias de gastos. Divida os custos por etiquetas, o grupo de recursos, o serviço e localização.
- Crie o orçamento para definir limites de utilização e os custos e ser notificado quando importantes limites são aproximados. Configure a automação com grupos de ação para acionar eventos personalizados e impor limites fixos em seus próprios termos.
- Otimize o custo e a utilização com as recomendações do Assistente do Azure. Descubra otimizações de compra com as reservas, porém subutilizadas máquinas de virtuais de atividade e eliminar recursos não utilizados para se manterem dentro orçamentos.
- Agende uma exportação de dados de utilização e custos para publicar um ficheiro CSV para a sua conta de armazenamento diariamente. Automatize a integração com sistemas externos para manter os dados de faturação em sincronia e atualizados.

## <a name="power-bi-integration"></a>Integração do Power BI

Se utilizar o Power BI para o relatório de custo, terá de efetuar a transição para o seguinte:

- Microsoft Azure consumo Insights aplicação Power BI
- Conector de área de trabalho de informações sobre consumo do Azure


O conector é recomendado para organizações que pretendem a maior flexibilidade. No entanto, a aplicação Power BI também está disponível para configuração rápida.

- Instalar o [informações sobre consumo do Microsoft Azure do Power BI aplicação](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Ligar com o conector Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

O pacote de conteúdos de informações sobre consumo e o conector mais antigos trabalharam num nível de inscrição. É necessária, pelo menos, a leitura de acesso. A nova aplicação de consumo Insights Power BI e o novo conector Azure Consumption Insights estão disponíveis para os utilizadores de perfil faturação. As equipes que precisam de opções adicionais para rever os custos de ou para ver os custos através de perfis de faturação devem utilizar no [análise de custo](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) o portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Leitura a [documentação de gestão de custos](index.yml) para aprender a monitorizar e controlar os gastos do Azure. Em alternativa, se quiser otimizar a utilização de recursos com o Cost Management.
