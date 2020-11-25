---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006883"
---
O recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Rastreia informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte de telemetria [Azure Application Insights](/azure/application-insights) ao seu recipiente.|
|Sim|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Sim|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|Não|[Fluente](#fluentd-settings)|Escreve registo e, opcionalmente, dados métricos para um servidor Fluentd.|
|Não|HTTP Proxy|Configura um representante HTTP para fazer pedidos de saída.|
|Não|[Registo](#logging-settings)|Fornece ASP.NET suporte de registo de registo do núcleo para o seu recipiente. |
|Não|[Montes](#mount-settings)|Lê e escreve dados do computador anfitrião para o recipiente e do recipiente de volta para o computador anfitrião.|