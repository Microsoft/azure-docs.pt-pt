---
title: Move web API chamando APIs web para a produção - plataforma de identidade Microsoft / Azure
description: Aprenda a mover uma API web que chama APIs web para a produção.
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
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885060"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API web que chama APIs web: Mover-se para a produção

Depois de adquirir um token para ligar para a Web APIs, pode mover a sua aplicação para a produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que sabe o básico de como chamar APIs web da sua própria Web API, você pode estar interessado no seguinte tutorial, que descreve o código que é usado para construir uma API web protegida que chama APIs web.

| Sample | Plataforma | Descrição |
|--------|----------|-------------|
| [active-directy-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API chama o Microsoft Graph, que você chama de uma aplicação WPF usando a plataforma de identidade microsoft (v2.0). |
