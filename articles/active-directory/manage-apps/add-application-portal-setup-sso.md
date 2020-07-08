---
title: 'Quickstart: Configurar um único Sign-on (SSO) para uma aplicação no seu inquilino Azure Ative Directory (Azure AD)'
description: Este quickstart percorre o processo de criação de Um Único Sign-On (SSO) para uma aplicação no seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038995"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Configurar um único Sign-on (SSO) para uma aplicação no seu inquilino Azure Ative Directory (Azure AD)

Inicie com logins de utilizador simplificados, configurando sSO para uma aplicação que adicionou ao seu inquilino AZure AD. Após a configuração do SSO, os seus utilizadores poderão iniciar súm numa aplicação utilizando as suas credenciais AZure AD. SSO está incluído na edição gratuita do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o SSO para uma aplicação que adicionou ao seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Uma aplicação que suporta uma única inscrição e que já foi pré-configurada e adicionada à galeria Azure AD. A maioria das aplicações pode usar Azure AD para um único sign-on. As aplicações na galeria AD AZure foram pré-configuradas. Se a sua aplicação não estiver listada ou for uma aplicação personalizada, ainda pode usá-la com Azure AD. Confira os tutoriais e outra documentação na tabela de conteúdos. Este quickstart foca-se em apps que foram pré-configuradas para SSO e adicionadas à galeria AZure AD pelos desenvolvedores de aplicações.
- (Opcional: Conclusão de Visualização das [suas apps).](view-applications-portal.md)
- (Opcional: Conclusão de [Adicionar uma aplicação).](add-application-portal.md)
- (Opcional: Conclusão do [Configure uma aplicação).](add-application-portal-configure.md)


>[!IMPORTANT]
>Recomendamos a utilização de um ambiente de não produção para testar os passos neste arranque rápido.


## <a name="enable-single-sign-on-for-an-app"></a>Ativar um único sinal de sômes para uma aplicação

Quando terminar de adicionar uma candidatura ao seu inquilino Azure AD, é imediatamente apresentada a página geral para o mesmo. Se está a configurar uma aplicação que já foi adicionada, então olhe para o primeiro quickstart, ele o acompanha através da visualização das aplicações adicionadas ao seu inquilino. 

Para configurar um único sinal de inscrição para uma aplicação:

1. No portal AD Azure, selecione **as aplicações Enterprise** e, em seguida, encontre e selecione a aplicação que pretende configurar para um único s-on.
2. Na secção Gerir, selecione **'Único's sign-on'** para abrir o painel de propriedades para edição.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Mostra a única página de configuração de inscrição no portal AD AZure.":::
3. Selecione SAML para abrir a página de configuração SSO. Neste exemplo, a aplicação que estamos a configurar para sSO é o GitHub. Após a configuração do GitHub, os seus utilizadores poderão inscrever-se no GitHub usando as suas credenciais do seu inquilino AZure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Mostra a única página de configuração de sign-on no GitHub.":::
4. O processo de configuração de uma aplicação para utilizar o Azure AD para SSO baseado em SAML varia consoante a aplicação. Note que há uma ligação com a orientação para o GitHub. Pode encontrar guias para outras aplicações em:https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Siga o guia para configurar o SSO para a aplicação. Muitas aplicações têm requisitos específicos de subscrição para a funcionalidade SSO. Por exemplo, o GitHub requer uma subscrição da Enterprise.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Mostra a opção de inscrição única na subscrição da Enterprise da página de preços do GitHub.":::


## <a name="next-steps"></a>Próximos passos

- [Eliminar uma aplicação](delete-application-portal.md)
