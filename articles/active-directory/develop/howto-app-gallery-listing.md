---
title: Listar seu aplicativo na Galeria de aplicativos Azure Active Directory | Microsoft Docs
description: Saiba como listar um aplicativo que dá suporte ao logon único na Galeria de aplicativos Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54c51a10f950fb5381ab29968a866772dcaec78c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918015"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar a aplicação na galeria de aplicações do Azure Active Directory

Este artigo mostra como listar um aplicativo na Galeria de aplicativos Azure Active Directory (Azure AD), implementar o SSO (logon único) e gerenciar a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de aplicativos do Azure AD?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Os clientes do Azure AD gratuitos, básicos e Premium podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam o sistema para[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(gerenciamento de identidade entre domínios) podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML/WS-enalimentado), o aplicativo deve oferecer suporte ao modelo de software como serviço (SaaS) para ser listado na Galeria de aplicativos do Azure AD. Os aplicativos da Galeria empresarial devem dar suporte a várias configurações de clientes e não a nenhum cliente específico.
- Para Open ID Connect, o aplicativo deve ter multilocatário e a [estrutura de consentimento do Azure ad](consent-framework.md) deve ser implementada corretamente para o aplicativo. O usuário pode enviar a solicitação de entrada para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. Você pode controlar o acesso do usuário com base na ID do locatário e o UPN do usuário recebido no token.
- Para SAML 2.0/WS-enalimentado, seu aplicativo deve ter a capacidade de fazer a integração de SSO de SAML/WS-alimentada no modo SP ou IDP. Verifique se esse recurso está funcionando corretamente antes de enviar a solicitação.
- Para SSO de senha, certifique-se de que seu aplicativo dá suporte à autenticação de formulário para que o cofre de senha possa ser feito para que o logon único funcione conforme o esperado.
- Você precisa de uma conta permanente para testar com pelo menos dois usuários registrados.

**Como obter o Azure AD para desenvolvedores?**

