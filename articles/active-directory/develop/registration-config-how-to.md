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
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82926685"
---
# <a name="how-to-discover-endpoints"></a>Como descobrir pontos finais

Pode encontrar os pontos finais de autenticação da sua aplicação no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Em **Manage**, selecione **as inscrições da App**e, em seguida, selecione **Endpoints** no menu superior.

    É apresentada a página **Endpoints,** mostrando os pontos finais de autenticação para o seu inquilino.
    
    Utilize o ponto final que corresponda ao protocolo de autenticação que está a utilizar em conjunto com o **ID da Aplicação (cliente)** para elaborar o pedido de autenticação específico da sua aplicação.

**As nuvens** nacionais (por exemplo, Azure AD China, Alemanha e Governo dos EUA) têm o seu próprio portal de registo de aplicações e pontos finais de autenticação AZure AD. Saiba mais na visão geral das [nuvens nacionais.](authentication-national-cloud.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os pontos finais nos diferentes ambientes Azure, consulte a visão geral das [nuvens nacionais.](authentication-national-cloud.md)
