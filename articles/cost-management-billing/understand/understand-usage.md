---
title: Compreender a utilização e os custos detalhados | Microsoft Docs
description: Saiba como ler e compreender a utilização e os custos detalhados
author: bandersmsft
manager: micflan
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: a68393b2852f8ddc758e2a47b9e1b5d94befb7b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290143"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Compreender os termos no ficheiro de utilização e de custos do Azure

O ficheiro da utilização e dos custos detalhados contém a utilização diária ponderada com base em tarifas negociadas, as compras (por exemplo, reservas, tarifas do Marketplace) e os reembolsos do período especificado.
As tarifas não incluem créditos, impostos nem outros encargos ou descontos.
A tabela a seguir aborda os custos incluídos em cada tipo de conta.

Tipo de conta | Utilização do Azure | Utilização do Marketplace | Compras | Reembolsos
--- | --- | --- | --- | ---
Contrato Enterprise (EA) | Sim | Sim | Sim | Não
Contrato de Cliente Microsoft (MCA) | Sim | Sim | Sim | Sim
Pay as you go (PAYG) | Sim | Sim | Não | Não

Para saber mais sobre as encomendas no Marketplace (também conhecidas como serviços externos), veja [Compreender os custos de serviços externos do Azure](understand-azure-marketplace-charges.md).

Para obter as instruções de transferência, veja [Como obter a faturação e os dados de utilização diária do Azure](../manage/download-azure-invoice-daily-usage-date.md).
Pode abrir o ficheiro CSV de utilização e custos no Microsoft Excel ou noutra aplicação de folha de cálculo.

## <a name="list-of-terms-and-descriptions"></a>Lista de termos e descrições

A tabela a seguir descreve os termos importantes utilizados na versão mais recente do ficheiro de utilização e custos do Azure.
A lista inclui as contas pay as you go (PAYG), Contrato Enterprise (EA) e Contrato de Cliente da Microsoft (MCA).

