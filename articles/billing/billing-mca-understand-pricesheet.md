---
title: Compreender os termos na sua folha de preços para um contrato de cliente da Microsoft - Azure
description: Saiba como ler e compreender a utilização e faturação para um contrato de cliente da Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490650"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Termos na sua folha de preços do contrato de cliente da Microsoft

Este artigo aplica-se a uma conta de faturação do Azure para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se for um perfil de faturação proprietário, Contribuidor, leitor ou Gerenciador de faturas pode transferir a folha de preços da sua organização no portal do Azure. Ver [modo de exibição e a transferência de preços de sua organização](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termos e descrições na sua folha de preços

A secção seguinte descreve os termos de importantes exibidos na sua folha de preços do contrato de cliente da Microsoft.

| **Nome do campo**   | **Descrição**   |
| --- | --- |
| billingAccountId  | Identificador exclusivo para a conta de cobrança.   |
| billingAccountName  | Nome da conta de cobrança.  |
| billingProfileId  | Identificador exclusivo para o perfil de faturação.   |
| billingProfileName  | Nome do perfil de faturação que está configurado para receber as notas fiscais. Os preços indicados na tabela de preços estão associados este perfil de faturação. |
| productOrderName  | Nome do plano do produto comprado. |
| serviceFamily  | Tipo de serviço do Azure. Por exemplo: Segurança de computação, análise, |
| Product  | Nome do produto a acumular os custos. Por exemplo: Básicos SQL DB vs padrão SQL DB  |
| productId  | Identificador exclusivo para o produto cuja medidor é consumido. |
| unitOfMeasure  | Identifica as unidades de medida para uma faturação para o serviço. Por exemplo, os serviços de computação são faturados por hora. |
| meterId  | Identificador exclusivo para o medidor. |
| meterName  | Nome do medidor. O medidor representa o recurso implementável de um serviço do Azure. |
| MeterCategory  | Nome da categoria de classificação para o medidor. Por exemplo, _serviços Cloud_, _Networking_, etc. |
| meterType  |  Nome do tipo de medidor. |
| MeterSubCategory  | Nome da categoria de classificação secundárias dos medidores.  |
| meterRegion  | Nome da região em que o medidor para o serviço está disponível. Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.    |
| tierId  | Identifica o escalão de preço quando aplicável. Isso funciona em conjunto com tierMinimumUnits para a definição de preços em camadas quando os preços variam com base no número de unidades consumidas.    |
| tierMinimumUnits  | Define o limite inferior do intervalo de camada para o qual os preços são definidos. Por exemplo, se o intervalo de 0 a 100, tierMinimumUnits seria 0.  |
| effectiveStartDate  | Quando entra em vigor o preço de data de início. |
| effectiveEndDate  | Data de fim do preço em vigor. |
| unitPrice  | Preço por unidade no momento da faturação (não o preço combinado eficaz) o mais específico para um nome de ordem de medidor e o produto.  Nota: O preço unitário não é o mesmo como o preço em vigor nos detalhes de utilização downloads em caso de serviços que têm preços diferenciais em escalões.  Em caso de serviços com várias camadas de preços, o preço em vigor é uma taxa combinada em todas as camadas e não mostra um preço de unidade do escalão específico. O preço combinado ou preço efetiva é o preço líquido para a distribuição de quantidade consumida em várias camadas (em que cada escalão tem um preço de unidade específica). |
| basePrice  | O preço de mercado no momento em que o cliente inicia sessão ou o preço de mercado no momento o medidor de serviço inicia se for após o início de sessão.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- [Veja e transfira o preço da sua organização](billing-ea-pricing.md)
