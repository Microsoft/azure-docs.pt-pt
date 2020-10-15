---
title: Planos de serviço e quotas para Azure Spring Cloud
description: Conheça as quotas de serviço e planos de serviços para a Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 496f2e812a102e85fea92a535552daaaadf5f31e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093434"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas e Planos de Serviço para Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Todos os serviços da Azure estabelecem limites e quotas de incumprimento para recursos e funcionalidades.   A Azure Spring Cloud oferece dois níveis de preços: Básico e Standard. Vamos detalhar os limites para ambos os níveis neste artigo.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Níveis e limites de serviço da Nuvem de primavera Azure

| Recurso | Básica | Standard
------- | ------- | -------
vCPU | 1 por instância de serviço | 4 por instância de serviço
Memória | 2 GB por instância de serviço | 8 GB por instância de serviço
Instâncias de serviço Azure Spring Cloud por região por subscrição | 10 | 10
Total de instâncias de aplicativos por instância de serviço Azure Spring Cloud | 25 | 500
Volumes persistentes | 1 GB/app x 10 apps | 50 GB/app x 10 apps

## <a name="next-steps"></a>Passos seguintes

Alguns limites de predefinição podem ser aumentados. Se a sua configuração necessitar de um aumento, [crie um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md).