---
title: Aplicação de extensões no Azure Active Directory B2C | Documentos da Microsoft
description: Restaurar a aplicação de extensões de b2c.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d9fbceee8fb0e565df668406f09df27272318dd0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509471"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Aplicação de extensões

Quando é criado um diretório do Azure AD B2C, um aplicativo chamado `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` é automaticamente criado dentro do diretório de novo. Esta aplicação, conhecido como o **b2c-extensions-app**, é visível na *registos das aplicações*. É utilizado pelo serviço do Azure AD B2C para armazenar informações sobre utilizadores e os atributos personalizados. Se a aplicação for eliminada, do Azure AD B2C não irão funcionar corretamente e ambiente de produção será afetado.

> [!IMPORTANT]
> Não elimine a aplicação de extensões de b2c, a menos que pretende eliminar imediatamente o seu inquilino. Se o aplicativo permanece eliminado durante mais de 30 dias, informações de utilizador serão permanentemente perdidas.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verificar se a aplicação de extensões está presente

Para verificar que a aplicação de extensões de b2c está presente:

1. Dentro do seu inquilino do Azure AD B2C, clique em **todos os serviços** no menu de navegação esquerdo.
1. Procurar e abrir **registos das aplicações**.
1. Procurar uma aplicação que começa com **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Recuperar a aplicação de extensões

Caso tenha eliminado acidentalmente a aplicação de extensões de b2c, tem 30 dias para recuperá-la. Pode restaurar a aplicação com a Graph API:

1. Navegue até [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/).
1. Inicie sessão no site como um administrador global do diretório do Azure AD B2C que pretende restaurar a aplicação foi eliminada para. Este administrador global tem de ter um endereço de e-mail semelhante ao seguinte: `username@{yourTenant}.onmicrosoft.com`.
1. Emitir um HTTP GET contra o URL `https://graph.windows.net/myorganization/deletedApplications` com a api-version = 1.6. Esta operação irá listar todas as aplicações que foram eliminadas nos últimos 30 dias.
1. Localize a aplicação na lista em que o nome comece com "b2c-extensão-app" e copie o `objectid` valor da propriedade.
1. Emitir um HTTP POST contra o URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Substitua a `{OBJECTID}` parte do URL com o `objectid` do passo anterior. 

Deve agora conseguir [ver a aplicação restaurada](#verifying-that-the-extensions-app-is-present) no portal do Azure.

> [!NOTE]
> Uma aplicação só pode ser restaurada, se tiver sido eliminado durante os últimos 30 dias. Se já se passaram mais de 30 dias, os dados serão perdidos permanentemente. Para obter mais assistência, enviar um pedido de suporte.
