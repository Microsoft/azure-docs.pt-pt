---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484098"
---
O contêiner tem as seguintes definições de configuração:

|Necessário|Definição|Objetivo|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Rastreia informações de cobrança.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite adicionar suporte à telemetria do [insights aplicativo Azure](https://docs.microsoft.com/azure/application-insights) ao seu contêiner.|
|Sim|[Faturação](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviço no Azure.|
|Sim|[EULA](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Grava o log e, opcionalmente, os dados de métrica para um servidor Fluentd.|
|Não|Proxy HTTP|Configura um proxy HTTP para fazer solicitações de saída.|
|Não|[Logout](#logging-settings)|Fornece suporte ao log de ASP.NET Core para seu contêiner. |
|Não|[Monta](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|
