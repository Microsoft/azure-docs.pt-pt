---
title: Compreender a sua utilização detalhada e custos | Documentos da Microsoft
description: Saiba como ler e compreender a sua utilização detalhada e custos
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683992"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Compreender os termos no seu ficheiro de utilização e os encargos do Azure

O ficheiro de utilização e os encargos detalhado contém a utilização de mais votada diária com base nas tarifas negociadas e reembolsos compras (por exemplo, as reservas, markeplace) durante o período especificado.
As tarifas não incluem créditos, impostos, ou outros custos ou descontos.
A tabela seguinte aborda o que estão incluídas para cada tipo de conta.

Tipo de conta | Utilização do Azure | Utilização do Marketplace | Compras | Reembolsos
--- | --- | --- | --- | ---
Contrato Enterprise (EA) | Sim | Sim | Sim | Não
Contrato de Cliente Microsoft (MCA) | Sim | Sim | Sim | Sim
Pay as you go (PAYG) | Sim | Não | Não | Não

Para saber mais sobre pedidos de Marketplace (também conhecidos como serviços externos), veja [compreender os encargos de serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

Ver [como obter a faturação da nota fiscal e diário de dados de utilização do Azure](billing-download-azure-invoice-daily-usage-date.md) para obter instruções de download.
O ficheiro de utilização e a cobrança está disponível num formato de ficheiro de valores separados por vírgulas (. csv), que pode abrir num aplicativo de folha de cálculo.

## <a name="list-of-terms-and-descriptions"></a>Lista de termos e as descrições

A tabela seguinte descreve termos importantes utilizados na versão mais recente do ficheiro de utilização e os encargos do Azure.
A lista cobrirá pay as you go (PAYG), Enterprise Agreement (EA) e contas de contrato de cliente da Microsoft (MCA).

Termo | Tipo de conta | Descrição
--- | --- | ---
AccountName | EA | Nome a apresentar da conta de inscrição.
AccountOwnerId | EA | Identificador exclusivo para a conta de inscrição.
AdditionalInfo | Todos | Metadados de serviço específicos. Por exemplo, um tipo de imagem para uma máquina virtual.
BillingAccountId | EA, MCA | Identificador exclusivo para a conta de faturação de raiz.
BillingAccountName | EA, MCA | Nome da conta de cobrança.
BillingCurrency | EA, MCA | Moeda associada com a conta de cobrança.
BillingPeriod | EA | O período de faturação do custo.
BillingPeriodEndDate | EA, MCA | A data de fim do período de faturação.
billingPeriodStartDate | EA, MCA | A data de início do período de faturação.
BillingProfileId | EA, MCA | Identificador exclusivo da inscrição EA ou MCA perfil de faturação.
BillingProfileName | EA, MCA | Nome da inscrição EA ou MCA perfil de faturação.
ChargeType | EA, MCA | Indica se o valor cobrado pelo representa utilização (**utilização**), uma compra (**comprar**), ou o reembolso (**reembolso**).
ConsumedQuantity | PAYG | Ver a quantidade.
ConsumedService | Todos | Nome do serviço a cobrança está associada.
Custo | EA | Ver CostInBillingCurrency.
CostCenter | EA, MCA | O Centro de custos definido para a subscrição para controlar os custos (disponíveis apenas em períodos de faturação abertos para contas MCA).
CostInBillingCurrency | MCA | Custo do custo na moeda de faturação antes de créditos ou outros impostos.
CostInPricingCurrency | MCA | Custo do custo na moeda preços antes de créditos ou outros impostos.
Moeda | PAYG | Ver BillingCurrency.
Date | EA, MCA | A data de utilização ou compra do custo.
ExchangeRateDate | MCA | Data que a taxa de câmbio foi estabelecida.
ExchangeRatePricingToBilling | MCA | Taxa de câmbio utilizada para converter o custo na moeda preços para a moeda de faturação.
Frequência | EA, MCA | Indica se uma cobrança é esperada para repetir. Os custos pode um acontece uma vez (**OneTime**), repita numa base mensal ou anualmente (**periódico**), ou ser com base na utilização (**UsageBased**).
includedQuantity | PAYG | A quantidade do medidor de que está incluída sem custos no período de faturação atual.
InstanceId | PAGY | Ver ResourceId.
InvoiceId | EA, MCA | O ID exclusivo do documento listado da nota fiscal PDF.
InvoiceSection | MCA | Ver InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificador exclusivo para o departamento de EA ou secção da nota fiscal MCA.
InvoiceSectionName | EA, MCA | Nome do departamento de EA ou secção da nota fiscal MCA.
IsAzureCreditEligible | EA, MCA | Indica se o custo é elegível para ser pagas com créditos do Azure (valores: TRUE, False).
Location | EA, MCA | Localização do Datacenter onde o recurso está em execução.
MeterCategory | Todos | Nome da categoria de classificação para o medidor. Por exemplo, *serviços Cloud* e *Networking*.
MeterId | Todos | O identificador exclusivo para o medidor.
MeterName | Todos | O nome do medidor.
MeterRegion | Todos | Nome da localização do datacenter para serviços cujo preço tem com base na localização. Ver a localização.
MeterSubCategory | Todos | Nome da categoria de subclassification medidor.
OfferId | EA, MCA | Nome da oferta comprada.
PartNumber | EA | Identificador utilizado para obter preços de medidor específico.
PlanName | EA | Nome do plano do Marketplace.
PreviousInvoiceId | MCA | Referência para uma nota fiscal original se este item de linha é um reembolso.
pricingCurrency | MCA | Moeda utilizada quando a classificação com base nos preços negociados.
Product | MCA | Ver ProductName.
productId | EA, MCA | Identificador exclusivo para o produto.
ProductName | EA | Nome do produto.
ProductOrderId | EA, MCA | Identificador exclusivo para a ordem de produto.
ProductOrderName | EA, MCA | Nome exclusivo para a ordem de produto.
Nome do Editor | EA, MCA | Publicador para os serviços do Marketplace.
PublisherType | EA, MCA | Tipo de publicador (valores: firstParty thirdPartyReseller, thirdPartyAgency).
Quantidade | EA, MCA | O número de unidades compradas ou consumidos.
Tarifa | PAYG | Ver UnitPrice.
ReservationId | EA, MCA | Identificador exclusivo para a instância de reserva comprada.
ReservationName | EA, MCA | Nome da instância de reserva comprada.
ResourceGroupId | EA, MCA | Identificador exclusivo para o [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) o recurso está no.
ResourceGroupName | EA, MCA | Nome da [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) o recurso está no.
ResourceId | EA, MCA | Identificador exclusivo dos [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) recursos.
ResourceLocation | EA, MCA | Localização do Datacenter onde o recurso está em execução. Ver a localização.
ResourceName | EA | Nome do recurso.
ResourceType | MCA | Tipo de instância de recurso.
ServiceFamily | EA, MCA | Família de serviços de que o serviço pertence.
ServiceInfo1 | Todos | Metadados de serviço específicos.
ServiceInfo2 | Todos | Campo legado com metadados opcionais de serviços específicos.
ServicePeriodEndDate | MCA | A data de fim do período de classificação que definidos e bloqueada de preços do serviço do consumidos ou serão comprado.
ServicePeriodStartDate | MCA | A data de início do período de classificação que definidos e bloqueada de preços do serviço do consumidos ou serão comprado.
SubscriptionId | Todos | Identificador exclusivo para a subscrição.
SubscriptionName | Todos | Nome da subscrição.
Tags | Todos | Etiquetas atribuídas ao recurso. Não inclui os sinalizadores de grupo de recursos. Pode ser utilizado para agrupar ou distribuir os custos de estorno interno. Para obter mais informações, consulte [organizar os recursos do Azure com etiquetas](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unidade | PAYG | Ver UnitOfMeasure.
UnitOfMeasure | Todos | A unidade de medida para uma faturação para o serviço. Por exemplo, os serviços de computação são faturados por hora.
unitPrice | EA | O preço por unidade para a cobrança.
UsageDate | PAYG | Ver a data.

Tenha em atenção de que alguns campos podem ser diferentes em maiúsculas/minúsculas e o espaçamento entre os tipos de conta.
As versões mais antigas dos ficheiros de utilização de pay as you go tem seções separadas para a instrução e uma utilização diária.

## <a name="ensure-that-your-charges-are-correct"></a>Certifique-se de que as cobranças são corretas

Para saber mais sobre a utilização detalhada e os encargos, leia sobre como compreender seu [pay as you go](./billing-understand-your-bill.md) ou [contrato de cliente da Microsoft](billing-mca-understand-your-bill.md) nota fiscal.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a sua utilização do Microsoft Azure e a cobrança](billing-download-azure-daily-usage.md)
