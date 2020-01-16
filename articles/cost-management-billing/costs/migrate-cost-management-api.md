---
title: Migrar EA para APIs de contrato do cliente da Microsoft-Azure
description: Este artigo ajuda você a entender as consequências da migração de um Microsoft Enterprise Agreement (EA) para um contrato com o cliente da Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0dfb7245135097780f027185e4037b6cafbae690
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989361"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migre do Enterprise Agreement para as APIs do contrato do cliente da Microsoft

Este artigo ajuda você a entender a estrutura de dados, a API e outras diferenças de integração do sistema entre Enterprise Agreement (EA) e contas do Microsoft Customer Agreement (MCA). O gerenciamento de custos do Azure dá suporte a APIs para ambos os tipos de conta. Examine o artigo [conta de cobrança de instalação do contrato do](../manage/mca-setup-account.md) cliente Microsoft antes de continuar.

As organizações com uma conta de EA existente devem examinar este artigo em conjunto com a configuração de uma conta MCA. Anteriormente, renovar uma conta de EA exigia um trabalho mínimo para mudar de um registro antigo para um novo. No entanto, migrar para uma conta MCA requer esforço adicional. O esforço adicional é devido às alterações no subsistema de cobrança subjacente, que afetam todas as APIs e ofertas de serviço relacionadas ao custo.

## <a name="mca-apis-and-integration"></a>APIs e integração de MCA

APIs MCA e nova integração permitem que você:

- Ter disponibilidade de API completa por meio de APIs nativas do Azure.
- Configure várias notas fiscais em uma única conta de cobrança.
- Acesse uma API combinada com uso de serviço do Azure, uso de Marketplace de terceiros e compras do Marketplace.
- Exiba os custos entre os perfis de cobrança (o mesmo que os registros) usando o gerenciamento de custos do Azure.
- Acesse novas APIs para mostrar os custos, seja notificado quando os custos excederem os limites predefinidos e exporte dados brutos automaticamente.

## <a name="migration-checklist"></a>Lista de verificação de migração

Os itens a seguir ajudam você a fazer a transição para APIs MCA.

