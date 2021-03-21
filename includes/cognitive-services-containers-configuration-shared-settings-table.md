---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006883"
---
O recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Rastreia informações de faturação.|
|No|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte de telemetria [Azure Application Insights](/azure/application-insights) ao seu recipiente.|
|Yes|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Yes|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|No|[Fluente](#fluentd-settings)|Escreve registo e, opcionalmente, dados métricos para um servidor Fluentd.|
|No|HTTP Proxy|Configura um representante HTTP para fazer pedidos de saída.|
|No|[Registo](#logging-settings)|Fornece ASP.NET suporte de registo de registo do núcleo para o seu recipiente. |
|No|[Montes](#mount-settings)|Lê e escreve dados do computador anfitrião para o recipiente e do recipiente de volta para o computador anfitrião.|