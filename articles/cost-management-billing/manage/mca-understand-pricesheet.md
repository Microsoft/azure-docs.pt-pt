---
title: Termos na folha de preços do Contrato de Cliente Microsoft – Azure
description: Saiba como ler e compreender a utilização e faturação de um Contrato de Cliente da Microsoft.
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
ms.openlocfilehash: 46d0f259d30f9864c371f8bd3dd01f593004c808
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133577"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Termos na sua folha de preços do Contrato de Cliente da Microsoft

Este artigo aplica-se à conta de faturação do Azure de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se for um Proprietário de perfil de faturação, Contribuidor, Leitor ou Gerente de Faturas, pode transferir a folha de preços da sua organização a partir do portal do Azure. Veja [Visualizar e transferir os preços da sua organização](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termos e descrições na sua folha de preços

A secção a seguir descreve os termos importantes mostrados na sua folha de preços do Contrato de Cliente da Microsoft.

| **Nome do campo**   | **Descrição**   |
| --- | --- |
| basePrice  | O preço de mercado no momento em que o cliente inicia sessão ou o preço de mercado no momento em que o medidor de serviço é iniciado, caso seja após o início de sessão.   |
| billingAccountId  | Identificador exclusivo da conta de faturação.   |
| billingAccountName  | Nome da conta de faturação.  |
| billingCurrency | Moeda na qual as cobranças são lançadas |
| billingProfileId  | Identificador exclusivo do perfil de faturação.   |
| billingProfileName  | Nome do perfil de faturação configurado para receber faturas. Os preços na folha de preços estão associados a este perfil de faturação. |
| moeda | Moeda na qual todos os preços são refletidos. |
| desconto | O desconto de preço oferecido pelo escalão de graduação, escalão gratuito, Quantidade Incluída ou descontos negociados, quando aplicável. Representado como percentagem. |
| effectiveEndDate  | Data de término do preço em vigor. |
| effectiveStartDate  | Data de início quando o preço entra em vigor. |
| includedQuantity | Quantidades de um serviço específico que um cliente tem direito a consumir sem encargos incrementais. |
| marketPrice | O atual preço de mercado predominante de um determinado serviço. |
| meterId  | Identificador exclusivo do medidor. |
| meterCategory  | Nome da categoria de classificação do medidor. Por exemplo, _Serviços Cloud_, _Rede_, etc. |
| meterName  | Nome do medidor. O medidor representa o recurso implantável de um serviço do Azure. |
| meterSubCategory  | Nome da categoria de subclassificação do medidor.  |
| meterType  |  Nome do tipo de medidor. |
| meterRegion  | Nome da região em que o medidor do serviço está disponível. Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.    |
| Produto  | Nome do produto que acumula os custos. Exemplo: Base de dados SQL Básico versus Base de dados SQL Standard  |
| productId  | Identificador exclusivo do produto cujo medidor é consumido. |
| productOrderName  | Nome do plano de produto comprado. |
| serviceFamily  | Tipo de serviço do Azure. Exemplo: Computação, Análise, Segurança |
| tierMinimumUnits  | Define o limite inferior do intervalo de camadas para o qual os preços são definidos. Por exemplo, se o intervalo for de 0 a 100, tierMinimumUnits será 0.  |
| unitOfMeasure  | Identifica as unidades de medida da faturação do serviço. Por exemplo, os serviços de computação são faturados à hora. |
| unitPrice  | Preço por unidade no momento da faturação (não o preço combinado em vigor) como específico a um medidor e nome de ordem de produto.  Nota: o preço por unidade não é o mesmo que o preço em vigor em transferências de detalhes de utilização em caso de serviços que têm preços diferenciais entre camadas.  No caso de serviços com preços de várias camadas, o preço em vigor é uma taxa combinada entre as camadas e não mostra um preço por unidade específico a uma camada. O preço combinado ou o preço em vigor é o preço líquido para a quantidade consumida que abrange várias camadas (em que cada camada tem um preço por unidade específico). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Ver e transferir os preços da sua organização](ea-pricing.md)
