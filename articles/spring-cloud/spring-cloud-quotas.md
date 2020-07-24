---
title: Planos de serviço e quotas para Azure Spring Cloud
description: Conheça as quotas de serviço e planos de serviços para a Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089469"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas e Planos de Serviço para Azure Spring Cloud

Todos os serviços da Azure estabelecem limites e quotas de incumprimento para recursos e funcionalidades.   A Azure Spring Cloud oferece dois níveis de preços: Básico e Standard. Vamos detalhar os limites para ambos os níveis neste artigo.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Níveis e limites de serviço da Nuvem de primavera Azure

| Recurso | Básica | Standard
------- | ------- | -------
vCPU | 1 por instância de serviço | 4 por instância de serviço
Memória | 2 GB por instância de serviço | 8 GB por instância de serviço
Instâncias de serviço Azure Spring Cloud por região por subscrição | 10 | 10
Total de instâncias de aplicativos por instância de serviço Azure Spring Cloud | 25 | 500
Volumes persistentes | 1 GB/app x 10 apps | 50 GB/app x 10 apps


Durante o período de pré-visualização, a Azure Spring Cloud oferece apenas um nível de serviço. Quando atingir um limite, receberá um erro de 400 que diz: "A quota excede o limite de subscrição da *sua subscrição* na *região onde o seu serviço Azure Spring Cloud é criado.*

## <a name="next-steps"></a>Passos seguintes

Alguns limites de predefinição podem ser aumentados. Se a sua configuração necessitar de um aumento, [crie um pedido de apoio](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
