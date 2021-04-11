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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675877"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API web que chama APIs web: Mover para a produção

Depois de ter adquirido um símbolo para chamar APIs web, aqui ficam algumas coisas a considerar ao mover a sua aplicação para a produção.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que conhece os fundamentos de como chamar APIs web da sua própria API web, pode estar interessado no seguinte tutorial, que descreve o código que é usado para construir uma API web protegida que chama APIs web.

| Sample | Plataforma | Description |
|--------|----------|-------------|
| [active-directório-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capítulo 1 | ASP.NET Core web API, Desktop (WPF) | ASP.NET Core web API chama o Microsoft Graph, que você chama a partir de uma aplicação WPF usando a plataforma de identidade Microsoft. |