- Familiarize-se com a nova [conta de cobrança do Microsoft Customer Agreement](../understand/mca-overview.md).
- Determine quais APIs você usa e veja quais delas são substituídas na seção a seguir.
- Familiarize-se com [Azure Resource Manager APIs REST](/rest/api/azure).
- Se você ainda não estiver usando APIs Azure Resource Manager, [Registre seu aplicativo cliente com o Azure ad](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Atualize qualquer código de programação para [usar a autenticação do Azure ad](/rest/api/azure/#create-the-request).
- Atualize qualquer código de programação para substituir chamadas de API EA por chamadas de API MCA.
- Atualize o tratamento de erros para usar novos códigos de erro.
- Examine as ofertas de integração adicionais, como Cloudyn e Power BI, para outras ações necessárias.

## <a name="ea-apis-replaced-with-mca-apis"></a>APIs de EA substituídas por APIs MCA

As APIs de EA usam uma chave de API para autenticação e autorização. APIs MCA usam a autenticação do Azure AD.

| Finalidade | API DE EA | API MCA |
| --- | --- | --- |
| Saldo e créditos | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Uso (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso do Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Períodos de cobrança | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Folha de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. billing/billingAccounts/billingProfiles/pricesheet/default/download Format = JSON|CSV Microsoft. billing/billingAccounts/.../billingProfiles/.../faturas/... formato/pricesheet/default/download = JSON|CSV Microsoft. billing/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Compras de Reservas | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Recomendações de reserva | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Uso de reserva | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> o serviço do Azure e o uso do Marketplace de terceiros estão disponíveis com a [API de detalhes de uso](/rest/api/consumption/usagedetails).

As seguintes APIs estão disponíveis para contas de cobrança de MCA:

| Finalidade | API do Microsoft Customer Agreement (MCA) |
| --- | --- |
| Contas de cobrança<sup>2</sup> | Microsoft. billing/billingAccounts |
| Perfis de cobrança<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Seções de fatura<sup>2</sup> | Microsoft. billing/billingAccounts/invoiceSections |
| Faturas | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Assinaturas de cobrança | {scope}/billingSubscriptions |

<sup>2</sup> as APIs retornam listas de objetos, que são escopos, em que as experiências de gerenciamento de custos no portal do Azure e as APIs operam. Para obter mais informações sobre escopos de gerenciamento de custos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

Se você usar qualquer API EA existente, precisará atualizá-las para dar suporte a contas de cobrança MCA. A tabela a seguir mostra outras alterações de integração:

| Finalidade | Oferta antiga | Nova oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gestão de Custos do Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pacote de conteúdo e conector [do Microsoft consumo insights](/power-bi/desktop-connect-azure-consumption-insights) | [Microsoft Azure Consumption Insights Power bi o aplicativo](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) e o [conector do Azure consumption insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs para obter saldo e créditos

A API de [Resumo de obtenção de saldo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornece um resumo mensal de:

- Saldos
- Novas compras
- Encargos de serviço do Azure Marketplace
- Ajustes
- Encargos excedentes de serviço

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

A API de Resumo de obtenção de saldo é substituída pela API Microsoft. billing/billingAccounts/billingProfiles/availableBalance.

Para obter os saldos disponíveis com a API de saldo disponível:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs para obter custo e uso

Obtenha uma análise diária dos custos do uso do serviço do Azure, do uso do Marketplace de terceiros e de outras compras do Marketplace com as seguintes APIs. As APIs separadas a seguir foram mescladas para os serviços do Azure e o uso do Marketplace de terceiros. As APIs antigas são substituídas pela API [Microsoft. consumo/usageDetails](/rest/api/consumption/usagedetails) . Ele adiciona compras do Marketplace, que anteriormente só eram mostradas no Resumo de saldo até a data.

- [Obter detalhes de uso/baixar](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/enviar](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter detalhes de uso/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter encargo de loja do Marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Obter encargo de loja do Marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

Todas as APIs anteriores são substituídas pela API de detalhes de consumo/uso.

Para obter detalhes de uso com a API de detalhes de uso:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A API de detalhes de uso, assim como com todas as APIs de gerenciamento de custo, está disponível em vários escopos. Para custos faturados, como você normalmente receberia em um nível de registro, use o escopo do perfil de cobrança.  Para obter mais informações sobre escopos de gerenciamento de custos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

| Tipo | Formato da ID |
| --- | --- |
| Conta de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Perfil de faturação | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscrição | `/subscriptions/{subscriptionId}` |
| Grupo de recursos | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Use os parâmetros de QueryString a seguir para atualizar qualquer código de programação.

| Parâmetros antigos | Novos parâmetros |
| --- | --- |
| `billingPeriod={billingPeriod}` | Não suportado |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

O corpo da resposta também foi alterado.

Corpo da resposta antiga:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Novo corpo de resposta:

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

O nome da propriedade que contém a matriz de registros de uso alterado de dados para _valores_. Cada registro costumava ter uma lista simples de propriedades detalhadas. No entanto, cada registro agora todos os detalhes agora estão em uma propriedade aninhada denominada _Propriedades_, com exceção das marcas. A nova estrutura é consistente com outras APIs do Azure. Alguns nomes de propriedade foram alterados. A tabela a seguir mostra as propriedades correspondentes.

| Propriedade antiga | Nova Propriedade | Notas |
| --- | --- | --- |
| AccountId | N/A | O criador da assinatura não é acompanhado. Use invoiceSectionId (igual a DepartmentID). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/A | O criador da assinatura não é acompanhado. Use invoiceSectionName (igual a departmentname). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observe que essas propriedades são opostas. Se isAzureCreditEnabled for true, ChargesBilledSeparately será false. |
| ConsumedQuantity | quantidade | &nbsp; |
| ConsumedService | consumedService | Valores de cadeia de caracteres exatos podem ser diferentes. |
| ConsumedServiceId | Nenhuma | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Data e usageStartDate | date | &nbsp;  |
| Dia | Nenhuma | Analisa o dia a partir da data. |
| DepartmentId | invoiceSectionId | Valores exatos são diferentes. |
| DepartmentName | invoiceSectionName | Valores de cadeia de caracteres exatos podem ser diferentes. Configure as seções da fatura para corresponder aos departamentos, se necessário. |
| ExtendedCost e custo | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| É encargo recorrente | Nenhuma | &nbsp;  |
| Localização | localização | &nbsp;  |
| MeterCategory | meterCategory | Valores de cadeia de caracteres exatos podem ser diferentes. |
| MeterId | meterId | Os valores de cadeia de caracteres exatos são diferentes. |
| MeterName | meterName | Valores de cadeia de caracteres exatos podem ser diferentes. |
| MeterRegion | meterRegion | Valores de cadeia de caracteres exatos podem ser diferentes. |
| MeterSubCategory | meterSubCategory | Valores de cadeia de caracteres exatos podem ser diferentes. |
| Mês | Nenhuma | Analisa o mês a partir da data. |
| Nome da Oferta | Nenhuma | Use PublisherName e productOrderName. |
| offerID | Nenhuma | &nbsp;  |
| Número da Encomenda | Nenhuma | &nbsp;  |
| PartNumber | Nenhuma | Use o medidorid e o productOrderName para identificar os preços com exclusividade. |
| Nome do Plano | productOrderName | &nbsp;  |
| Produto | Produto |   |
| ProductId | productId | Os valores de cadeia de caracteres exatos são diferentes. |
| Nome do Editor | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Os valores de cadeia de caracteres exatos são diferentes. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Nenhuma | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| serviceName | meterCategory | Valores de cadeia de caracteres exatos podem ser diferentes. |
| Da camada | meterSubCategory | Valores de cadeia de caracteres exatos podem ser diferentes. |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Etiquetas | etiquetas | A propriedade Tags aplica-se ao objeto raiz, não à propriedade de propriedades aninhadas. |
| UnitOfMeasure | unitOfMeasure | Os valores de cadeia de caracteres exatos são diferentes. |
| usageEndDate | date | &nbsp;  |
| Ano | Nenhuma | Analisa o ano a partir da data. |
| (novo) | billingCurrency | Moeda usada para a cobrança. |
| (novo) | billingProfileId | ID exclusiva do perfil de cobrança (o mesmo que o registro). |
| (novo) | billingProfileName | Nome do perfil de cobrança (o mesmo que o registro). |
| (novo) | chargeType | Use para diferenciar o uso do serviço do Azure, o uso do Marketplace e as compras. |
| (novo) | invoiceId | ID exclusiva para a nota fiscal. Vazio para o mês atual, aberto. |
| (novo) | publisherType | Tipo de Publicador para compras. Vazio para uso. |
| (novo) | serviceFamily | Tipo de compra. Vazio para uso. |
| (novo) | servicePeriodEndDate | Data de término do serviço comprado. |
| (novo) | servicePeriodStartDate | Data de início do serviço comprado. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API de períodos de cobrança substituído por API de notas fiscais

As contas de cobrança MCA não usam períodos de cobrança. Em vez disso, eles usam faturas para delimitar os custos para períodos de cobrança específicos. A [API de períodos de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) é substituída pela API de notas fiscais. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

Para obter faturas com a API de notas fiscais:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>APIs de folha de preços

Esta seção aborda as APIs de folha de preços existentes e fornece recomendações para migrar para a API da folha de preços para contratos de clientes da Microsoft. Ele também aborda a API de folha de preços para contratos de clientes da Microsoft e explica os campos nas folhas de preços. As APIs [Enterprise Get datasheet sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Enterprise Get de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) são substituídas pela API da folha de preços para contratos de clientes da Microsoft (Microsoft. billing/billingAccounts/billingProfiles/pricesheet). A nova API dá suporte aos formatos JSON e CSV, em formatos REST assíncronos. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Cobrança de APIs empresariais

Você usou as APIs empresariais de cobrança com registros empresariais para obter informações sobre o preço e o período de cobrança. Autenticação e autorização usados Azure Active Directory tokens da Web.

Para obter os preços aplicáveis para o registro empresarial especificado com a folha de preços e as APIs de período de cobrança:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API de folha de preços para contratos de clientes da Microsoft

Use a API de folha de preços para contratos de clientes da Microsoft para exibir preços para todos os serviços de consumo do Azure e consumo do Marketplace. Os preços mostrados para o perfil de cobrança se aplicam a todas as assinaturas que pertencem ao perfil de cobrança.

Use a API da folha de preços para exibir os dados da folha de preços dos serviços de consumo do Azure no formato CSV:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Use a API da folha de preços para exibir todos os dados da folha de preços dos serviços de consumo do Azure no formato JSON:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Usar a API retorna a folha de preços da conta inteira. No entanto, você também pode obter uma versão condensada da folha de preços no formato PDF. O resumo inclui o consumo do Azure e os serviços de consumo do Marketplace que são cobrados por uma fatura específica. A nota fiscal é identificada pelo {faturaid}, que é o mesmo que o **número da nota fiscal** mostrado nos arquivos PDF de resumo da nota fiscal. Veja a seguir um exemplo.

![Imagem de exemplo mostrando o número da nota fiscal que corresponde à faturaid](./media/migrate-cost-management-api/invoicesummary.png)

Para exibir informações de fatura com a API de folha de preços no formato CSV:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir informações de fatura com a API de folha de preços no formato JSON:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Você também pode ver os preços estimados para qualquer serviço de consumo do Marketplace ou consumo do Azure no período de serviço ou ciclo de cobrança aberto atual.

Para exibir os preços estimados dos serviços de consumo com a API de folha de preços no formato CSV:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir os preços estimados dos serviços de consumo com a API de folha de preços no formato JSON:

| Método | URI de solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

As APIs de folha de preços do contrato do cliente da Microsoft são *APIs REST assíncronas*. As respostas para as APIs foram alteradas das APIs síncronas mais antigas. O corpo da resposta da API também foi alterado.

#### <a name="old-response-body"></a>Corpo da resposta antiga

Aqui está um exemplo da resposta síncrona da API REST:

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

#### <a name="new-response-body"></a>Novo corpo de resposta

As APIs dão suporte ao formato [assíncrono do REST do Azure](../../azure-resource-manager/management/async-operations.md) . Chame a API usando GET e você receberá a seguinte resposta:

```
No Response Body

HTTP Status 202 Accepted
```

Os cabeçalhos a seguir são enviados com o local da saída:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Faça outra chamada GET para o local. A resposta para a chamada GET é a mesma até que a operação atinja um estado de conclusão ou de falha. Quando concluído, a resposta para o local de chamada GET retorna a URL de download. Assim como se a operação foi executada ao mesmo tempo. Segue-se um exemplo:

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

O cliente também pode fazer uma chamada GET para o `Azure-AsyncOperation`. O ponto de extremidade retorna o status da operação.

A tabela a seguir mostra os campos da API de folha de preços do Enterprise Get mais antiga. Ele inclui campos correspondentes na nova folha de preços para contratos de clientes da Microsoft:

| Propriedade antiga | Nova Propriedade | Notas |
| --- | --- | --- |
| billingPeriodId  | _Não aplicável_ | Não aplicável. Para contratos de clientes da Microsoft, a fatura e a folha de preços associada substituíram o conceito de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Valores de cadeia de caracteres exatos podem ser diferentes. |
| includedQuantity  | includedQuantity | Não aplicável para serviços em contratos de clientes da Microsoft. |
| partNumber  | _Não aplicável_ | Em vez disso, use uma combinação de productOrderName (igual a OfferId) e medidorid. |
| unitPrice  | unitPrice | O preço unitário é aplicável aos serviços consumidos nos contratos de clientes da Microsoft. |
| currencyCode  | pricingCurrency | Os contratos de clientes da Microsoft têm representações de preço na moeda de preços e na moeda de cobrança. O currencyCode corresponde ao pricingCurrency nos contratos de clientes da Microsoft. |
| offerID | productOrderName | Em vez de OfferId, você pode usar productOrderName, mas não é o mesmo que OfferId. No entanto, o productOrderName e o medidor determinam os preços dos contratos de clientes da Microsoft relacionados a medidorid e OfferId em registros herdados. |

## <a name="consumption-price-sheet-api-operations"></a>Operações de API da folha de preços de consumo

Para Enterprise Agreements, você usou a API da folha de preços de consumo [obter](/rest/api/consumption/pricesheet/get) e [obter por](/rest/api/consumption/pricesheet/getbybillingperiod) operações do período de cobrança para um escopo por SubscriptionId ou um período de cobrança. A API usa a autenticação de gerenciamento de recursos do Azure.

Para obter as informações da folha de preços para um escopo com a API da folha de preços:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para obter informações de folha de preços por período de cobrança com a API da folha de preços:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Em vez dos pontos de extremidade de API acima, use os seguintes para contratos de clientes da Microsoft:

**API de folha de preços para contratos de clientes da Microsoft (API REST assíncrona)**

Essa API é para contratos de clientes da Microsoft e fornece atributos adicionais.

**Folha de preços para um escopo de perfil de cobrança em uma conta de cobrança**

Essa API é a API existente. Ele foi atualizado para fornecer a folha de preços de um perfil de cobrança em uma conta de cobrança.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Folha de preços para um escopo por conta de cobrança

Azure Resource Manager autenticação é usada quando você obtém a folha de preços no escopo de registro em uma conta de cobrança.

Para obter a folha de preços na conta de registro em uma conta de cobrança:

| Método | URI de solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Para um contrato de cliente da Microsoft, use as informações na seção a seguir. Ele fornece as propriedades de campo usadas para contratos de clientes da Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Folha de preços para um escopo de perfil de cobrança em uma conta de cobrança

A tabela de preços atualizada por API da conta de cobrança Obtém a folha de preços no formato CSV. Para obter a folha de preços no escopo do perfil de cobrança para um MCA:

| Método | URI de solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

No escopo de registro do EA, a resposta da API e as propriedades são idênticas. As propriedades correspondem às mesmas propriedades de MCA.

As propriedades mais antigas para [Azure Resource Manager as APIs de folha de preços](/rest/api/consumption/pricesheet) e as mesmas novas propriedades estão na tabela a seguir.

| Propriedade de API da folha de preços Azure Resource Manager antiga  | Nova propriedade de API da folha de preços do contrato do cliente Microsoft   | Descrição |
| --- | --- | --- |
| ID do Medidor | _meterId_ | Identificador exclusivo do medidor. Mesmo que medidorid. |
| Nome do medidor | meterName | Nome do medidor. O medidor representa o recurso implantável do serviço do Azure. |
| Categoria do medidor  | serviço | Nome da categoria de classificação do medidor. O mesmo que o serviço na folha de preços do contrato do cliente da Microsoft. Os valores de cadeia de caracteres exatos são diferentes. |
| Subcategoria do medidor | meterSubCategory | Nome da categoria de subclassificação do medidor. Com base na classificação de diferenciação do conjunto de recursos de alto nível no serviço. Por exemplo, banco de BD SQL básico versus BD SQL padrão. |
| Região do medidor | meterRegion | &nbsp;  |
| Unidade | _Não aplicável_ | Pode ser analisado em medida. |
| Unidade de medida | unitOfMeasure | &nbsp;  |
| Número da peça | _Não aplicável_ | Em vez de número de peça, use productOrderName e Medidorid para identificar exclusivamente o preço de um perfil de cobrança. Os campos são listados na nota fiscal MCA em vez do número da peça em notas fiscais MCA. |
| Preço unitário | unitPrice | Preço unitário do contrato do cliente da Microsoft. |
| Código de moeda | pricingCurrency | Os contratos de clientes da Microsoft representam preços na moeda de preços e na moeda de cobrança. O código de moeda é o mesmo que o pricingCurrency nos contratos de clientes da Microsoft. |
| Quantidade incluída | includedQuantity | Não aplicável a serviços em contratos de clientes da Microsoft. Mostrar com valores de zero. |
|  ID da oferta  | productOrderName | Em vez de OfferId, use productOrderName. Não é o mesmo que OfferId, no entanto, o productOrderName e o medidor determinam os preços dos contratos do cliente da Microsoft. Relacionado a medidorid e OfferId em registros herdados. |

O preço dos contratos de clientes da Microsoft é definido de maneira diferente dos contratos Enterprise. O preço dos serviços no registro Enterprise é exclusivo para o produto, o número de peça, o medidor e a oferta. O número da peça não é usado em contratos de clientes da Microsoft.

O preço do serviço de consumo do Azure que faz parte de um contrato de cliente da Microsoft é exclusivo para productOrderName e medidorid. Eles representam o medidor de serviço e o plano de produto.

Para reconciliar entre a planilha de preços e o uso na API de detalhes de uso, você pode usar o productOrderName e o meterid.

Os usuários que têm direitos de proprietário do perfil de cobrança, colaborador, leitor e Gerenciador de faturas podem baixar a folha de preços.

A folha de preços inclui preços para serviços cujo preço é baseado no uso. Os serviços incluem consumo do Azure e consumo do Marketplace. O preço mais recente no final de cada período de serviço é bloqueado e aplicado ao uso em um único período de serviço. Para os serviços de consumo do Azure, o período de serviço é geralmente um mês civil.

### <a name="retired-price-sheet-api-fields"></a>Campos de API de folha de preços desativados

Os campos a seguir não estão disponíveis nas APIs de folha de preços do contrato do cliente da Microsoft ou têm os mesmos campos.

|Campo desativado| Descrição|
|---|---|
| billingPeriodId | Não aplicável. Corresponde a faturaid para MCA. |
| offerID | Não aplicável. Corresponde a productOrderName em MCA. |
| meterCategory  | Não aplicável. Corresponde ao serviço em MCA. |
| unidade | Não aplicável. Pode ser analisado em medida. |
| currencyCode | O mesmo que o pricingCurrency em MCA. |
| meterLocation | O mesmo que o meterRegion em MCA. |
| partNumber PARTNUMBER | Não aplicável porque o número de peça não está listado em notas fiscais de MCA. Em vez de número de peça, use a combinação de medidorid e productOrderName para identificar os preços com exclusividade. |
| totalIncludedQuantity | Não aplicável. |
| pretaxStandardRate  | Não aplicável. |

## <a name="reservation-instance-charge-api-replaced"></a>API de cobrança de instância de reserva substituída

Você pode obter transações de cobrança para compras de reserva com a [API de cobrança de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). A nova API inclui todas as compras, incluindo ofertas do Marketplace de terceiros. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). A API de cobrança de instância reservada é substituída pela API de transações.

Para obter transações de compra de reserva com a API de transações:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>APIs de recomendações substituídas

As APIs de recomendações de compra de instância reservadas fornecem o uso da máquina virtual nos últimos 7, 30 ou 60 dias. As APIs também fornecem recomendações de compra de reserva. Os relatórios incluem:

- [API de recomendação de instância reservada compartilhada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API de recomendações de instância reservada única](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). As APIs de recomendações de reserva listadas anteriormente são substituídas pela API [Microsoft. consumo/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) .

Para obter recomendações de reserva com a API de recomendações de reserva:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>APIs de uso de reserva substituídas

Você pode obter o uso de reserva em um registro com a API de Uso da Instância Reservada. Se houver mais de uma instância reservada em um registro, você também poderá obter o uso de todas as compras de instância reservada usando essa API.

Os relatórios incluem:

- [Detalhes do Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Resumo de Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [introdução ao REST](/rest/api/azure/#create-the-request). As APIs de recomendações de reserva listadas anteriormente são substituídas pelas APIs [Microsoft. consumo/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft. consumo/reservationSummaries](/rest/api/consumption/reservationssummaries) .

Para obter detalhes de reserva com a API de detalhes de reserva:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Para obter resumos de reserva com a API de resumos de reserva:

| Método | URI de solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Mover do Cloudyn para o gerenciamento de custos

As organizações que usam o [Cloudyn](https://cloudyn.com) devem começar a usar o [Gerenciamento de custos do Azure](https://azure.microsoft.com/services/cost-management/) para qualquer necessidade de gerenciamento de custos. O gerenciamento de custos está disponível no portal do Azure sem integração e uma latência de oito horas. Para obter mais informações, consulte a [documentação de gerenciamento de custos](../index.yml).

Com o Azure Cost Management, pode:

- Exibir custos ao longo do tempo em um orçamento predefinido. Analisar padrões de custos diários para identificar e parar os gastos anómalos. Divida os custos por marcas, grupo de recursos, serviço e local.
- Crie orçamentos para definir limites de uso e custos e seja notificado quando forem abordados limites importantes. Configurar a automatização com grupos de ações para acionar eventos personalizados e aplicar limites duros segundo as suas condições.
- Otimize o custo e o uso com recomendações do Azure Advisor. Descubra otimizações de compra com reservas, máquinas virtuais diminuir subutilizadas e exclua recursos não utilizados para permanecer dentro dos orçamentos.
- Agende uma exportação de dados de uso e custo para publicar um arquivo CSV em sua conta de armazenamento diariamente. Automatize a integração com sistemas externos para manter os dados de cobrança sincronizados e atualizados.

## <a name="power-bi-integration"></a>Integração do Power BI

Você também pode usar Power BI para relatórios de custo. O [conector de gerenciamento de custos do Azure](/power-bi/desktop-connect-azure-cost-management) para Power bi desktop pode ser usado para criar relatórios personalizados e avançados que ajudam você a entender melhor seus gastos com o Azure. Atualmente, o conector de gerenciamento de custos do Azure dá suporte a clientes com um contrato de cliente da Microsoft ou um EA (Enterprise Agreement).

## <a name="next-steps"></a>Passos seguintes

- Leia a [documentação do gerenciamento de custos](../index.yml) para saber como monitorar e controlar os gastos do Azure. Ou, se você quiser otimizar o uso de recursos com o gerenciamento de custos.
