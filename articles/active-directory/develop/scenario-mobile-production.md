---
title: Prepare apis web de chamadas de aplicativos móveis para produção | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chame APIs web. (Preparar aplicativos para produção.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675962"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparar aplicativos móveis para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e fiabilidade da sua aplicação móvel antes de a transferir para a produção.

## <a name="handle-errors"></a>Processar erros

Ao preparar uma aplicação móvel para produção, podem ocorrer várias condições de erro. Os principais casos que vais tratar são falhas silenciosas e recuos à interação. Outras condições que deve considerar incluem situações sem rede, interrupções de serviço, requisitos para o consentimento administrativo e outros casos específicos do cenário.

Para cada tipo de Biblioteca de Autenticação da Microsoft (MSAL), pode encontrar código de amostra e conteúdo wiki que descreva como lidar com as condições de erro:

- [Wiki MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

Para experimentar amostras adicionais, consulte [as aplicações de desktop e cliente público móvel.](sample-v2-code.md#desktop-and-mobile-public-client-apps)