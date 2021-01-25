---
title: Mover a API web chamando APIs web para | de produção Rio Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753344"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API web que chama APIs web: Mover para a produção

Depois de adquirir um token para chamar APIs web, pode mover a sua app para a produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saber mais

Agora que conhece os fundamentos de como chamar APIs web da sua própria API web, pode estar interessado no seguinte tutorial, que descreve o código que é usado para construir uma API web protegida que chama APIs web.

| Sample | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directório-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capítulo 1 | ASP.NET Core web API, Desktop (WPF) | ASP.NET Core web API chama o Microsoft Graph, que você chama a partir de uma aplicação WPF usando a plataforma de identidade Microsoft. |
