---
title: Planos de serviço e quotas para Azure Spring Cloud
description: Conheça as quotas de serviço e planos de serviços para a Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711880"
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
> Os limites listados para a total de instâncias de aplicações por instância de serviço aplicam-se a aplicações e implementações em qualquer estado, incluindo estado parado. Por favor, elimine aplicações ou implementações que não estejam a ser utilizadas.

## <a name="next-steps"></a>Passos seguintes

Alguns limites de predefinição podem ser aumentados. Se a sua configuração necessitar de um aumento, [crie um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md).
