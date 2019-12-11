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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965174"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API Web que chama APIs da Web – mover para produção

Depois de adquirir um token para chamar APIs Web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saber mais

Agora que você conhece as noções básicas de como chamar APIs da Web de sua própria API Web, você pode estar interessado neste tutorial, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Exemplo | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API Web do ASP.NET Core 2,2, área de trabalho (WPF) | ASP.NET Core API Web de 2,2 chamando Microsoft Graph, chamada a partir de um aplicativo WPF usando a plataforma de identidade da Microsoft (v 2.0) |
