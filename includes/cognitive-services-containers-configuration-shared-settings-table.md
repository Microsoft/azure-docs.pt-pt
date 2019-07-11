---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 46376477aae9f94c1c8f6e1dd6bc718d213cc373
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712568"
---
O contentor tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Faixas de informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite a adição [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights) suporte de telemetria ao seu contentor.|
|Sim|[Billing](#billing-configuration-setting)|Especifica o ponto final do URI do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que aceite a licença para o contentor.|
|Não|[Fluentd](#fluentd-settings)|Registo de escritas e, opcionalmente, dados de métrica para um servidor de Fluentd.|
|Não|Proxy de HTTP|Configura um proxy HTTP para fazer pedidos de saída.|
|Não|[Logging](#logging-settings)|Fornece suporte de registo do ASP.NET Core para o seu contentor. |
|Não|[Mounts](#mount-settings)|Lê e escreve dados do computador anfitrião para o contentor e para o contentor de volta para o computador anfitrião.|
