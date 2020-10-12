---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73484098"
---
O recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Rastreia informações de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente.|
|Sim|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Sim|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|Não|[Fluente](#fluentd-settings)|Escreve registo e, opcionalmente, dados métricos para um servidor Fluentd.|
|Não|HTTP Proxy|Configura um representante HTTP para fazer pedidos de saída.|
|Não|[Registo](#logging-settings)|Fornece ASP.NET suporte de registo de registo do núcleo para o seu recipiente. |
|Não|[Montes](#mount-settings)|Lê e escreve dados do computador anfitrião para o recipiente e do recipiente de volta para o computador anfitrião.|
