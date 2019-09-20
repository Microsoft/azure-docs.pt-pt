---
title: Compreender os termos na sua folha de preços para um Contrato de Cliente da Microsoft – Azure
description: Saiba como ler e compreender a utilização e faturação de um Contrato de Cliente da Microsoft.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490650"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Termos na sua folha de preços do Contrato de Cliente da Microsoft

Este artigo aplica-se à conta de faturação do Azure de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se for um Proprietário de perfil de faturação, Contribuidor, Leitor ou Gerente de Faturas, pode transferir a folha de preços da sua organização a partir do portal do Azure. Veja [Visualizar e transferir os preços da sua organização](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termos e descrições na sua folha de preços

A secção a seguir descreve os termos importantes mostrados na sua folha de preços do Contrato de Cliente da Microsoft.

| **Nome do campo**   | **Descrição**   |
| --- | --- |
| billingAccountId  | Identificador exclusivo da conta de faturação.   |
| billingAccountName  | Nome da conta de faturação.  |
| billingProfileId  | Identificador exclusivo do perfil de faturação.   |
| billingProfileName  | Nome do perfil de faturação configurado para receber faturas. Os preços na folha de preços estão associados a este perfil de faturação. |
| productOrderName  | Nome do plano de produto comprado. |
| serviceFamily  | Tipo de serviço do Azure. Exemplo: Computação, Análise, Segurança |
| Produto  | Nome do produto que acumula os custos. Exemplo: Base de dados SQL Básico versus Base de dados SQL Standard  |
| productId  | Identificador exclusivo do produto cujo medidor é consumido. |
| unitOfMeasure  | Identifica as unidades de medida da faturação do serviço. Por exemplo, os serviços de computação são faturados à hora. |
| meterId  | Identificador exclusivo do medidor. |
| meterName  | Nome do medidor. O medidor representa o recurso implantável de um serviço do Azure. |
| meterCategory  | Nome da categoria de classificação do medidor. Por exemplo, _Serviços Cloud_, _Rede_, etc. |
| meterType  |  Nome do tipo de medidor. |
| meterSubCategory  | Nome da categoria de subclassificação do medidor.  |
| meterRegion  | Nome da região em que o medidor do serviço está disponível. Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.    |
| tierId  | Identifica o escalão de preço quando aplicável. Funciona em conjunto com tierMinimumUnits para definir preços em camadas quando os preços variam de acordo com o número de unidades consumidas.    |
| tierMinimumUnits  | Define o limite inferior do intervalo de camadas para o qual os preços são definidos. Por exemplo, se o intervalo for de 0 a 100, tierMinimumUnits será 0.  |
| effectiveStartDate  | Data de início quando o preço entra em vigor. |
| effectiveEndDate  | Data de término do preço em vigor. |
| unitPrice  | Preço por unidade no momento da faturação (não o preço combinado em vigor) como específico a um medidor e nome de ordem de produto.  Nota: o preço por unidade não é o mesmo que o preço em vigor em transferências de detalhes de utilização em caso de serviços que têm preços diferenciais entre camadas.  No caso de serviços com preços de várias camadas, o preço em vigor é uma taxa combinada entre as camadas e não mostra um preço por unidade específico a uma camada. O preço combinado ou o preço em vigor é o preço líquido para a quantidade consumida que abrange várias camadas (em que cada camada tem um preço por unidade específico). |
| basePrice  | O preço de mercado no momento em que o cliente inicia sessão ou o preço de mercado no momento em que o medidor de serviço é iniciado, caso seja após o início de sessão.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Ver e transferir os preços da sua organização](billing-ea-pricing.md)
