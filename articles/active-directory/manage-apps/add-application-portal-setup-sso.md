---
title: 'Quickstart: Configurar um único sign-on (SSO) baseado em SAML para uma aplicação no seu inquilino Azure Ative Directory (Azure AD)'
description: Este quickstart percorre o processo de criação de um único sign-on (SSO) baseado em SAML para uma aplicação no seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 44a838bdce4721d24bd0bdbb45ac7b36ceb34e71
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488374"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Configurar um único sign-on (SSO) baseado em SAML para uma aplicação no seu inquilino Azure Ative Directory (Azure AD)

Inicie com logins de utilizador simplificados, configurando um único login (SSO) para uma aplicação que adicionou ao seu inquilino Azure Ative Directory (Azure AD). Depois de configurar o SSO, os seus utilizadores podem iniciar sação numa aplicação utilizando as suas credenciais AZure AD. SSO está incluído na edição gratuita do Azure AD.

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

Depois de terminar de adicionar uma aplicação ao seu inquilino Azure AD, a página geral aparece. Se está a configurar uma aplicação que já foi adicionada, olhe para o primeiro quickstart. Acompanha-o a ver as candidaturas adicionadas ao seu inquilino.

Para configurar um único sinal de inscrição para uma aplicação:

1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende configurar para um único sôm- insi.
1. Na secção **'Gerir',** selecione **'Único's sign-on'** para abrir o painel **de inscrição único** para edição.

    > [!IMPORTANT]
    > Se a aplicação utilizar a norma OpenID Connect (OIDC) para SSO, então não verá uma única opção de acesso na navegação. Consulte o quickstart no SSO baseado em OIDC para aprender a configurar.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="A screenshot mostra a página configurar de assinatura única no portal AD Azure.":::

1. Selecione **SAML** para abrir a página de configuração SSO. Neste exemplo, a aplicação que estamos a configurar para sSO é o GitHub. Depois de configurar o GitHub, os seus utilizadores podem inscrever-se no GitHub usando as suas credenciais do seu inquilino AZure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="A screenshot mostra a página de configuração de assinatura única no GitHub.":::

1. O processo de configuração de uma aplicação para utilizar o Azure AD para SSO baseado em SAML varia consoante a aplicação. Há uma ligação com a orientação para o GitHub. Para encontrar guias para outras aplicações, consulte [Tutoriais para integrar aplicações SaaS com O Diretório Ativo Azure.](/azure/active-directory/saas-apps/)
1. Siga o guia para configurar o SSO para a aplicação. Muitas aplicações têm requisitos específicos de subscrição para a funcionalidade SSO. Por exemplo, o GitHub requer uma subscrição da Enterprise.
    > [!TIP]
    > Para saber mais sobre as opções de configuração SAML, consulte o [sign-on único baseado em Configure SAML](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="O Screenshot mostra a opção de inscrição única na subscrição enterprise da página de preços do GitHub.":::

> [!TIP]
> Pode automatizar a gestão de aplicações utilizando a API do Gráfico, ver [Gestão de aplicações do Automamate com a Microsoft Graph API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar esta série de arranque rápido, considere apagar a app para limpar o seu inquilino de teste. A eliminação da aplicação é abrangida pelo último quickstart desta série, ver [Delete uma aplicação](delete-application-portal.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a apagar uma aplicação.
> [!div class="nextstepaction"]
> [Eliminar uma aplicação](delete-application-portal.md)
