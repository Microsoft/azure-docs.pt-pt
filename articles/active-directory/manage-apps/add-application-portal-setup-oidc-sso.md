---
title: 'Quickstart: Configurar um único sign-on (SSO) baseado no OIDC para uma aplicação no seu inquilino Azure Ative (Azure AD)'
description: Este quickstart percorre o processo de criação de um único sign-on (SSO) baseado no OIDC para uma aplicação no seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258359"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Configurar um único sign-on (SSO) baseado no OIDC para uma aplicação no seu inquilino Azure Ative (Azure AD)

Inicie com logins de utilizador simplificados, configurando um único login (SSO) para uma aplicação que adicionou ao seu inquilino Azure Ative Directory (Azure AD). Depois de configurar o SSO, os seus utilizadores podem iniciar sação numa aplicação utilizando as suas credenciais AZure AD. SSO está incluído na edição gratuita do Azure AD.

Para saber mais sobre o SSO baseado no OIDC, consulte [o sign-on único baseado no OIDC.](configure-oidc-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o SSO para uma aplicação que adicionou ao seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Uma aplicação que suporta sSO e que já estava pré-configurada e adicionada à galeria AZure AD. A maioria das aplicações pode usar Azure AD para SSO. As aplicações na galeria Azure AD estão pré-configuradas. Se a sua aplicação não estiver listada ou for uma aplicação desenvolvida sob medida, ainda pode usá-la com Azure AD. Confira os tutoriais e outra documentação na tabela de conteúdos. Este quickstart foca-se em apps pré-configuradas para SSO e adicionadas à galeria AZure AD pelos desenvolvedores de aplicações.
- Opcional: Conclusão de [Ver as suas apps.](view-applications-portal.md)
- Opcional: Conclusão de [Adicionar uma aplicação.](add-application-portal.md)
- Opcional: Conclusão de [Configurar uma aplicação.](add-application-portal-configure.md)
- Opcional: Conclusão de [atribuir utilizadores a uma aplicação.](add-application-portal-assign-users.md)

>[!IMPORTANT]
>Utilize um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="enable-single-sign-on-for-an-app"></a>Ativar um único sinal de sômes para uma aplicação

Quando adiciona uma aplicação que utiliza a norma OIDC para SSO, tem um botão de configuração. Ao selecionar o botão, aceda ao site das aplicações e complete o processo de inscrição para a aplicação. O processo de adição de uma aplicação está coberto no Add uma aplicação de quickstart no início desta série. Se está a configurar uma aplicação que já foi adicionada, olhe para o primeiro quickstart. Acompanha-o a ver as candidaturas já no seu inquilino. 

Para configurar um único sinal de inscrição para uma aplicação:

1. No início desta série, aprendeu a adicionar uma app que utilizará o seu inquilino AZURE AD para gestão de identidade. Se o desenvolvedor de aplicações usou a norma OIDC para implementar o SSO, então é-lhe apresentado um botão de inscrição ao adicionar a aplicação. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="A screenshot mostra a única opção de inscrição e o botão de inscrição." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Selecione **Iniciar sê-lo** e será levado para a página de inscrição dos desenvolvedores de aplicações. Inscreva-se usando credenciais de inscrição do Azure Ative Directory. 

   > [!IMPORTANT]
    > Se já tem uma subscrição da aplicação, então a validação dos dados do utilizador e informações de inquilino/diretório ocorrerão. Se a aplicação não conseguir verificar o utilizador, irá redirecioná-lo para se inscrever para o serviço de aplicação ou para a página de erro.

3. Após a autenticação bem sucedida, aparece um diálogo pedindo o consentimento administrativo. Selecione **Consentimento em nome da sua organização** e, em seguida, selecione **Aceitar**. Para saber mais sobre o consentimento do utilizador e da administração, consulte [o consentimento do utilizador e da administração.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="A screenshot mostra o ecrã de consentimento para uma aplicação." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. O pedido é adicionado ao seu inquilino e a página inicial do pedido aparece.


> [!TIP]
> Pode automatizar a gestão de aplicações utilizando a API do Gráfico, ver [Gestão de aplicações do Automamate com a Microsoft Graph API](/graph/application-saml-sso-configure-api).

Aqui está um vídeo que percorre detalhes adicionais de adicionar uma aplicação baseada em OIDC ao Azure AD.

Adicionar uma aplicação baseada em OIDC no Azure Ative Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar esta série de arranque rápido, considere apagar a app para limpar o seu inquilino de teste. A eliminação da aplicação é abrangida pelo último quickstart desta série, ver [Delete uma aplicação](delete-application-portal.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a apagar uma aplicação.
> [!div class="nextstepaction"]
> [Eliminar uma aplicação](delete-application-portal.md)
