---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484098"
---
O recipiente tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Rastreia a informação de faturação.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte de telemetria [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao seu recipiente.|
|Sim|[Faturação](#billing-configuration-setting)|Especifica o ponto final URI do recurso de serviço no Azure.|
|Sim|[Eula](#eula-setting)| Indica que aceitou a licença para o contentor.|
|Não|[Fluente](#fluentd-settings)|Escreve registo e, opcionalmente, dados métricos para um servidor Fluente.|
|Não|HTTP Proxy|Configura um proxy HTTP para fazer pedidos de saída.|
|Não|[Registo](#logging-settings)|Fornece ASP.NET suporte de exploração madeireira Core para o seu recipiente. |
|Não|[Montes](#mount-settings)|Lê e escreve dados do computador hospedeiro para o recipiente e do recipiente de volta para o computador hospedeiro.|
