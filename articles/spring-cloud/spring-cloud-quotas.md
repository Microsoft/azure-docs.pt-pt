---
title: Planos de serviço e cotas para o Azure Spring Cloud
description: Saiba mais sobre cotas de serviço e planos de serviço para o Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607675"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cotas e planos de serviço para o Azure Spring Cloud

Todos os serviços do Azure definem limites e cotas padrão para recursos e recursos.  Durante o período de visualização, o Azure Spring Cloud oferece apenas um plano de serviço.

Este artigo detalha as cotas de serviço oferecidas durante o período de visualização atual.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Cotas e camadas de serviço do Azure Spring Cloud

Durante o período de visualização, o Azure Spring Cloud oferece apenas uma camada de serviço.

Recurso | Montante
------- | -------
vCPU | 4
Memória | 8 GBytes
Assinatura do Azure Spring Cloud | 1
Instâncias de serviço de nuvem do Azure Spring por região por assinatura | 2
Total de instâncias de aplicativo por instância de serviço de nuvem do Azure Spring | 50
Total de instâncias do aplicativo por aplicativo Spring | 20
Volumes persistentes | 10 x 50 GBytes

Quando você chegar a uma cota, receberá um erro 400 que lê: "a cota excede o limite para a assinatura da *sua assinatura* na região da região *onde o serviço de nuvem do Azure Spring é criado*.

## <a name="next-steps"></a>Passos seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Se o recurso exigir um aumento, [crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
