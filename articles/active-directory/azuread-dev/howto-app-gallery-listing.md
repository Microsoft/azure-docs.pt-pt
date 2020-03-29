---
title: Lista a sua aplicação na galeria de aplicações da AD Azure [ Microsoft Docs
description: Saiba como listar uma aplicação que suporta um único sign-on na galeria de aplicações do Azure Ative Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154972"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar a aplicação na galeria de aplicações do Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Este artigo mostra como listar uma aplicação na galeria de aplicações Azure Ative Directory (Azure AD), implementar um único sign-on (SSO) e gerir a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicações da AD Azure?

- Os clientes encontram a melhor experiência de inscrição individual possível.
- A configuração da aplicação é simples e mínima.
- Uma pesquisa rápida encontra a sua aplicação na galeria.
- Os clientes ada toda a AD Gratuito, Básico e Premium Azure podem utilizar esta integração.
- Os clientes mútuos obtêm um tutorial de configuração passo a passo.
- Os clientes que utilizam o Sistema de Gestão de Identidade de Domínio Transversal[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)podem utilizar o provisionamento para a mesma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicações federadas (Open ID e SAML/WS-Fed), a aplicação deve apoiar o modelo software-as-a-service (SaaS) para ser listado na galeria de aplicações Azure AD. As aplicações da galeria da empresa devem suportar múltiplas configurações de clientes e não qualquer cliente específico.
- Para o Open ID Connect, a aplicação deve ser multiarrendada e o quadro de consentimento da [AD Azure](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve ser corretamente implementado para a aplicação. O utilizador pode enviar o pedido de sessão para um ponto final comum para que qualquer cliente possa dar o seu consentimento à aplicação. Pode controlar o acesso do utilizador com base no ID do inquilino e na UPN do utilizador recebida no token.
- Para a SAML 2.0/WS-Fed, a sua aplicação deve ter a capacidade de fazer a integração SAML/WS-Fed SSO no modo SP ou IDP. Certifique-se de que esta capacidade está a funcionar corretamente antes de submeter o pedido.
- Para obter uma senha SSO, certifique-se de que o suporte da sua aplicação é autenticação para que a abóbada de senha possa ser feita para obter um único sinal para funcionar como esperado.
- Precisa de uma conta permanente para testes com pelo menos dois utilizadores registados.

**Como obter anúncio sacana do Azure para programadores?**

Você pode obter uma conta de teste gratuita com todas as funcionalidades premium Azure AD - 90 dias grátis e pode ser estendida desde que trabalhe com ele:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Submeter o pedido no portal

Depois de ter testado que a integração da sua aplicação funciona com a Azure AD, submeta o seu pedido de acesso no portal da Rede de [Aplicações.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Se tiver uma conta do Office 365, use-a para iniciar sessão neste portal. Caso contrário, utilize a sua conta Microsoft, como o Outlook ou o Hotmail, para iniciar sessão.

Se a página seguinte aparecer após a sua inscrição, contacte a Equipa de [Integração SSO Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de e-mail que pretende utilizar para submeter o pedido. A equipa da Azure AD irá adicionar a conta no portal da Microsoft Application Network.

![Mensagem de pedido de acesso no portal SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois de adicionada a conta, pode iniciar sessão no portal da Microsoft Application Network.

Se a página seguinte aparecer após a sua inscrição, forneça uma justificação de negócio para necessitar de acesso na caixa de texto. Em seguida, selecione Acesso ao **Pedido**.

  ![Caixa de justificação de negócios no portal SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

A nossa equipa analisa os detalhes e dá-lhe acesso em conformidade. Após a aprovação do seu pedido, pode iniciar sessão no portal e submeter o pedido selecionando o azulejo De **enviar (ISV)** na página inicial.

![Enviar Pedido (ISV) azulejo na página inicial](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Questões sobre a exploração de login no portal

Se está a ver este erro durante o login, então aqui estão os detalhes sobre o assunto e é assim que pode corrigi-lo.

* Se o seu sign-in foi bloqueado como mostrado abaixo:

  ![questões que resolvem a aplicação na galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está a acontecer:**

O utilizador convidado é federado a um inquilino doméstico que também é um Azure AD. O utilizador convidado está em alto risco. A Microsoft não permite que os utilizadores de alto risco acedam aos seus recursos. Todos os utilizadores de alto risco (funcionários ou hóspedes/fornecedores) devem remediar/fechar o risco de aceder aos recursos da Microsoft. Para os utilizadores convidados, este risco de utilizador vem do inquilino doméstico e a política vem do inquilino de recursos (Microsoft neste caso).
 
**Soluções seguras:**

* Os utilizadores de hóspedes registados pela MFA remediam o seu próprio risco de utilizador. Isto pode ser feito pelo utilizador convidado que executahttps://aka.ms/sspr) uma alteração ou reset de senha segura (no seu inquilino doméstico (isto precisa de MFA e SSPR no inquilino da casa). A alteração ou reset de palavra-passe segura deve ser iniciada em Azure AD e não on-prem.

* Os utilizadores convidados têm os seus administradores a remediar o seu risco. Neste caso, o administrador realizará um reset de palavra-passe (geração temporária de passwords). Isto não precisa de proteção contra a identidade. O administrador do utilizador convidado https://aka.ms/RiskyUsers pode ir e clicar em 'Reset password'.

* Os utilizadores convidados têm os seus administradores próximos / descartam o seu risco. Mais uma vez, isto não precisa de proteção contra a identidade. O administrador pode https://aka.ms/RiskyUsers ir e clicar em 'Descartar o risco do utilizador'. No entanto, o administrador deve fazer as devidas diligências para garantir que se tratava de uma falsa avaliação de risco positiva antes de fechar o risco do utilizador. Caso contrário, estão a colocar em risco os seus recursos e os recursos da Microsoft, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se tiver algum problema com acesso, contacte a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementar SSO utilizando o protocolo da federação

Para listar uma aplicação na galeria de aplicações Azure AD, primeiro é necessário implementar um dos seguintes protocolos da federação apoiados pela Azure AD. Também precisa de concordar com os termos e condições da galeria de aplicações da AD Azure. Leia os termos e condições da galeria de aplicações da AD Azure [neste site.](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)

- **OpenID Connect**: Para integrar a sua aplicação com o Azure AD utilizando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](v1-authentication-scenarios.md).

    ![Listando uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

    * Se pretender adicionar a sua aplicação à lista na galeria utilizando o OpenID Connect, selecione **OpenID Connect & OAuth 2.0,** como mostrado.
    * Se tiver algum problema com acesso, contacte a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** ou **WS-Fed**: Se a sua aplicação suportar o SAML 2.0, pode integrá-la diretamente com um inquilino DaD Azure seguindo as [instruções para adicionar uma aplicação personalizada](../active-directory-saas-custom-apps.md).

  ![Listando uma aplicação SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

  * Se quiser adicionar a sua candidatura à lista na galeria utilizando **o SAML 2.0** ou **o WS-Fed,** selecione **SAML 2.0/WS-Fed** como mostrado.

  * Se tiver algum problema com acesso, contacte a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementar SSO utilizando a palavra-passe SSO

Crie uma aplicação web que tenha uma página de início de sessão HTML para configurar o [único sign-on baseado em palavra-passe](../manage-apps/what-is-single-sign-on.md). O SSO baseado em palavra-passe, também referido como abóbada de senhas, permite-lhe gerir o acesso do utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como nas contas de aplicações da sua organização nas redes sociais.

![Listando uma aplicação SSO de senha na galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se pretender adicionar a sua aplicação à lista na galeria utilizando a palavra-passe SSO, selecione **Password SSO** como mostrado.
* Se tiver algum problema com acesso, contacte a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Pedido de prestação de utilizadores

Siga o processo mostrado na seguinte imagem para solicitar o fornecimento do utilizador.

   ![Pedido de prestação de utilizadores](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Para atualizar ou remover uma aplicação existente na galeria de aplicações Azure AD, primeiro precisa de submeter o pedido no portal da Rede de [Aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se tiver uma conta do Office 365, use-a para iniciar sessão neste portal. Caso contrário, utilize a sua conta Microsoft, como o Outlook ou o Hotmail, para iniciar sessão.

- Selecione a opção adequada como mostrado na seguinte imagem.

    ![Listando uma aplicação SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Para atualizar uma aplicação existente, selecione a opção adequada de acordo com o seu requisito.
    * Para remover uma aplicação existente da galeria de aplicações Azure AD, **selecione Remover a minha listagem de aplicações da galeria**.
    * Se tiver algum problema com acesso, contacte a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Pedidos de lista por clientes

Os clientes podem submeter um pedido de lista de uma aplicação selecionando pedidos de **App por Clientes** > **Enviar novo pedido**.

![Mostra o azulejo de apps solicitado pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Aqui está o fluxo de aplicações solicitadas pelo cliente.

![Mostra o fluxo de aplicações solicitadas pelo cliente](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Linhas cronológicas

A cronologia do processo de listagem de uma aplicação SAML 2.0 ou WS-Fed na galeria é de 7 a 10 dias úteis.

  ![Cronologia para a listagem de uma aplicação SAML na galeria](./media/howto-app-gallery-listing/timeline.png)

A cronologia do processo de listagem de uma aplicação OpenID Connect na galeria é de 2 a 5 dias úteis.

  ![Linha do tempo para listar uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escaladas

Para quaisquer escaladas, envie e-mail para a SaaSApplicationIntegrations@service.microsoft.comEquipa de [Integração SSO da AD Azure](mailto:SaaSApplicationIntegrations@service.microsoft.com) em , e responderemos o mais rápido possível.
