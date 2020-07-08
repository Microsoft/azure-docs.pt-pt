---
title: Mover a API web chamando APIs web para a produção - plataforma de identidade microsoft / Rio Azure
description: Saiba como mover uma API web que chama APIs web à produção.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537156"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API web que chama APIs web: Mover para a produção

Depois de adquirir um token para chamar APIs web, pode mover a sua app para a produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que conhece os fundamentos de como chamar APIs web da sua própria API web, pode estar interessado no seguinte tutorial, que descreve o código que é usado para construir uma API web protegida que chama APIs web.

| Sample | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directório-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 web API, Desktop (WPF) | ASP.NET Core 2.2 web API chama o Microsoft Graph, que você chama a partir de uma aplicação WPF usando a plataforma de identidade Microsoft (v2.0). |
