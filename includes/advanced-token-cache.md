---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94628021"
---
Você pode usar a implementação de cache simbólico da MSAL para permitir que aplicações de fundo, APIs e serviços usem a cache de acesso para continuar a agir em nome dos utilizadores na sua ausência. Fazê-lo é especialmente útil se as aplicações e serviços de fundo precisarem de continuar a trabalhar em nome do utilizador depois de o utilizador ter saído da aplicação web frontal.

Hoje em dia, a maioria dos processos de fundo usam permissões de [aplicação](/graph/auth/auth-concepts#microsoft-graph-permissions) quando precisam de trabalhar com os dados de um utilizador sem que estes estejam presentes para autenticar ou reautenticar. Como as permissões de aplicação requerem frequentemente o consentimento administrativo, o que requer a elevação do privilégio, o atrito desnecessário é encontrado, uma vez que o desenvolvedor não pretendia obter permissão para além do que o utilizador inicialmente consentiu para a sua app.

Esta amostra de código no GitHub mostra como evitar este atrito não é percebido, acedendo à cache simbólica da MSAL a partir de aplicações de fundo:

 [Aceder à cache simbólica do utilizador iniciado a partir de aplicações de fundo, APIs e serviços](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)