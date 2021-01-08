---
title: Obtenha os pontos finais para um registo de aplicações AD Azure
titleSuffix: Microsoft identity platform
description: Como encontrar os pontos finais de autenticação para uma aplicação personalizada que está a desenvolver ou a registar-se com a Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: 2c348bd7a1738742744976c46396f62a3317dc17
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017089"
---
# <a name="how-to-discover-endpoints"></a>Como descobrir pontos finais

Pode encontrar os pontos finais de autenticação da sua aplicação no [portal Azure](https://portal.azure.com).

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Selecione **Azure Active Directory**.
1. Em **Manage**, selecione **as inscrições da App** e, em seguida, selecione **Endpoints** no menu superior.

    É apresentada a página **Endpoints,** mostrando os pontos finais de autenticação para o seu inquilino.
    
    Utilize o ponto final que corresponda ao protocolo de autenticação que está a utilizar em conjunto com o **ID da Aplicação (cliente)** para elaborar o pedido de autenticação específico da sua aplicação.

**As nuvens** nacionais (por exemplo, Azure AD China, Alemanha e Governo dos EUA) têm o seu próprio portal de registo de aplicações e pontos finais de autenticação AZure AD. Saiba mais na visão geral das [nuvens nacionais.](authentication-national-cloud.md)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os pontos finais nos diferentes ambientes Azure, consulte a visão geral das [nuvens nacionais.](authentication-national-cloud.md)
