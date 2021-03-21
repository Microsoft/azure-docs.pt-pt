---
title: Construa uma API web que chame APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs da web a jusante (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 376c61f6a5ba94492cac26950465c61e3d8fe4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038565"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Cenário: Uma API web que chama APIs web

Saiba o que precisa de saber para construir uma API web que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

Este cenário, no qual uma API web protegida chama a outras APIs web, baseia-se no [Cenário: API web protegida](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Descrição geral

- Um cliente de aplicação web, desktop, móvel ou de uma página (não representado no diagrama que o acompanha) chama uma API web protegida e fornece um símbolo portador da Web Token (JWT) JSON no seu cabeçalho HTTP "Autorização".
- A API web protegida valida o token e utiliza o método da Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` para solicitar outro token do Azure Ative Directory (AZure AD) para que a API web protegida possa ligar para uma segunda API web, ou API web a jusante, em nome do utilizador. `AcquireTokenOnBehalfOf` refresca o símbolo quando necessário.
![Diagrama de uma API web chamando uma API web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificidades

A parte de registo de aplicações relacionada com permissões de API é clássica. A configuração da aplicação envolve a utilização do fluxo OAuth 2.0 On-Behalf-Of para trocar o token do portador JWT contra um símbolo para uma API a jusante. Este token é adicionado à cache simbólica, onde está disponível nos controladores da API web, e pode então adquirir um símbolo silenciosamente para chamar APIs a jusante.

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [registo de aplicações.](scenario-web-api-call-api-app-registration.md)
