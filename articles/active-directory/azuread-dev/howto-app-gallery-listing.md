---
title: Enuprova a sua aplicação na galeria de aplicações AZure AD ! Microsoft Docs
description: Saiba como listar uma aplicação que suporta um único sign-on na galeria de aplicações Azure Ative Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 40d299049a0a1bbff81c412a5beb84f28e64ff46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706307"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar a aplicação na galeria de aplicações do Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Este artigo mostra como listar uma aplicação na galeria de aplicações Azure Ative (Azure AD), implementar um único sign-on (SSO) e gerir a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicações AD AZure?

- Os clientes encontram a melhor experiência possível de inscrição única.
- A configuração da aplicação é simples e mínima.
- Uma pesquisa rápida encontra a sua aplicação na galeria.
- Os clientes AD Azure gratuitos, básicos e premium podem utilizar esta integração.
- Os clientes mútuos obtêm um tutorial de configuração passo a passo.
- Os clientes que utilizam o Sistema de Gestão de Identidade de Domínio Cruzado[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)podem utilizar o provisionamento para a mesma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicações federadas (Open ID e SAML/WS-Fed), a aplicação deve apoiar o modelo software-as-a-service (SaaS) para ser listado na galeria de aplicações AD Azure. As aplicações da galeria da empresa devem suportar múltiplas configurações de clientes e não qualquer cliente específico.
- Para o Open ID Connect, a aplicação deve ser multitendida e o [quadro de consentimento Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve ser devidamente implementado para a aplicação. O utilizador pode enviar o pedido de inscrição para um ponto final comum para que qualquer cliente possa fornecer consentimento para a aplicação. Pode controlar o acesso do utilizador com base no ID do inquilino e na UPN do utilizador recebida no token.
- Para a SAML 2.0/WS-Fed, a sua aplicação deve ter a capacidade de fazer a integração SSO SAML/WS-Fed no modo SP ou IDP. Certifique-se de que esta capacidade está a funcionar corretamente antes de submeter o pedido.
- Para a palavra-passe SSO, certifique-se de que a sua aplicação suporta a autenticação do formulário para que a abóbada de senha possa ser feita para obter um único sinal de acesso ao funcionamento como esperado.
- Precisa de uma conta permanente para testes com pelo menos dois utilizadores registados.

**Como obter Azure AD para desenvolvedores?**

Você pode obter uma conta de teste gratuita com todas as funcionalidades premium AZure AD - 90 dias grátis e pode ser estendida desde que faça dev trabalhar com ele: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Submeter o pedido no portal

Depois de ter testado que a integração da sua aplicação funciona com a Azure AD, submeta o seu pedido de candidatura no [portal da Rede de Aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps)da Microsoft .

Se a página seguinte aparecer depois de iniciar sina, contacte a [Equipa de Integração Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de e-mail que pretende utilizar para submeter o pedido. Um endereço de e-mail de negócios como [name@yourbusiness.com](mailto:name@yourbusiness.com) é preferível. A equipa Azure AD adicionará a conta no portal da Rede de Aplicações da Microsoft.

![Mensagem de pedido de acesso no portal SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois de a conta ser adicionada, pode iniciar sôm no portal da Rede de Aplicações da Microsoft.

Se a página seguinte aparecer depois de iniciar sôm, forneça uma justificação comercial para precisar de acesso na caixa de texto. Em seguida, selecione **'Pedir'.**

  ![Caixa de justificação de negócios no portal SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

A nossa equipa analisa os detalhes e dá-lhe acesso em conformidade. Após a aprovação do seu pedido, pode iniciar sedível no portal e submeter o pedido selecionando o azulejo do **Pedido de Submissão (ISV)** na página inicial.

![Enviar azulejos do Pedido (ISV) na página inicial](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemas no login no portal

Se estiver a ver este erro durante o início de sessão, então aqui estão os detalhes sobre o assunto e é assim que pode corrigi-lo.

* Se o seu sinal de sposição foi bloqueado como mostrado abaixo:

  ![questões que resolvem aplicação na galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está a acontecer:**

O utilizador convidado é federado a um inquilino doméstico que também é um AD Azure. O utilizador convidado está em alto risco. A Microsoft não permite que utilizadores de alto risco acedam aos seus recursos. Todos os utilizadores de alto risco (empregados ou hóspedes/fornecedores) devem remediar/fechar o seu risco de aceder aos recursos da Microsoft. Para os utilizadores convidados, este risco de utilizador provém do arrendatário doméstico e a política provém do inquilino de recursos (Microsoft neste caso).
 
**Soluções seguras:**

* Os utilizadores de hóspedes registados no MFA remediam o seu próprio risco de utilizador. Isto pode ser feito pelo utilizador convidado que executa uma alteração de senha garantida ou reset https://aka.ms/sspr) (no seu inquilino de casa (isto precisa de MFA e SSPR no inquilino doméstico). A alteração ou reset de palavra-passe garantido deve ser iniciada no Ad Azure e não no pré-pré.

* Os utilizadores convidados têm os seus administradores a remediar o seu risco. Neste caso, o administrador realizará um reset de palavra-passe (geração temporária de palavra-passe). Isto não precisa de proteção de identidade. O administrador do utilizador convidado pode ir e clicar em 'Redefinir a https://aka.ms/RiskyUsers palavra-passe'.

* Os utilizadores convidados têm os seus administradores próximos /dispensam o seu risco. Mais uma vez, isto não precisa de proteção de identidade. O administrador pode ir https://aka.ms/RiskyUsers e clicar em 'Dispensar o risco do utilizador'. No entanto, a administração deve fazer as devidas diligências para garantir que se tratava de uma falsa avaliação de risco positivo antes de fechar o risco do utilizador. Caso contrário, estão a colocar os seus recursos e os recursos da Microsoft em risco, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementar SSO utilizando o protocolo da federação

Para listar uma aplicação na galeria de aplicações AZure AD, primeiro tem de implementar um dos seguintes protocolos da federação apoiados pela Azure AD. Também tem de concordar com os termos e condições da galeria de aplicações Azure. Leia os termos e condições da galeria de aplicações AZure AD [neste site.](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)

- **Ligação OpenID**: Para integrar a sua aplicação com a Azure AD utilizando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores.](v1-authentication-scenarios.md)

    ![Listagem de uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

    * Se pretender adicionar a sua aplicação à lista na galeria utilizando o OpenID Connect, selecione **OpenID Connect & OAuth 2.0** como mostrado.
    * Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** ou **WS-Fed**: Se a sua aplicação suportar SAML 2.0, pode integrá-lo diretamente com um inquilino AZure AD seguindo as [instruções para adicionar uma aplicação personalizada.](../manage-apps/view-applications-portal.md)

  ![Listando um pedido DE SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

  * Se pretender adicionar a sua candidatura à lista na galeria utilizando **SAML 2.0** ou **WS-Fed,** selecione **SAML 2.0/WS-Fed** como mostrado.

  * Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementar SSO utilizando a palavra-passe SSO

Crie uma aplicação web que tenha uma página de entrada HTML para configurar um [único sinal baseado em palavra-passe](../manage-apps/what-is-single-sign-on.md). O SSO baseado em palavras-passe, também referido como cofre de palavras-passe, permite-lhe gerir o acesso ao utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como é o caso das contas de aplicações da sua organização nas redes sociais.

![Listagem de uma aplicação SSO de palavra-passe na galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se pretender adicionar a sua aplicação à lista na galeria utilizando a palavra-passe SSO, selecione **Password SSO (UserName & Password)** como mostrado.
* Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Pedido de provisionamento de utilizadores

Acompanhe o processo apresentado na imagem seguinte para solicitar o provisionamento do utilizador.

   ![Pedido de provisionamento de utilizadores](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Para atualizar ou remover uma aplicação existente na galeria de aplicações AZure AD, primeiro tem de submeter o pedido no [portal da Rede de Aplicações.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Se tiver uma conta de trabalho ou escola, use-a para iniciar scontabilidade neste portal. Caso contrário, utilize a sua conta Microsoft, como o Outlook ou o Hotmail, para iniciar scontabilidade.

- Selecione a opção adequada como mostrado na imagem seguinte.

    ![Listagem de uma aplicação SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Para atualizar uma aplicação existente, selecione a opção adequada de acordo com o seu requisito.
    * Para remover uma aplicação existente na galeria de aplicações AD AZure, selecione **Remova a minha lista de aplicações da galeria.**
    * Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Pedidos de lista por clientes

Os clientes podem submeter um pedido de lista de uma aplicação selecionando **pedidos de App por Clientes**  >  **Enviar novo pedido.**

![Mostra o azulejo de aplicações solicitado pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Aqui está o fluxo de aplicações solicitadas pelo cliente.

![Mostra o fluxo de aplicações solicitadas pelo cliente](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Linhas cronológicas

A cronologia do processo de listagem de um pedido DE SAML 2.0 ou WS-Fed na galeria é de 7 a 10 dias úteis.

  ![Cronologia para listar uma aplicação SAML na galeria](./media/howto-app-gallery-listing/timeline.png)

A cronologia do processo de listagem de uma aplicação OpenID Connect na galeria é de 2 a 5 dias úteis.

  ![Linha do tempo para listar uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escaladas

Para qualquer escalada, envie um e-mail para a [Equipa de Integração SSO do Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) SaaSApplicationIntegrations@service.microsoft.com em , e responderemos o mais rapidamente possível.