---
title: Planos de serviço e quotas para Azure Spring Cloud
description: Conheça as quotas de serviço e planos de serviços para a Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095671"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas e Planos de Serviço para Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Todos os serviços da Azure estabelecem limites e quotas de incumprimento para recursos e funcionalidades.   A Azure Spring Cloud oferece dois níveis de preços: Básico e Standard. Vamos detalhar os limites para ambos os níveis neste artigo.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Níveis e limites de serviço da Nuvem de primavera Azure

| Recurso | Âmbito | Básica | Standard
------- | ------- | -------
vCPU | por instância de aplicação | 1 | 4
Memória | por instância de aplicação | 2 GB | 8 GB
Instâncias de serviço Azure Spring Cloud | por região por subscrição | 10 | 10
Total de instâncias de aplicações | por Azure Spring Cloud exemplo de serviço | 25 | 500
Domínios Personalizados | por Azure Spring Cloud exemplo de serviço | 0 | 25 
Volumes persistentes | por Azure Spring Cloud exemplo de serviço | 1 GB/app x 10 apps | 50 GB/app x 10 apps

> [!TIP]
> As tarifas listadas para a total de instâncias de aplicações por instância de serviço aplicam-se a aplicações/implementações em estado parado. Por favor, elimine aplicações/implementações que não sejam utilizadas.

## <a name="next-steps"></a>Passos seguintes

Alguns limites de predefinição podem ser aumentados. Se a sua configuração necessitar de um aumento, [crie um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md).
