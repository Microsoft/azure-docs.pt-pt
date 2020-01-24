---
title: Mover API Web chamando APIs da Web para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como mover uma API Web que chama APIs da Web para produção.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701727"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API Web que chama APIs da Web: mover para produção

Depois de adquirir um token para chamar APIs Web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saber mais

Agora que você conhece as noções básicas de como chamar APIs Web de sua própria API Web, você pode estar interessado no tutorial a seguir, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Exemplo | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API Web do ASP.NET Core 2,2, área de trabalho (WPF) | ASP.NET Core chamadas de API Web 2,2 Microsoft Graph, que você chama de um aplicativo WPF usando a plataforma de identidade da Microsoft (v 2.0). |
