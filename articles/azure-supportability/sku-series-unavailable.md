---
title: Série de SKUs não disponível | Microsoft Docs
description: Algumas séries de SKU não estão disponíveis para a assinatura selecionada para esta região.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249050"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU indisponível
Este artigo descreve como resolver o problema de uma assinatura do Azure que não tem acesso a uma região ou SKU de VM.

## <a name="symptoms"></a>Sintomas

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Ao implantar uma máquina virtual, você recebe uma das seguintes mensagens de erro:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Ao comprar instâncias de máquinas virtuais reservadas, você receberá uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Ao criar uma solicitação de suporte para aumentar a cota de núcleo de computação, uma região ou uma família de SKUs não está disponível para seleção.

## <a name="solution"></a>Solução
Primeiro, recomendamos que você considere uma região alternativa ou SKU que atenda às suas necessidades de negócios. Se não for possível encontrar uma região ou SKU adequada, crie uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de "gerenciamento de assinatura" seguindo as etapas abaixo:


- Na página noções básicas, selecione tipo de problema como "gerenciamento de assinatura", selecione a assinatura e clique em "Avançar".

![Painel Básico](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na página problema, selecione o tipo de problema como "outras perguntas gerais".
- Na seção detalhes:
  - Indique se você está procurando implantar máquinas virtuais ou comprar instâncias de máquinas virtuais reservadas
  - Especifique a região, a SKU e o número de instâncias de máquina virtual que você está planejando implantar ou comprar


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Insira seus detalhes de contato e clique em "criar".

![Informações de Contacto](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Comentários
Estamos sempre abertos para comentários e sugestões! Envie-nos suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode entrar em contato conosco por meio do [Twitter](https://twitter.com/azuresupport) ou dos [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Saber mais
[Perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq)

