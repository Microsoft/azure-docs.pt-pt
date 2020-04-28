---
title: Planos de serviço e quotas para Azure Spring Cloud
description: Conheça as quotas de serviço e planos de serviço para a Nuvem de primavera Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278896"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas e Planos de Serviço para nuvem de primavera azure

Todos os serviços Azure estabelecem limites e quotas padrão para recursos e recursos.  Durante o período de pré-visualização, a Azure Spring Cloud oferece apenas um plano de serviço.

Este artigo detalha as quotas de serviço oferecidas durante o período de pré-visualização em curso.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Níveis e quotas de serviço Azure Spring Cloud

Durante o período de pré-visualização, a Azure Spring Cloud oferece apenas um nível de serviço.

Recurso | Montante
------- | -------
vCPU | 4 por instância de serviço
Memória | 8 GBytes por instância de serviço
Casos de serviço azure Spring Cloud por região por subscrição | 10
Total de instâncias de aplicativos por exemplo de serviço Azure Spring Cloud | 500
Total de instâncias de aplicativos por aplicação primavera | 20
Volumes persistentes | 10 x 50 GBytes

Quando atingir uma quota, receberá um erro de 400 que diz: "A quota excede o limite para a subscrição da *sua subscrição* na *região onde o seu serviço Azure Spring Cloud é criado*.

## <a name="next-steps"></a>Passos seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Se o seu recurso necessitar de um aumento, [crie um pedido de apoio](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
