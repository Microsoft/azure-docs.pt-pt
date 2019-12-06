---
title: Planos de serviço e cotas para o Azure Spring Cloud
description: Saiba mais sobre cotas de serviço e planos de serviço para o Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851558"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cotas e planos de serviço para o Azure Spring Cloud

Todos os serviços do Azure definem limites e cotas padrão para recursos e recursos.  Durante o período de visualização, o Azure Spring Cloud oferece apenas um plano de serviço.

Este artigo detalha as cotas de serviço oferecidas durante o período de visualização atual.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Cotas e camadas de serviço do Azure Spring Cloud

Durante o período de visualização, o Azure Spring Cloud oferece apenas uma camada de serviço.

Recurso | Montante
------- | -------
vCPU | 4 por instância de serviço
Memória | 8 GBytes por instância de serviço
Instâncias de serviço de nuvem do Azure Spring por região por assinatura | 10
Total de instâncias de aplicativo por instância de serviço de nuvem do Azure Spring | 500
Total de instâncias do aplicativo por aplicativo Spring | 20
Volumes persistentes | 10 x 50 GBytes

Quando você chegar a uma cota, receberá um erro 400 que lê: "a cota excede o limite para a assinatura da *sua assinatura* na região da região *onde o serviço de nuvem do Azure Spring é criado*.

## <a name="next-steps"></a>Passos seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Se o recurso exigir um aumento, [crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
