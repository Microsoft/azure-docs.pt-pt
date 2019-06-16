---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: 4f9300ab1d688e1f5043f5b919e70c5a36c7c0e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063981"
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
