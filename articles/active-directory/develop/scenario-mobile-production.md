---
title: Prepare APIs web de chamada de aplicativomóvel para produção Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma aplicação móvel que chama APIs web. (Prepare aplicativos para produção.)
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
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882544"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparar aplicativos móveis para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e fiabilidade da sua aplicação móvel antes de a mover para a produção.

## <a name="handle-errors"></a>Processar erros

Ao preparar uma aplicação móvel para produção, podem ocorrer várias condições de erro. Os principais casos que vai lidar são falhas silenciosas e recuos na interação. Outras condições que deve considerar incluem situações de não-rede, interrupções de serviço, requisitos para o consentimento da administração e outros casos específicos do cenário.

Para cada tipo de Biblioteca de Autenticação da Microsoft (MSAL), pode encontrar código de amostra e conteúdo wiki que descreva como lidar com as condições de erro:

- [Wiki Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Mitigar e investigar questões

Para melhor diagnosticar problemas na sua aplicação, recolha dados. Para obter informações sobre o tipo de dados que pode recolher, consulte [o Registo nas aplicações MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Aqui ficam algumas sugestões para a recolha de dados:

- Os utilizadores podem pedir ajuda quando têm problemas. A melhor prática é capturar e armazenar temporariamente registos. Forneça um local onde os utilizadores possam fazer o upload dos registos. A MSAL fornece extensões de exploração para capturar informações detalhadas sobre a autenticação.

- Se a telemetria estiver disponível, ative-a através do MSAL para recolher dados sobre como os utilizadores se inscrevem na sua aplicação.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Para experimentar amostras adicionais, consulte aplicações de [desktop e clientes públicos móveis.](sample-v2-code.md#desktop-and-mobile-public-client-apps)
