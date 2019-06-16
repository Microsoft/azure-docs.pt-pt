---
title: Web API que chamadas de APIs web (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como compilar uma web API que chamadas downstream web APIs (passar para produção).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074759"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API Web que chama o web APIs - mover para produção

Depois de adquirir um token para chamar as APIs web, pode mover a sua aplicação para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que sabe as noções básicas de como chamar APIs web a partir do seu próprio web API, poderá estar interessado a neste tutorial, que descreve o código que utilizou para criar uma API web protegida está chamando as APIs web.

| Exemplo | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2\.2 API Web ASP.NET Core, a área de trabalho (WPF) | API da Web do ASP.NET Core 2.2 chamar o Microsoft Graph, em si chamado a partir de um aplicativo do WPF usando a plataforma de identidade da Microsoft (v2.0) |
