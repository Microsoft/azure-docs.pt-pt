---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116682"
---
Este contentor tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Utilizado para controlar informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite que adicione [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor.|
|Sim|[Billing](#billing-configuration-setting)|Especifica o ponto final do URI do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que aceite a licença para o contentor.|
|Não|[Fluentd](#fluentd-settings)|Escrever o registo e, opcionalmente, dados de métrica para um servidor de Fluentd.|
|Não|Proxy de HTTP|Configure um proxy HTTP para fazer pedidos de saída.|
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Não|[Mounts](#mount-settings)|Ler e escrever dados do computador anfitrião para o contentor e do contentor para o computador anfitrião.|
