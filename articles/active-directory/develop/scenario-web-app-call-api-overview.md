---
title: Construa uma aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como construir uma aplicação web que chama APIs web (visão geral)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617171"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Cenário: Uma aplicação web que chama APIs web

Saiba como construir uma aplicação web que institu os utilizadores na plataforma de identidade da Microsoft e, em seguida, ligue para a web APIs em nome do utilizador inscrito.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Este cenário pressupõe que já passou pelo seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação web que inicia a sessão de utilizadores](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Descrição geral

Adiciona autenticação à sua aplicação web para que possa iniciar sessão de utilizadores e ligar para uma API web em nome do utilizador inscrito.

![Aplicação Web que chama as APIs Web](./media/scenario-webapp/web-app.svg)

Aplicações web que ligam para apis web são aplicações confidenciais do cliente.
É por isso que registam um segredo (uma palavra-passe de candidatura ou certificado) com o Azure Ative Directory (Azure AD). Este segredo é passado durante a chamada para a AD Azure para obter um símbolo.

## <a name="specifics"></a>Especificidades

> [!NOTE]
> Adicionar sessão a uma aplicação web é proteger a própria aplicação web. Esta proteção é conseguida utilizando bibliotecas *de middleware,* e não a Microsoft Authentication Library (MSAL). O cenário anterior, [aplicação Web que assina nos utilizadores,](scenario-web-app-sign-user-overview.md)cobria esse assunto.
>
> Este cenário abrange como chamar APIs web a partir de uma aplicação web. Tens de ter fichas de acesso para essas APIs web. Usa bibliotecas MSAL para adquirir estes tokens.

O desenvolvimento para este cenário envolve estas tarefas específicas:

- Durante o registo da [candidatura,](scenario-web-app-call-api-app-registration.md)deve fornecer uma resposta URI, segredo ou certificado a ser partilhado com a Azure AD. Se implementar a sua aplicação em vários locais, irá fornecer esta informação para cada local.
- A configuração da [aplicação](scenario-web-app-call-api-app-configuration.md) deve fornecer as credenciais do cliente que foram partilhadas com a AD Azure durante o registo da candidatura.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma aplicação web que chama APIs web: Registo de aplicações](scenario-web-app-call-api-app-registration.md)
