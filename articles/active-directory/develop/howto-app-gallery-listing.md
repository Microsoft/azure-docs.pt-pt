---
title: Listar seu aplicativo na Galeria de aplicativos Azure Active Directory | Microsoft Docs
description: Saiba como listar um aplicativo que dá suporte ao logon único na Galeria de aplicativos Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e1b0ea2f808c982a587392edbe57eb75c532ee2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324713"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Como: Listar a aplicação na galeria de aplicações do Azure Active Directory

Este artigo mostra como listar um aplicativo na Galeria de aplicativos do Azure AD, implementar SSO (logon único) e gerenciar a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de aplicativos do Azure AD?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Os clientes do Azure AD gratuitos, básicos e Premium podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam o SCIM podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML/WS-alimentado), o aplicativo deve dar suporte ao modelo de SaaS para ser listado na galeria do Azure AD. Os aplicativos da Galeria empresarial devem dar suporte a várias configurações de clientes e não a nenhum cliente específico.

- Para o Open ID Connect, o aplicativo deve ser multilocatário e a [estrutura de consentimento do Azure ad](consent-framework.md) deve ser implementada corretamente para o aplicativo. O usuário pode enviar a solicitação de logon para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. Você pode controlar o acesso do usuário com base na ID do locatário e o UPN do usuário recebido no token.

- Para SAML 2.0/WS-enalimentado, seu aplicativo precisa ter a capacidade de fazer a integração de SSO de SAML/WS-alimentada no modo SP ou IDP. Verifique se isso está funcionando corretamente antes de enviar a solicitação.

- Para SSO de senha, verifique se o aplicativo dá suporte à autenticação de formulário para que o cofre de senha possa ser feito para que o logon único funcione conforme o esperado.

- Para solicitações automáticas de provisionamento de usuário, o aplicativo deve ser listado na galeria com o recurso de logon único habilitado usando SAML 2.0/WS-enalimentado. Você pode solicitar o SSO e o provisionamento de usuário juntos no portal, se ele ainda não estiver listado.

>[!NOTE]
>Estamos executando com alto número de solicitações de conector SCIM, portanto, vamos parar de fazer novas solicitações em nosso portal. Aguarde suas solicitações até um aviso adicional. Nós desculpasmos esse atraso e qualquer inconveniente que isso possa ter causado.

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Depois de testar se a integração do aplicativo funciona com o Azure AD, envie sua solicitação de acesso em nosso [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar neste portal. Caso contrário, use o conta Microsoft (como o Outlook ou hotmail) para entrar.

Se a página a seguir aparecer depois de entrar, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e forneça a conta de email que você deseja usar para enviar a solicitação. Em seguida, a equipe do Azure AD adicionará a conta no portal de rede de aplicativos da Microsoft.

![Solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois que a conta for adicionada, você poderá entrar no portal de rede de aplicativos da Microsoft.

E, se a página a seguir aparecer depois de entrar, forneça uma justificativa de negócios para a necessidade de acesso na caixa de texto e selecione **solicitar acesso**.

  ![Solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nossa equipe revisa os detalhes e fornece acesso adequado. Depois que sua solicitação for aprovada, você poderá entrar no portal e enviar a solicitação clicando no bloco de **requisição Enviar solicitação (ISV)** do Home Page.

![Home Page do portal do SharePoint](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Se você tiver problemas com relação ao acesso, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementando o SSO usando o protocolo de Federação

Para listar um aplicativo na Galeria de aplicativos do Azure AD, primeiro você precisa implementar um dos seguintes protocolos de Federação com suporte do Azure AD e concordar com os termos e condições da Galeria de aplicativos do Azure AD. Leia os termos e condições da Galeria de aplicativos do Azure AD [aqui](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Para integrar seu aplicativo com o Azure AD usando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](authentication-scenarios.md).

    ![Linha do tempo de listagem do aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você quiser adicionar seu aplicativo para listar na Galeria usando o OpenID Connect, selecione **OpenID connect & OAuth 2,0** como acima.
    * Se você tiver problemas com relação ao acesso, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2,0** ou **WS-alimentado**: Se seu aplicativo der suporte ao SAML 2,0, você poderá integrá-lo diretamente a um locatário do Azure AD usando as [instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

  ![Linha do tempo de listagem de SAML 2,0 ou aplicativo WS-enalimentado na Galeria](./media/howto-app-gallery-listing/saml.png)

  * Se você quiser adicionar seu aplicativo para listar na Galeria usando **saml 2,0** ou **WS-** enalimentado, selecione **SAML 2.0/WS-alimentado** como acima.
  * Se você tiver problemas com relação ao acesso, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementando o SSO usando o SSO de senha

Crie um aplicativo Web que tenha uma página de entrada HTML para configurar o [logon único baseado em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como cofre de senhas, permite que você gerencie o acesso do usuário e as senhas para aplicativos Web que não dão suporte à Federação de identidade. Ele também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização.

![Linha do tempo de listagem do aplicativo de SSO de senha na Galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você quiser adicionar seu aplicativo para listar na Galeria usando o SSO de senha, selecione **SSO de senha** como acima.
* Se você tiver problemas com relação ao acesso, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Atualizar/remover listagem existente

Para atualizar ou remover um aplicativo existente na Galeria de aplicativos do Azure AD, primeiro você precisa enviar a solicitação no [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar neste portal. Caso contrário, use o conta Microsoft (como o Outlook ou hotmail) para entrar.

- Selecione a opção apropriada, conforme mostrado na imagem a seguir:

    ![Linha do tempo de listagem do aplicativo SAML na Galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Se você quiser atualizar um aplicativo existente, selecione **Atualizar listagem de aplicativos existentes**.
    * Se você quiser remover um aplicativo existente da galeria do Azure AD, selecione **remover listagem de aplicativos existentes**.
    * Se você tiver problemas com relação ao acesso, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Listando solicitações por clientes

Os clientes podem enviar a solicitação de listagem de um aplicativo clicando em **solicitações de aplicativo por clientes** -> **Enviar nova solicitação**.

![Mostra o bloco aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Abaixo está o fluxo de aplicativos solicitados pelo cliente-

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Linhas

A linha do tempo para o processo de listagem de um aplicativo SAML 2,0 ou WS-enalimentado na galeria é de 7-10 dias úteis.

   ![Linha do tempo de listagem do aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo OpenID Connect na galeria é de 2-5 dias úteis.

   ![Linha do tempo de listagem do aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamento, envie um email para a [equipe de integração de SSO do Azure ad](mailto:SaaSApplicationIntegrations@service.microsoft.com) que é SaaSApplicationIntegrations@service.microsoft.com e responderemos assim que possível.