Você pode obter uma conta de teste gratuita com todos os recursos premium do Azure AD-90 dias gratuitos e pode ser estendido desde que os desenvolvedores trabalhem com ele: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Depois de testar se a integração do aplicativo funciona com o Azure AD, envie sua solicitação de acesso no [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar neste portal. Caso contrário, use o conta Microsoft, como o Outlook ou hotmail, para entrar.

Se a seguinte página aparecer depois que você entrar, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de email que você deseja usar para enviar a solicitação. A equipe do Azure AD adicionará a conta no portal de rede de aplicativos da Microsoft.

![Mensagem de solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois que a conta for adicionada, você poderá entrar no portal de rede de aplicativos da Microsoft.

Se a página a seguir aparecer depois que você entrar, forneça uma justificativa de negócios para a necessidade de acesso na caixa de texto. Em seguida, selecione **solicitar acesso**.

  ![Caixa de justificativa de negócios no portal do SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nossa equipe revisa os detalhes e fornece acesso adequado. Depois que a solicitação for aprovada, você poderá entrar no portal e enviar a solicitação selecionando o bloco de **solicitação de envio (ISV)** no Home Page.

![Bloco de solicitação de envio (ISV) no home page](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemas ao fazer logon no portal

Se você estiver vendo esse erro durante o logon, aqui estão os detalhes sobre o problema e isso é como você pode corrigi-lo.

* Se sua entrada foi bloqueada, conforme mostrado abaixo:

  ![problemas de resolução de aplicativo na Galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está acontecendo:**

O usuário convidado é federado a um locatário inicial que também é um Azure AD. O usuário convidado tem um risco alto. A Microsoft não permite que usuários de alto risco acessem seus recursos. Todos os usuários de alto risco (funcionários ou convidados/fornecedores) devem corrigir/fechar seu risco para acessar os recursos da Microsoft. Para usuários convidados, esse risco de usuário é proveniente do locatário inicial e a política vem do locatário de recursos (a Microsoft, neste caso).
 
**Soluções seguras:**

* Os usuários convidados registrados do MFA recorrem seus próprios riscos de usuário. Isso pode ser feito pelo usuário convidado que executa uma alteração ou redefinição de senha protegida (https://aka.ms/sspr) em seu locatário inicial (isso precisa de MFA e SSPR no locatário inicial). A alteração ou redefinição de senha protegida deve ser iniciada no Azure AD e não no local.

* Os usuários convidados têm seus administradores para corrigir seus riscos. Nesse caso, o administrador executará uma redefinição de senha (geração de senha temporária). Isso não precisa de proteção de identidade. O administrador do usuário convidado pode ir para https://aka.ms/RiskyUsers e clicar em ' Redefinir senha '.

* Os usuários convidados têm seus administradores perto/descartam seus riscos. Novamente, isso não precisa de proteção de identidade. O administrador pode ir para https://aka.ms/RiskyUsers e clicar em ' ignorar risco do usuário '. No entanto, o administrador deve fazer a auditoria detalhada para garantir que essa foi uma avaliação de risco positiva de falsos, antes de fechar o risco do usuário. Caso contrário, eles estão colocando os recursos da Microsoft em risco, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementar o SSO usando o protocolo de Federação

Para listar um aplicativo na Galeria de aplicativos do Azure AD, primeiro você precisa implementar um dos seguintes protocolos de Federação com suporte do Azure AD. Você também precisa concordar com os termos e condições da Galeria de aplicativos do Azure AD. Leia os termos e condições da Galeria de aplicativos do Azure AD neste [site](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: para integrar seu aplicativo ao Azure ad usando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](v1-authentication-scenarios.md).

    ![Listando um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você quiser adicionar seu aplicativo para listar na Galeria usando o OpenID Connect, selecione **OpenID connect & OAuth 2,0** , conforme mostrado.
    * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** ou **WS-alimentado**: se seu aplicativo der suporte a SAML 2,0, você poderá integrá-lo diretamente com um locatário do Azure ad seguindo as [instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

  ![Listando um aplicativo SAML 2,0 ou WS-enalimentado na Galeria](./media/howto-app-gallery-listing/saml.png)

  * Se você quiser adicionar seu aplicativo para listar na Galeria usando o **saml 2,0** ou o **WS-enalimentado**, selecione **SAML 2.0/WS-alimentado** , conforme mostrado.

  * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementar o SSO usando o SSO de senha

Crie um aplicativo Web que tenha uma página de entrada HTML para configurar o [logon único baseado em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como cofre de senhas, permite que você gerencie o acesso do usuário e as senhas para aplicativos Web que não dão suporte à Federação de identidade. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização.

![Listando um aplicativo SSO de senha na Galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você quiser adicionar seu aplicativo para listar na Galeria usando o SSO de senha, selecione **SSO de senha** , conforme mostrado.
* Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Solicitação de provisionamento de usuário

Siga o processo mostrado na imagem a seguir para solicitar o provisionamento de usuário.

   ![Solicitação de provisionamento de usuário](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Para atualizar ou remover um aplicativo existente na Galeria de aplicativos do Azure AD, primeiro você precisa enviar a solicitação no [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar neste portal. Caso contrário, use o conta Microsoft, como o Outlook ou hotmail, para entrar.

- Selecione a opção apropriada, conforme mostrado na imagem a seguir.

    ![Listando um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Para atualizar um aplicativo existente, selecione a opção apropriada de acordo com seu requisito.
    * Para remover um aplicativo existente da Galeria de aplicativos do Azure AD, selecione **remover minha listagem de aplicativos da Galeria**.
    * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Listar solicitações por clientes

Os clientes podem enviar uma solicitação para listar um aplicativo selecionando **solicitações de aplicativo por clientes** > **Enviar nova solicitação**.

![Mostra o bloco aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Este é o fluxo de aplicativos solicitados pelo cliente.

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Linhas cronológicas

A linha do tempo para o processo de listagem de um aplicativo SAML 2,0 ou WS-enalimentado na galeria é de 7 a 10 dias úteis.

  ![Linha do tempo para listar um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo do OpenID Connect na galeria é de 2 a 5 dias úteis.

  ![Linha do tempo para listar um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamento, envie um email para a [equipe de integração de SSO do Azure ad](mailto:SaaSApplicationIntegrations@service.microsoft.com) em SaaSApplicationIntegrations@service.microsoft.come responderemos assim que possível.
