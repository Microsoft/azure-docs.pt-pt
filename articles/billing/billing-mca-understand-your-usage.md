---
title: Compreender os termos na sua utilização do Azure e os encargos de CSV para um contrato de cliente da Microsoft | Documentos da Microsoft
description: Saiba como ler e entender as secções da utilização do Azure e os encargos CSV para o seu perfil de faturação
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57836951"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Compreender os termos na sua utilização do Azure e os encargos de CSV para um contrato de cliente da Microsoft

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

O ficheiro CSV de utilização e os encargos do Azure contém os custos de utilização de nível de diária e dos medidores para o período de faturação atual.

Para obter o seu ficheiro de utilização e os encargos do Azure, veja [ver e transferir utilização do Azure e custos para o contrato do cliente Microsoft](billing-download-azure-daily-usage.md).
Está disponível num formato de ficheiro de valores separados por vírgulas (. csv) que pode abrir num aplicativo de folha de cálculo.

Custos de utilização são o total **mensal** das cobranças uma subscrição. Os custos de utilização não levam em conta quaisquer créditos ou descontos.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Alterações na utilização do Azure do contrato Enterprise e custos de CSV

Se fosse num cliente EA, observará que os termos no perfil de faturação ficheiro CSV de utilização do Azure são diferentes do que os termos no ficheiro CSV de utilização do EA Azure. Este é um mapeamento dos termos de utilização do mesmo para os termos de utilização de perfil de faturação:

| Utilização do EA Azure CSV | Utilização do Microsoft Azure do contrato de cliente e os encargos de CSV |
| --- | --- |
| Date | date |
| Mês| date |
| Dia | date |
| Ano | date |
| Produto | produto |
| Id do Medidor | meterID |
| Categoria do Medidor | meterCategory |
| Sub-categoria do Medidor | meterSubCategory |
| Região do Medidor | meterRegion |
| Nome do Medidor | meterName |
| Quantidade Consumida | quantidade |
| Taxa de Recursos | effectivePrice | <!-- this was highlighted -->
| Custos Alargados | custo |
| Localização do Recurso | resourceLocation |
| Serviço Consumido | consumedService |
| Id da Instância | instanceId |
| Informações de Serviço 1 | serviceInfo1 |
| Informações de Serviço 2 | serviceInfo2 |
| Informações Adicionais | additionalInfo |
| Etiquetas | etiquetas |
| Identificador de Serviço da Loja | N/A |
| Nome do Departamento | invoiceSection | <!-- this was highlighted -->
| Centro de Custo | costCenter |
| Unidade de Medida | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Termos detalhados e as descrições no seu ficheiro de utilização e os encargos do Azure

A secção seguinte descreve os termos importantes mostrados no ficheiro de utilização e os encargos do Azure.

Termo | Descrição
--- | ---
invoiceId | O ID do documento exclusivo listado da nota fiscal PDF
previousInvoiceId | Referência para uma nota fiscal original se este item de linha é um reembolso
billingAccountName | Nome da conta de faturação
billingAccountId | Identificador exclusivo para a conta de faturação de raiz
billingProfileId | Nome do perfil de faturação que estará a acumular os custos que são faturados
billingProfileName | Identificador exclusivo para o perfil de faturação que estará a acumular os custos que são faturados
invoiceSectionId | Identificador exclusivo para a secção de nota fiscal
invoiceSectionName | Nome da secção de nota fiscal
costCenter | O Centro de custos definido na subscrição para controlar os custos (disponíveis apenas em períodos de faturação abertos)
billingPeriodStartDate | A data de início do período de faturação para o qual a nota fiscal é gerada
billingPeriodEndDate | A data de fim do período de faturação para o qual a nota fiscal é gerada
servicePeriodStartDate | A data de início do período de classificação que tem definidos e bloqueada de preços do serviço do consumidos ou serão comprado
servicePeriodEndDate | A data de fim do período de classificação que tem definidos e bloqueada de preços do serviço do consumidos ou serão comprado
date | Para o Azure Marketplace com base na utilização custos e, essa é a data de classificação. Para compras de uso individual (reservas, Marketplace) ou fixos custos recorrentes (ofertas de suporte), essa é a data de compra.
serviceFamily | Família de serviços de que o serviço pertence a
productOrderId | Identificador exclusivo para a ordem de produto
productOrderName | Nome exclusivo para a ordem de produto
consumedService | Nome do serviço consumido
meterId | O identificador exclusivo para o medidor
meterName | O nome do medidor de
meterCategory | Nome da categoria de classificação para o medidor. Por exemplo, *serviços Cloud*, *Networking*, etc.
meterSubCategory | Nome da categoria de classificação frações de medidor
meterRegion | Nome da região em que o medidor para o serviço está disponível. Identifica a localização do Datacenter para determinados serviços cujo preço é definido com base na localização do Datacenter.
oferta | Nome da oferta comprada
productId | Identificador exclusivo para o produto acumulação de encargos
produto | Nome do produto acumulação de encargos
ID de subscrição | Identificador exclusivo para a subscrição a acumular os custos
subscriptionName | Nome da subscrição acumulação de encargos
reservationId | Identificador exclusivo para a instância de reserva comprada
reservationName | Nome da instância de reserva comprada
publisherType | Tipo de publicador (valores: firstParty thirdPartyReseller, thirdPartyAgency)
publisherName | Publicador para os serviços do Marketplace
resourceGroupId | Identificador exclusivo para o grupo de recursos associado com o recurso
resourceGroupName | Nome do grupo de recursos associado com o recurso
resourceId | Identificador exclusivo para a instância de recurso
resourceType | Tipo de instância de recurso
resourceLocation | Identifica a localização do Datacenter onde o recurso está em execução.
localização | Normalizado localização do recurso se localizações de recurso diferentes estão configuradas para as regiões da mesmas
quantidade | O número de unidades compradas ou consumidos
unitOfMeasure | A unidade de medida para uma faturação para o serviço. Por exemplo, os serviços de computação são faturados por hora.
chargeType | O tipo de custo. Valores: <ul><li>AsCharged-Usage: Custos que são acumulados com base na utilização de um serviço do Azure. Isto inclui a utilização nas VMs que não são cobradas por causa de instâncias reservadas.</li><li>AsCharged-PurchaseMarketplace: De cobrança recorrente única ou fixa de compras do Marketplace</li><li>AsCharged-UsageMarketplace: Custos de serviços do Marketplace são cobrados com base em unidades de consumo</li></ul>
isAzureCreditEligible | Sinalizador que indica se o valor cobrado pelo contra o serviço é elegível para ser pagas com créditos do Azure (valores: TRUE, False)
serviceInfo1 | Metadados específicos do serviço
serviceInfo2 | Campo legado que captura metadados opcionais do específico do serviço
additionalInfo | Metadados de serviço específicas adicionais.
etiquetas | Etiquetas que atribuir ao recurso

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Como posso Certifique-se de que os encargos no meu arquivo de utilização e os encargos do Azure estão corretos?

Se existe uma cobrança no seu ficheiro de utilização detalhada que deseja obter mais detalhes, consulte o artigo [compreender os encargos na fatura de seu perfil de faturação](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a sua utilização do Microsoft Azure e a cobrança](billing-download-azure-daily-usage.md)
