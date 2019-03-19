---
title: Compreender os termos na sua folha de preços para um contrato de cliente da Microsoft - Azure | Documentos da Microsoft
description: Saiba como ler e compreender a utilização e faturação para a sua subscrição do Azure
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880760"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Compreender os termos na sua folha de preços para um contrato de cliente da Microsoft

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se for um perfil de faturação proprietário, Contribuidor, leitor ou Gerenciador de faturas pode transferir a folha de preços da sua organização no portal do Azure. Ver [modo de exibição e a transferência de preços de sua organização](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Termos detalhados e descrições na sua folha de preços do contrato de cliente da Microsoft

A secção seguinte descreve os termos de importantes exibidos na sua folha de preços do contrato de cliente da Microsoft.

| **Nome do campo**   | **Descrição**   |
| --- | --- |
| billingAccountId  | Identificador exclusivo para a conta de cobrança.   |
| billingAccountName  | Nome da conta de cobrança.  |
| billingProfileId  | Identificador exclusivo para o perfil de faturação.   |
| billingProfileName  | Nome do perfil de faturação que está configurado para receber as notas fiscais. Os preços indicados na tabela de preços estão associados este perfil de faturação. |
| productOrderName  | Nome do plano do produto comprado. |
| serviceFamily  | Tipo de serviço do Azure. Por exemplo: Segurança de computação, análise, |
| Produto  | Nome do produto a acumular os custos. Por exemplo: Básicos SQL DB vs padrão SQL DB  |
| productId  | Identificador exclusivo para o produto cuja medidor é consumido. |
| unitOfMeasure  | Identifica as unidades de medida para uma faturação para o serviço. Por exemplo, os serviços de computação são faturados por hora. |
| meterId  | Identificador exclusivo para o medidor. |
| meterName  | Nome do medidor. O medidor representa o recurso implementável de um serviço do Azure. |
| meterCategory  | Nome da categoria de classificação para o medidor. Por exemplo, _serviços Cloud_, _Networking_, etc. |
| meterType  |  Nome do tipo de medidor. |
| meterSubCategory  | Nome da categoria de classificação secundárias dos medidores.  |
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
