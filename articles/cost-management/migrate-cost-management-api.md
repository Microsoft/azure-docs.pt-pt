---
title: Migrar do contrato Enterprise para APIs de contrato de cliente da Microsoft – Azure | Documentos da Microsoft
description: Este artigo ajuda-o a compreender o impacto às APIs, ao migrar de um Microsoft Enterprise Agreement (EA) para um contrato de cliente da Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: a12360e60026fd4251cc232caccd1bc52a34d2ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344880"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrar do contrato Enterprise para APIs de contrato de cliente da Microsoft

Este artigo ajuda-o a compreender o impacto às APIs, ao migrar de um Microsoft Enterprise Agreement (EA) para um contrato de cliente da Microsoft. As seguintes novas APIs são utilizadas para ver os custos e preços:

- Folha de preços API para contratos com o cliente da Microsoft
  - Folha de preços por fatura
  - Folha de preços por perfil de faturação
  - Folha de preços para o período de faturação aberto
- Detalhes de utilização API de contratos com o cliente da Microsoft
  - Detalhes de utilização por fatura para o perfil de faturação (filtro pela secção de nota fiscal)
    - Detalhes de utilização por data de utilização
    - Detalhes de utilização para a conta de faturação
    - Detalhes de utilização para o perfil de faturação
    - Detalhes de utilização para a secção de nota fiscal
    - Detalhes de utilização da subscrição
  - Detalhes de utilização para o mês até à data
    - Detalhes de utilização para a conta de faturação
    - Detalhes de utilização para o perfil de faturação
    - Detalhes de utilização para a secção de nota fiscal
    - Detalhes de utilização da subscrição
  - Detalhes de utilização por período de faturação
- Créditos de API para o contrato de cliente da Microsoft
  - API resumo do saldo atual pelo perfil de faturação
  - Eventos de API por perfil de faturação de crédito
  - Muitos API pelo perfil de faturação de crédito
- API de Consulta
- Custos de API
  - Custos de API por conta de faturação
  - Encargos de API por perfil de faturação
  - Custos de API pela secção de nota fiscal
- API de lista de nota fiscal
  - Por perfil de faturação
  - Por conta de faturação
- APIs de Reserva
- API de integração do Power BI
- API de exportações
  - por conta de faturação
  - por perfil de faturação
  - Pela secção de nota fiscal
- Transação de API
- Orçamentos de API
- API de custo agregado

## <a name="effect-on-automation"></a>Efeito na automatização

As APIs usadas com um contrato de cliente da Microsoft são diferentes de aplicações utilizadas com um Enterprise Agreement. Poderá configurou anteriormente utilizando empresarial baseada em chave de automatização de API APIs ou APIs com base no Azure Resource Manager para inscrições de empresas. Se assim for, atualize a configuração de automatização para APIs mais recentes usadas com os contratos de cliente da Microsoft.

