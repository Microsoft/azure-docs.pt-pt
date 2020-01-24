---
title: Mover aplicação móvel chamando APIs web para produção - plataforma de identidade Microsoft / Azure
description: Saiba como construir uma aplicação móvel que chama APIs web (mover-se para a produção)
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
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702033"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicação móvel que chama APIs web - mude para a produção

Este artigo fornece detalhes sobre como melhorar a qualidade e fiabilidade da sua aplicação antes de a transferir para a produção.

## <a name="handling-errors-in-mobile-applications"></a>Erros de manuseamento em aplicações móveis

Uma série de condições de erro podem ocorrer na sua aplicação neste momento. Os principais cenários a lidar são falhas silenciosas e recuos na interação. Outras condições que deve considerar para a produção incluem situações de não-rede, interrupções de serviço, requisitos para o consentimento da administração e outros casos específicos do cenário.

Cada biblioteca MSAL tem código de amostra e conteúdo wiki que descreve como lidar com estas condições:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Questões de mitigação e investigação

Para diagnosticar problemas na sua aplicação, ajuda a recolher dados. Para obter informações sobre o tipo de dados que pode recolher, consulte a plataforma MSAL wikis.

- Os utilizadores podem pedir ajuda quando encontram problemas. A melhor prática é capturar e armazenar temporariamente registos e fornecer um local onde os utilizadores possam carregá-los. A MSAL fornece extensões de exploração para capturar informações detalhadas sobre a autenticação.
- Se estiver disponível, ative a telemetria através do MSAL para recolher dados sobre como os utilizadores estão a iniciar sessão na sua aplicação.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Experimente amostras adicionais disponíveis a partir de [Amostras / Aplicativos de desktop e clientes públicos móveis](sample-v2-code.md#desktop-and-mobile-public-client-apps)
