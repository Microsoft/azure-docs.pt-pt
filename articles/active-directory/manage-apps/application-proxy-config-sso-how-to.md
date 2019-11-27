---
title: Como configurar o logon único para um aplicativo de proxy de aplicativo
description: Como configurar o início de sessão único para a sua aplicação de proxy de aplicação rapidamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5eca08e0b736937af42e58d81148636e3269df
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275639"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o início de sessão único para uma aplicação de Proxy de aplicações

Início de sessão único (SSO) permite aos utilizadores aceder a uma aplicação sem autenticar o várias vezes. Ele permite que a autenticação única ocorrer na cloud, relativamente ao Azure Active Directory e permite que o serviço ou o conector para representar o utilizador para concluir qualquer desafios de autenticação adicionais da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o início de sessão único
Para configurar o SSO, certifique-se de que seu aplicativo está configurado para pré-autenticação através do Azure Active Directory. Para fazer essa configuração, vá para **Azure Active Directory** -&gt; **aplicativos empresariais** -&gt; **todos os aplicativos** -&gt; seu aplicativo-&gt; o **proxy de aplicativo**. Nesta página, veja o campo de "Pré-autenticação" e certifique-se de que está definido como "Azure Active Directory. 

Para obter mais informações sobre os métodos de pré-autenticação, consulte a etapa 4 do [documento de publicação de aplicativos](application-proxy-add-on-premises-application.md).

   ![Método de pré-autenticação no portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar os modos de início de sessão únicos para aplicações de Proxy de aplicação
Configure o tipo específico de início de sessão único. Os métodos de início de sessão são classificados com base no tipo de autenticação a aplicação de back-end utiliza. Aplicações de Proxy de aplicação suportam três tipos de início de sessão:

-   **Logon baseado em senha**: o logon baseado em senha pode ser usado para qualquer aplicativo que use campos de nome de usuário e senha para fazer logon. As etapas de configuração estão em [Configurar logon único com senha para um aplicativo da galeria do Azure ad](configure-password-single-sign-on-gallery-applications.md).

-   **Autenticação integrada do Windows**: para aplicativos que usam a autenticação integrada do Windows (IWA), o logon único é habilitado por meio da KCD (delegação restrita de Kerberos). Este método dá permissão de Conetores Proxy de aplicações no Active Directory para representar os utilizadores e para enviar e receber tokens em seu nome. Detalhes sobre como configurar o KCD podem ser encontrados no [logon único com a documentação do KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logon baseado em cabeçalho**: o logon baseado em cabeçalho é habilitado por meio de uma parceria e requer alguma configuração adicional. Para obter detalhes sobre a parceria e instruções passo a passo para configurar o logon único para um aplicativo que usa cabeçalhos para autenticação, consulte a documentação do [PingAccess for Azure ad](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Logon único do SAML**: com o logon único do SAML, o Azure AD é autenticado no aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Com o logon único baseado em SAML, você pode mapear usuários para funções de aplicativo específicas com base nas regras que você define em suas declarações SAML. Para obter informações sobre como configurar o logon único do SAML, consulte [SAML para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).

Cada uma dessas opções pode ser encontrada acessando seu aplicativo em "aplicativos empresariais" e abrindo a página **logon único** no menu à esquerda. Observe que, se o aplicativo tiver sido criado no portal antigo, talvez você não veja todas essas opções.

Nesta página, verá também uma opção de início de sessão adicional: o início de sessão ligado. Esta opção também é suportada pelo Proxy de aplicações. No entanto, esta opção não adiciona o início de sessão único para a aplicação. Dito que a aplicação pode já ter o início de sessão único implementado através de outro serviço, como serviços de Federação do Active Directory. 

Esta opção permite que um administrador para criar uma ligação a uma aplicação que terra primeiro dos utilizadores no quando o acesso à aplicação. Por exemplo, se existir uma aplicação que está configurada para autenticar os utilizadores com o Active Directory Federation Services 2.0, um administrador pode utilizar a opção "ligado início de sessão" para criar uma ligação ao mesmo no painel de acesso.

## <a name="next-steps"></a>Passos seguintes
- [Compartimentação de senha para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação restrita de Kerberos para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).
