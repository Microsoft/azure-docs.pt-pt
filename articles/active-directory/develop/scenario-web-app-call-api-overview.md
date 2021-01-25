---
title: Construa uma aplicação web que chame APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação web que chame APIs web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756283"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Cenário: Uma aplicação web que chama APIs web

Saiba como construir uma aplicação web que inscreve os utilizadores na plataforma de identidade da Microsoft e, em seguida, ligue para APIs web em nome do utilizador inscrito.

## <a name="prerequisites"></a>Pré-requisitos

Este cenário pressupõe que já tenha concluído [Cenário: aplicação web que assina nos utilizadores.](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Descrição geral

Adiciona autenticação à sua aplicação web para que possa iniciar súpido e ligar para uma API web em nome do utilizador inscrito.

![Aplicação Web que chama as APIs Web](./media/scenario-webapp/web-app.svg)

As aplicações web que chamam APIs web são aplicações confidenciais do cliente.
É por isso que registam um segredo (uma senha de aplicação ou certificado) com o Azure Ative Directory (Azure AD). Este segredo é passado durante a chamada para Azure AD para obter um símbolo.

## <a name="specifics"></a>Especificidades

> [!NOTE]
> Adicionar o s-in a uma aplicação web é sobre proteger a própria aplicação web. Esta proteção é conseguida utilizando bibliotecas *de middleware,* não a Microsoft Authentication Library (MSAL). O cenário anterior, [a aplicação Web que assina nos utilizadores,](scenario-web-app-sign-user-overview.md)cobriu esse assunto.
>
> Este cenário abrange como chamar APIs web a partir de uma aplicação web. Tens de ter acesso a fichas para essas APIs web. Usas bibliotecas MSAL para adquirir estes tokens.

O desenvolvimento deste cenário envolve estas tarefas específicas:

- Durante [o registo de pedidos,](scenario-web-app-call-api-app-registration.md)deve fornecer uma resposta URI, segredo ou certificado a ser partilhado com a Azure AD. Se implementar a sua aplicação em vários locais, irá fornecer um URI de resposta para cada local.
- A configuração da [aplicação](scenario-web-app-call-api-app-configuration.md) deve fornecer as credenciais do cliente que foram partilhadas com a Azure AD durante o registo da candidatura.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [registo de aplicações.](scenario-web-app-call-api-app-registration.md)