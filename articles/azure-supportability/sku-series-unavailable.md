---
title: Séries SKU não disponíveis | Documentos da Microsoft
description: Algumas séries SKU não estão disponíveis para a subscrição selecionada para esta região.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649136"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU não disponível
Este artigo descreve como resolver o problema de uma subscrição do Azure não ter acesso a uma região ou um SKU de VM.

## <a name="symptoms"></a>Sintomas

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Ao implementar uma máquina virtual, receberá uma das seguintes mensagens de erro:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Ao comprar instâncias reservadas de Máquina Virtual, receberá uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Ao criar um pedido de suporte para aumentar a quota de núcleos de computação, uma região ou uma família SKU não está disponível para seleção.

## <a name="solution"></a>Solução
Recomendamos primeiro que considere uma região alternativo ou SKU que atende às necessidades da sua empresa. Se não é possível encontrar uma região adequada ou SKU, criar um "gestão de subscrição" [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo os passos abaixo:


- Na página de noções básicas, selecione o tipo de problema como "Gestão de subscrições", selecione a subscrição e clique em "Seguinte".

![Painel Básico](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na página do problema, selecione o tipo de problema como "Outras questões gerais".
- Na secção de detalhes:
  - Indique se pretende implementar máquinas virtuais ou comprar instâncias reservadas de Máquina Virtual
  - Especifique a região, SKU e o número de instâncias de máquina virtual que estiver a planear implementar ou comprar


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Insira seus detalhes de contacto e clique em "Criar".

![Informações de Contacto](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Comentários
Estamos sempre abertas para comentários e sugestões! Envie-nos seus [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, pode interagir com os-nos através de [Twitter](https://twitter.com/azuresupport) ou o [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Saiba mais
[FAQ de suporte do Azure](https://azure.microsoft.com/support/faq)

