---
title: Termos do ficheiro de utilização e custos do Contrato de Cliente Microsoft do Azure
description: Saiba como ler e compreender as secções do CSV de utilização e de custos do Azure para o seu perfil de faturação.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 009a69baa68ed4a826d6a78c0df5d19d34bff006
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134584"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Termos no ficheiro de utilização e custos do Azure para um Contrato de Cliente da Microsoft

Este artigo aplica-se à conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

O ficheiro CSV de utilização e custos do Azure contém custos de utilização diária e de nível de medidor para o período de faturação atual.

Para obter o ficheiro de utilização e custos do Azure, veja [Visualizar e transferir a utilização e os custos do Azure para o seu Contrato de Cliente da Microsoft](download-azure-daily-usage.md). Está disponível num formato de ficheiro de valores separados por vírgulas (.csv) que pode abrir numa aplicação de folha de cálculo.

Os custos de utilização são o total de custos **mensais** de uma subscrição. Os custos de utilização não têm em consideração nenhum crédito ou desconto.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Alterações de utilização e custos do Azure EA

Se for um cliente do EA, vai reparar que os termos no ficheiro CSV de utilização do perfil de faturação do Azure diferem dos termos no ficheiro CSV de utilização do Azure EA. Segue-se um mapa dos termos de utilização do EA para termos de utilização do perfil de faturação:

| CSV de utilização do Azure EA | CSV de utilização e custos do Contrato de Cliente da Microsoft do Azure |
| --- | --- |
| Date | date |
| Mês| date |
| Dia | date |
| Ano | date |
| Produto | produto |
| MeterId | meterId |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantidade |
| ResourceRate | effectivePrice |
| ExtendedCost | custo |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Etiquetas | etiquetas |
| StoreServiceIdentifier | N/D |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Termos e descrições detalhados

Os termos a seguir são mostrados no ficheiro de utilização e custos do Azure.

Termo | Descrição
--- | ---
invoiceId | ID exclusivo do documento listado no PDF da fatura
previousInvoiceId | Referência a uma fatura original, caso este item de linha seja um reembolso
billingAccountName | Nome da conta de faturação
billingAccountId | Identificador exclusivo da conta de faturação raiz
billingProfileId | Nome do perfil de faturação que está a acumular os custos que são faturados
billingProfileName | Identificador exclusivo do perfil de faturação que está a acumular os custos que são faturados
invoiceSectionId | Identificador exclusivo para a secção da fatura
invoiceSectionName | Nome da secção da fatura
costCenter | Centro de custos definido na subscrição para controlar os custos (apenas disponível nos períodos de faturação abertos)
billingPeriodStartDate | Data de início do período de cobrança para o qual a fatura é gerada
billingPeriodEndDate | Data de término do período de cobrança para o qual a fatura é gerada
servicePeriodStartDate | Data de início do período de classificação que definiu e bloqueou o preço do serviço consumido ou comprado
servicePeriodEndDate | Data de término do período de classificação que definiu e bloqueou o preço do serviço consumido ou comprado
date | Para custos com base na utilização do Azure e do Marketplace, esta é a data de classificação. Para compras de utilização única (Reservas, Marketplace) ou custos recorrentes fixos (ofertas de suporte), esta é a data de compra.
serviceFamily | Família de serviços à qual o serviço pertence
productOrderId | Identificador exclusivo da encomenda do produto
productOrderName | Nome exclusivo da encomenda do produto
consumedService | Nome do serviço consumido
meterId | Identificador exclusivo do medidor
meterName | Nome do medidor
meterCategory | Nome da categoria de classificação do medidor. Por exemplo, *Serviços Cloud*, *Rede*, etc.
meterSubCategory | Nome da categoria de subclassificação do medidor
meterRegion | Nome da região em que o medidor do serviço está disponível. Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.
oferta | Nome da oferta comprada
productId | Identificador exclusivo do produto que acumula os custos
produto | Nome do produto que acumula os custos
ID da subscrição | Identificador exclusivo da subscrição que acumula os custos
subscriptionName | Nome da subscrição que acumula os custos
reservationId | Identificador exclusivo da instância da reserva comprada
reservationName | Nome da instância da reserva comprada
publisherType | Tipo de publicador (valores: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Publicador dos serviços do Marketplace
resourceGroupId | Identificador exclusivo do grupo de recursos associado ao recurso
resourceGroupName | Nome do grupo de recursos associado ao recurso
resourceId | Identificador exclusivo para a instância de recurso
resourceType | Tipo da instância de recurso
resourceLocation | Identifica a localização do datacenter onde o recurso está em execução.
localização | Local normalizado do recurso se localizações de recursos diferentes estiverem configuradas para as mesmas regiões
quantidade | Número de unidades compradas ou consumidas
unitOfMeasure | Unidade de medida da faturação do serviço. Por exemplo, os serviços de computação são faturados à hora.
chargeType | Tipo de custos. Valores: <ul><li>AsCharged-Usage: custos acumulados com base na utilização de um serviço do Azure. Tal inclui a utilização em máquinas virtuais que não são cobradas devido a instâncias reservadas.</li><li>AsCharged-PurchaseMarketplace: custos recorrentes de utilização única ou fixos de compras do Marketplace</li><li>AsCharged-UsageMarketplace: custos para serviços do Marketplace cobrados com base em unidades de consumo</li></ul>
isAzureCreditEligible | Sinalizador que indica se os custos pelo serviço são elegíveis para pagamento com os créditos do Azure (valores: Verdadeiro, Falso)
serviceInfo1 | Metadados específicos do serviço
serviceInfo2 | Campo legado que captura metadados opcionais específicos de serviço
additionalInfo | Metadados adicionais específicos do serviço.
etiquetas | Etiquetas que atribui ao recurso

### <a name="make-sure-that-charges-are-correct"></a>Verifique se os custos estão corretos

Se quiser confirmar se os custos no seu ficheiro de utilização detalhado estão corretos, pode verificá-los. Veja [Compreender os custos na fatura do perfil de faturação](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Ver e transferir a fatura do Microsoft Azure](download-azure-invoice.md)
- [Ver e transferir a utilização e os custos do Microsoft Azure](download-azure-daily-usage.md)