Duração | Tipo de conta | Descrição
--- | --- | ---
AccountName | EA, PAYG | Nome a apresentar da conta de inscrição EA ou da conta de faturação PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Identificador exclusivo da conta de inscrição EA ou da conta de faturação PAYG.
AdditionalInfo | Tudo | Metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual.
BillingAccountId<sup>1</sup> | Tudo | Identificador exclusivo da conta de faturação raiz.
BillingAccountName | Tudo | Nome da conta de faturação.
BillingCurrency | Tudo | Moeda associada à conta de faturação.
BillingPeriod | EA, PAYG | O período de faturação do custo.
BillingPeriodEndDate | Tudo | A data de fim do período de faturação.
BillingPeriodStartDate | Tudo | A data de início do período de faturação.
BillingProfileId<sup>1</sup> | Tudo | Identificador exclusivo da inscrição EA, da subscrição PAYG, do perfil de faturação MCA ou da conta consolidada AWS.
BillingProfileName | Tudo | Nome da inscrição EA, da subscrição PAYG, do perfil de faturação MCA ou da conta consolidada AWS.
ChargeType | Tudo | Indica se o custo representa a utilização (**Utilização**), uma compra (**Compra**) ou um reembolso (**Reembolso**).
ConsumedService | Tudo | Nome do serviço ao qual o custo está associado.
CostCenter<sup>1</sup> | EA, MCA | O centro de custos definido para a subscrição para controlar os custos (apenas disponível nos períodos de faturação abertos para as contas MCA).
Custo | EA, PAYG | Veja CostInBillingCurrency.
CostInBillingCurrency | MCA | Custo do encargo na moeda de faturação antes dos créditos ou dos impostos.
CostInPricingCurrency | MCA | Custo do encargo na moeda do preço antes dos créditos ou dos impostos.
Moeda | EA, PAYG | Veja BillingCurrency.
Data<sup>1</sup> | Tudo | A data de utilização ou de compra à qual corresponde o custo.
EffectivePrice | Tudo | Preço unitário combinado para o período. Os preços combinados resultam da média das flutuações no preço unitário, como os escalões de preços proporcionais, o que reduz o preço à medida que a quantidade aumenta com o tempo.
ExchangeRateDate | MCA | Data em que a taxa de câmbio foi estabelecida.
ExchangeRatePricingToBilling | MCA | Taxa de câmbio utilizada para converter o custo da moeda do preço na moeda de faturação.
Frequência | Tudo | Indica se é esperado que um encargo se repita. O encargo pode ocorrer uma vez (**OneTime**), repetir-se mensalmente ou anualmente (**Recorrente**) ou basear-se na utilização (**UsageBased**).
InvoiceId | PAYG, MCA | O ID exclusivo do documento listado no PDF da fatura.
InvoiceSection | MCA | Veja InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Identificador exclusivo do departamento do EA ou da secção da fatura do MCA.
InvoiceSectionName | EA, MCA | Nome do departamento do EA ou da secção da fatura do MCA.
IsAzureCreditEligible | Tudo | Indica se o encargo está qualificado para ser pago pelo uso de créditos do Azure (valores: true, false).
Localização | MCA | Localização do datacenter onde o recurso está em execução.
MeterCategory | Tudo | Nome da categoria de classificação do medidor. Por exemplo, *Serviços cloud* e *Rede*.
MeterId<sup>1</sup> | Tudo | O identificador exclusivo do medidor.
MeterName | Tudo | O nome do medidor.
MeterRegion | Tudo | Nome da localização do datacenter dos serviços com preços baseados na localização. Veja Localização.
MeterSubCategory | Tudo | Nome da categoria de subclassificação do medidor.
OfferId<sup>1</sup> | Tudo | Nome da oferta comprada.
PartNumber<sup>1</sup> | EA, PAYG | Identificador utilizado para obter preços específicos do medidor.
PlanName | EA, PAYG | Nome do plano do Marketplace.
PreviousInvoiceId | MCA | Referência a uma fatura original, caso este item de linha seja um reembolso.
PricingCurrency | MCA | Moeda utilizada aquando da classificação baseada nos preços negociados.
Produto | Tudo | Nome do produto.
ProductId<sup>1</sup> | MCA | Identificador exclusivo do produto.
ProductOrderId | Tudo | Identificador exclusivo da encomenda do produto.
ProductOrderName | Tudo | Nome exclusivo da encomenda do produto.
Nome do Editor | Tudo | Publicador dos serviços do Marketplace.
PublisherType | Tudo | Tipo de Publicador (valores: **Azure**, **AWS**, **Marketplace**).
Quantidade | Tudo | O número de unidades compradas ou consumidas.
ReservationId | EA, MCA | Identificador exclusivo da instância da reserva comprada.
ReservationName | EA, MCA | Nome da instância da reserva comprada.
ResourceGroup | Tudo | Nome do [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) no qual se encontra o recurso. Nem todos os encargos são provenientes de recursos implantados em grupos de recursos. Os encargos que não têm um grupo de recursos serão mostrados como nulo/vazio, **outros**ou **não aplicáveis**.
ResourceId<sup>1</sup> | Tudo | Identificador exclusivo do recurso [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | Tudo | Localização do datacenter onde o recurso está em execução. Veja Localização.
ResourceName | EA, PAYG | Nome do recurso. Nem todos os encargos são provenientes de recursos implantados. Os encargos que não têm um tipo de recurso serão mostrados como nulo/vazio, **outros**ou **não aplicáveis**.
ResourceType | MCA | Tipo da instância do recurso. Nem todos os encargos são provenientes de recursos implantados. Os encargos que não têm um tipo de recurso serão mostrados como nulo/vazio, **outros**ou **não aplicáveis**.
ServiceFamily | MCA | Família de serviços à qual o serviço pertence.
ServiceInfo1 | Tudo | Metadados específicos do serviço.
ServiceInfo2 | Tudo | Campo legado com metadados opcionais e específicos do serviço.
ServicePeriodEndDate | MCA | A data de fim do período de classificação que definiu e bloqueou o preço do serviço consumido ou comprado.
ServicePeriodStartDate | MCA | A data de início do período de classificação que definiu e bloqueou o preço do serviço consumido ou comprado.
SubscriptionId<sup>1</sup> | Tudo | Identificador exclusivo da subscrição do Azure.
SubscriptionName | Tudo | Nome da subscrição do Azure.
Tags<sup>1</sup> | Tudo | Etiquetas atribuídas ao recurso. Não inclui etiquetas de grupos de recursos. Podem ser utilizada para agrupar ou distribuir encargos para estorno interno. Para obter mais informações, veja [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizar os recursos do Azure com etiquetas).
Duração | Tudo | Apresenta o termo da validade da oferta. Por exemplo: no caso de instâncias reservadas, ele exibe 12 meses como o termo. Para compras pontuais ou compras periódicas, o Termo é 1 mês (SaaS, Suporte do Marketplace). Não é aplicável ao consumo do Azure.
UnitOfMeasure | Tudo | A unidade de medida da faturação do serviço. Por exemplo, os serviços de computação são faturados à hora.
UnitPrice | EA, PAYG | O preço unitário do encargo.

_<sup> **1**</sup> Campos utilizados para criar um ID exclusivo para um único registo de custos._

Repare que alguns campos podem diferir nas maiúsculas, minúsculas e nos espaços entre um tipo de conta e outro.
As versões mais antigas dos ficheiros de utilização pay as you go têm secções separadas para o extrato e para a utilização diária.

### <a name="list-of-terms-from-older-apis"></a>Lista de termos das APIs mais antigas
A tabela a seguir estabelece a correspondência entre os termos utilizados nas APIs mais antigas e os novos termos. Veja a tabela acima para obter as descrições.

Termo antigo | Novo termo
--- | ---
ConsumedQuantity | Quantidade
IncludedQuantity | N/A
InstanceId | ResourceId
Tarifa | EffectivePrice
Unidade | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Confirmar que os custos estão corretos

Para saber mais sobre a utilização e os custos detalhados, veja como compreender a fatura [pay as you go](review-individual-bill.md) ou do [Contrato de Cliente da Microsoft](review-customer-agreement-bill.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Ver e transferir a fatura do Microsoft Azure](download-azure-invoice.md)
- [Ver e transferir a utilização e os custos do Microsoft Azure](download-azure-daily-usage.md)
