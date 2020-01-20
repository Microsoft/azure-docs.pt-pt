---
title: Planos de serviço e cotas para o Azure Spring Cloud
description: Saiba mais sobre cotas de serviço e planos de serviço para o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278896"
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

Podem ser aumentados determinados limites e quotas predefinidos. Se o recurso exigir um aumento, [crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
