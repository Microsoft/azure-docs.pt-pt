---
title: Configurar pontos de extremidade | Microsoft Docs
description: Como localizar os pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6547266b92cbeee2754871d4cdca7a2de261fc8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325357"
---
# <a name="how-to-configure-endpoints"></a>Como configurar pontos de extremidade

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [portal do Azure](https://portal.azure.com).

-   Navegue para o [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory**.

-   Clique em **registros do aplicativo** e escolha pontos de **extremidade**.

-   Isso abre a página **pontos de extremidade** , que lista todos os pontos de extremidade de autenticação do seu locatário.

-   Use o ponto de extremidade específico para o protocolo de autenticação que você está usando, em conjunto com a ID do aplicativo para criar a solicitação de autenticação específica para seu aplicativo.

## <a name="next-steps"></a>Passos Seguintes
[Guia para programadores do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