Para ver uma lista de APIs sem suporte mais antigas por contratos com o cliente Microsoft, consulte [APIs sem suporte](#unsupported-apis).

APIs para contratos de cliente da Microsoft suportam apenas a autenticação do Azure Resource Manager, que não suportam autenticação baseada em chave de API.

## <a name="pricesheet-apis-show-prices-for-azure-services"></a>APIs de folha de preços mostrar os preços dos serviços do Azure

As APIs de folha de preços para obter informações sobre todos os serviços que são classificados para faturação, preços de inclusão de contrato de cliente da Microsoft com base na quantidade de utilização. A API não inclui a elegibilidade de compras. Por exemplo, as compras de elegibilidade do Marketplace, os preços de instância reservada e ofertas de suporte. A API fornece o preço exclusivo para cada serviço por um nome de ordem de medidor e produto específico.

### <a name="view-the-pricesheet-for-azure-consumption-services-by-billing-profile"></a>Ver a folha de preços para os serviços de consumo do Azure ao perfil de faturação

Pode ver os preços para todos os serviços de consumo do Azure primeiro e de terceiros na folha de preços para um perfil de faturação. Utilize a API para ver os preços para os serviços de consumo do Azure. Os preços apresentados para o perfil de faturação aplicam-se a todas as subscrições que pertencem ao perfil de faturação.

- Formato CSV
  - POST -`https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31& format=csv`
- Formato JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json`

### <a name="view-current-open-billing-or-service-period-estimated-prices"></a>Faturação do modo de exibição atual aberta ou serviço estimado, período, os preços

Para ver os preços estimados para qualquer serviço no open atual de ciclo de faturação ou ponto de serviço, pode ver a folha de preços no formato JSON ou CSV.

- Formato CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`
- Formato JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Os preços indicados na folha de preços para o atual período de serviço sempre refletem os preços mais recentes para o medidor. Os preços são atualizados diariamente, com o preço mais recente para a ordem de medidor e o produto.

Ao revisar os custos com base no preço no período aberto, os custos são estimados. O último preço bloqueado no final do período de serviço abertos aplica-se a utilização no período aberto.

O período de aberta é o atual período de serviço em que a utilização ainda não estejam classificada de custos e de faturação.

### <a name="view-invoice-by-billing-profile-prices"></a>Nota fiscal de vista, os preços de perfil de faturação

Pode ver os preços dos serviços de consumo do Azure que são faturados numa nota fiscal específicas para um perfil de faturação. Utilize a API para exibir os preços apenas para os serviços que estão listados na nota fiscal. A nota fiscal é identificada pelo número da nota fiscal.

- Formato CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`

- Formato JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

O `{invoiceId}` listados anteriormente é igual a **número da nota fiscal** mostra o ficheiro PDF de resumo de nota fiscal. Eis um exemplo.

![Imagem de exemplo que mostra o número de nota fiscal que corresponde à InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

## <a name="pricesheet-api-asynchronous-response"></a>Resposta assíncrona da API da folha de preços

As APIs suportam os [assíncrona de REST do Azure](../azure-resource-manager/resource-manager-async-operations.md) formato. Depois de chamar as APIs com GET, verá a seguinte resposta:

```
            No Response Body
            HTTP Status 202 Accepted
```

 Os seguintes cabeçalhos são enviados com a localização de saída.

```
Location: https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
Azure-AsyncOperation: https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
   Retry-After: 10
 OData-EntityId: {operationId}
```

O cliente pode fazer uma chamada GET para a localização. A resposta para a chamada GET é o mesmo que as informações de exemplo mostradas anteriormente, até a operação de atinge um Estado de conclusão ou falha. Quando concluída, a resposta para a chamada para GET na localização devolve a mesma resposta como se a operação foi executada ao mesmo tempo. Eis um exemplo.

```
HTTP Status 200
i.e.
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

Além disso, o cliente pode também fazer uma chamada GET o `Azure-AsyncOperation`. O ponto final devolve o estado para a operação. Segue-se um exemplo:

```
HTTP Status 200
               {
"id": “providers/Microsoft.Consumption/operationStatus/{operationId}",
"name": "operationId",
"status" : "Succeeded | Failed | Canceled",
"startTime": "<DateLiteral per ISO8601>",
"endTime": "<DateLiteral per ISO8601>",
"percentComplete": <double between 0 and 100>
"properties": {
    /\* The resource provider can choose the values here, but it should only be
       returned on a successful operation (status being "Succeeded"). \*/
},
"error" : {
    /\* This is the OData v4 format, used by the RPC and will go into the
     v2.2 Azure REST API guidelines \*/
    "code": "BadArgument",
    "message": "The provided database &#39;foo&#39; has an invalid username."
}
}
```

### <a name="pricesheet-fields"></a>Campos de folha de preços

Reveja as informações em [compreender os termos na sua folha de preços para um contrato de cliente da Microsoft](../billing/billing-mca-understand-pricesheet.md) para ver todos os campos de folha de preços e suas descrições.

Para obter os preços com base na utilização, o preço de utilização está bloqueado para o período de serviço desde o início do período. Preço do serviço é aplicado e bloqueado na data da compra. O período de serviço é normalmente um mês de calendário para serviços do Azure.

O preço de um serviço de consumo do Azure num contrato de cliente da Microsoft é exclusivo para uma ordem de produto e o medidor.

Os campos que pode utilizar para identificar exclusivamente o preço para os serviços de consumo do Azure são:

- productOrderName
- meterId (also, meterName)

O preço de contratos de cliente da Microsoft é definido diferente contratos Enterprise em que é exclusivo para o preço dos serviços de na inscrição:

- Produto
- PartNumber
- Medidor
- oferta

### <a name="pricesheet-support"></a>Suporte de folha de preços

Atualmente, a API da folha de preços só suporta os preços de contrato de cliente da Microsoft para original do Azure e serviços de consumo de terceiros. Ele não suporta os preços para os serviços do Marketplace, as instâncias reservadas e benefícios de híbrida do Azure (AHUB).

A API fornece apenas o preço de unidade que será utilizado o serviço. Este preço é igual ou inferior ao preço negociado.

## <a name="query-for-usage-and-costs"></a>Consulta para utilização e custos

A API de detalhes de utilização fornece acesso ao Azure e a utilização de serviço do Marketplace de terceiros e os custos. Os preços baseiam-se nos preços negociados para seu contrato. Os clientes com EA mudar para um contrato de cliente devem utilizar detalhes de utilização para a conta de cobrança ou âmbito de perfil de faturação. Pode ser consulta em qualquer âmbito, com as seguintes exceções:

|   | Contrato de cliente | Enterprise Agreement | Contratos individuais (PAYG) |
| --- | --- | --- | --- |
| Conta de faturação | Sim | Sim | N/A |
| Perfis de faturação (nota fiscal) | Sim | N/D. Ver a conta de cobrança. | N/D. Ver subscrições. |
| Secções de fatura | Não. Utilize perfis de faturas com um filtro. | N/A | N/A |
| Departamentos de EA | N/A | Sim | N/A |
| Contas de inscrição de EA | N/A | Sim | N/D. Ver subscrições. |
| Grupos de gestão | Não. Utilize a conta de cobrança. | Sim | Sim |
| Subscrições | Sim | Sim | Sim |
| Grupos de recursos | Não. Utilize as subscrições com um filtro. | Não. Utilize as subscrições com um filtro. | Não. Utilize as subscrições com um filtro. |

As propriedades retornadas por UsageDetails por tipo de contrato são diferentes. Ver [documentação da API de UsageDetails](/rest/api/consumption/usagedetails/) para obter uma lista completa.

### <a name="power-bi-integration"></a>Integração do Power BI

Os clientes com EA mudar para um contrato de cliente da Microsoft devem parar de utilizar qualquer integração existente com o Power BI ao Microsoft Azure Consumption Insights com as suas informações de EA. Em vez disso, pode usar:

- Power BI Desktop – relatórios de criar novo Power BI para o contrato do Microsoft Customer com o [conector do Azure Cost Management](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights).
- Serviço do Power BI – movimentação do [Microsoft Azure Consumption Insights](https://docs.microsoft.com/power-bi/service-connect-to-azure-consumption-insights) pacote para a aplicação do Azure Cost Management (contrato de cliente), disponível em de conteúdos [AppSource](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview).

## <a name="unsupported-apis"></a>APIs não suportadas

Contratos de cliente da Microsoft não suporta as seguintes APIs do contrato Enterprise. São descritas alternativas para APIs não suportadas.

[Obter a folha de preços por subscrição](/rest/api/consumption/pricesheet/get/) não é suportado. Esta tabela indica os preços para o âmbito de um subscriptionId. Em vez disso, pode usar **folha de preços por perfil de faturação**. O preço de um serviço no âmbito de uma subscrição é o mesmo que o preço no âmbito de um perfil de faturação. O utilizador a chamar a API deve ter as permissões necessárias.

**Preço folha - obter por período de faturação** não é suportado. Obter a folha de preços para um âmbito ao subscriptionId e o período de faturação. Em vez disso, pode usar **folha de preços por perfil de faturação**. O preço do serviço no âmbito da subscrição é igual ao preço do serviço num âmbito de perfil de faturação. O utilizador a chamar a API deve ter as permissões necessárias.

[Folha de preços para os clientes empresariais por inscrição](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) não é suportada. E, [folha de preços para os clientes empresariais por inscrição durante um período de faturação](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) não é suportada. Ambos fornecem a taxa aplicável para cada contador para a inscrição e o período de faturação determinado. Em vez disso, pode usar **folha de preços por perfil de faturação**. A folha de preços no âmbito da inscrição é a folha de preços disponível para o perfil de faturação. O utilizador a chamar a API deve ter as permissões necessárias.

## <a name="see-also"></a>Consulte também
- Saiba mais sobre [APIs de REST de consumo do Azure](/rest/api/consumption/).
