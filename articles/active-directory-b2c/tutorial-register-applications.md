---
title: Tutorial - registar uma aplicação - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como registar uma aplicação web no Azure Active Directory B2C no portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f79ecd96b15ff4ff96a3bb93061f0c9d7598191f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338162"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registar uma aplicação no Azure Active Directory B2C

Antes de sua [aplicativos](active-directory-b2c-apps.md) pode interagir com o Azure Active Directory (Azure AD) B2C, tem de estar registados num inquilino que gere. Este tutorial mostra como registar uma aplicação web com o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver criado o seu [inquilino do Azure AD B2C](tutorial-create-tenant.md), crie uma agora. Pode utilizar um inquilino existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registar uma aplicação Web

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
2. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
3. Selecione **aplicativos**e, em seguida, selecione **Add**.
4. Introduza um nome para a aplicação. Por exemplo, *webapp1*.
5. Para **incluir a aplicação web / web API** e **permitir fluxo implícito**, selecione **Sim**.
6. Para **URL de resposta**, introduza um ponto de extremidade em que o Azure AD B2C deverá devolver quaisquer tokens que a aplicação solicite. Por exemplo, pode defini-lo para escutar localmente em `https://localhost:44316` se ainda não conhece o número de porta, pode introduzir um valor de marcador de posição e alterá-la mais tarde. Para fins de teste pode defini-lo `https://jwt.ms`, que exibe o conteúdo de um token para inspeção. Para este tutorial, defina-o como `https://jwt.ms`. 

    O URL tem de começar com o esquema de resposta `https`, e responder a todos os valores de URL têm de partilhar um único domínio DNS. Por exemplo, se o aplicativo tem um URL de resposta de `https://login.contoso.com`, pode adicionar a ele como este URL `https://login.contoso.com/new`. Em alternativa, pode consultar um subdomínio DNS de `login.contoso.com`, tais como `https://new.login.contoso.com`. Se quiser ter uma aplicação com `login-east.contoso.com` e `login-west.contoso.com` como URLs de resposta, tem de adicionar esses URLs de resposta pela seguinte ordem: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Pode adicionar os dois últimos porque são subdomínios do primeiro URL de resposta, `contoso.com`.

7. Clique em **Criar**.

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se estiver aplicativo troca um código de um token, terá de criar um segredo de aplicação.

1. Selecione **chaves** e, em seguida, clique em **gerar chave**.
2. Selecione **guardar** para ver a chave. Anote o valor da **Chave da aplicação**. Utilize o valor como o segredo de aplicação no código da aplicação.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

> [!div class="nextstepaction"]
> [Criar fluxos de utilizador no Azure Active Directory B2C](tutorial-create-user-flows.md)