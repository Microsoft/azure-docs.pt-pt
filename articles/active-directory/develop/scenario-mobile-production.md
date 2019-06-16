---
title: Aplicação móvel que chamadas de APIs web (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chamadas de web APIs (passar para produção)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962350"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicação móvel que chama o web APIs - mover para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a fiabilidade da sua aplicação antes de mover para produção.

## <a name="handling-errors-in-mobile-applications"></a>Tratamento de erros em aplicativos móveis

Um número de condições de erro pode ocorrer na sua aplicação neste momento. Os principais cenários para lidar com são falhas silenciosas e fallbacks à interação. Outras condições que deve considerar para produção incluem situações de não-rede, as falhas de serviço, requisitos de consentimento de administrador e outros casos específicos de cenário.

Cada biblioteca MSAL tem conteúdo de código e wiki de exemplo que descreve como lidar com essas condições:

- [Wiki de Android de MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Mitigação e investigar problemas

Para diagnosticar problemas em seu aplicativo, ele ajuda a recolher dados. Para obter informações sobre os tipos de dados pode recolher, consulte os wikis de plataforma MSAL.

- Os utilizadores podem pedir ajuda quando eles encontram problemas. É uma prática recomendada capturar e armazenar os registos de temporariamente e fornecer uma localização onde os utilizadores podem a carregá-los. A MSAL fornece extensões de registo para capturar informações detalhadas sobre a autenticação.
- Se estiver disponível, ative a telemetria através de MSAL para recolher dados sobre como os utilizadores estão a iniciar sessão para a sua aplicação.

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
