---
title: Como configurar um único início de sessão numa aplicação proxy de aplicação
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
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712026"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o início de sessão único para uma aplicação de Proxy de aplicações

Início de sessão único (SSO) permite aos utilizadores aceder a uma aplicação sem autenticar o várias vezes. Ele permite que a autenticação única ocorrer na cloud, relativamente ao Azure Active Directory e permite que o serviço ou o conector para representar o utilizador para concluir qualquer desafios de autenticação adicionais da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o início de sessão único
Para configurar o SSO, certifique-se de que seu aplicativo está configurado para pré-autenticação através do Azure Active Directory. Para fazer esta configuração, vá ao **Azure Ative Directory** -&gt; **Enterprise Applications** -&gt; **todas as aplicações** -&gt; A sua aplicação-&gt; **Procuração de aplicações.** Nesta página, veja o campo de "Pré-autenticação" e certifique-se de que está definido como "Azure Active Directory. 

Para obter mais informações sobre os métodos de Pré-Autenticação, consulte o passo 4 do documento de publicação da [aplicação](application-proxy-add-on-premises-application.md).

   ![Método de pré-autenticação no portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar os modos de início de sessão únicos para aplicações de Proxy de aplicação
Configure o tipo específico de início de sessão único. Os métodos de início de sessão são classificados com base no tipo de autenticação a aplicação de back-end utiliza. Aplicações de Proxy de aplicação suportam três tipos de início de sessão:

-   **Sign-On baseado em palavra-passe**: O sinal de acesso baseado em palavras-passe pode ser usado para qualquer aplicação que utilize o nome de utilizador e os campos de palavra-passe para iniciar sessão. Os passos de configuração estão na [palavra-passe Configuração Um único sinal para uma aplicação de galeria Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Autenticação Integrada do Windows**: Para aplicações que utilizem autenticação integrada do Windows (IWA), o único sinal é ativado através da Delegação Limitada kerberos (KCD). Este método dá permissão de Conetores Proxy de aplicações no Active Directory para representar os utilizadores e para enviar e receber tokens em seu nome. Os detalhes sobre a configuração do KCD podem ser encontrados no [Single Sign-On com documentação KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Sign-On baseado em cabeçalho:** O sign-on baseado em cabeçalho é ativado através de uma parceria e requer alguma configuração adicional. Para obter mais informações sobre a parceria e instruções passo a passo para configurar um único sinal para uma aplicação que utilize cabeçalhos para autenticação, consulte o [PingAccess para documentação ad.](application-proxy-configure-single-sign-on-with-ping-access.md)

-   **SAML single sign-on**: Com o único sinal da SAML, a Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML. Para obter informações sobre a criação de um único sinal da SAML, consulte [o SAML para obter um único sinal com procuração](application-proxy-configure-single-sign-on-on-premises-apps.md)de aplicação .

Cada uma destas opções pode ser encontrada indo para a sua aplicação em "Aplicações Empresariais", e abrindo a página **De Sessão Única** no menu esquerdo. Note que se a sua aplicação foi criada no portal antigo, poderá não ver todas estas opções.

Nesta página, verá também uma opção de início de sessão adicional: o início de sessão ligado. Esta opção também é suportada pelo Proxy de aplicações. No entanto, esta opção não adiciona o início de sessão único para a aplicação. Dito que a aplicação pode já ter o início de sessão único implementado através de outro serviço, como serviços de Federação do Active Directory. 

Esta opção permite que um administrador para criar uma ligação a uma aplicação que terra primeiro dos utilizadores no quando o acesso à aplicação. Por exemplo, se existir uma aplicação que está configurada para autenticar os utilizadores com o Active Directory Federation Services 2.0, um administrador pode utilizar a opção "ligado início de sessão" para criar uma ligação ao mesmo no painel de acesso.

## <a name="next-steps"></a>Passos Seguintes
- [Abóbada de senha para inscrição única com Procuração de Aplicação](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação limitada kerberos para inscrição única com procuração de pedido](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para inscrição única com procuração de aplicação](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para um único sinal com procuração de aplicação](application-proxy-configure-single-sign-on-on-premises-apps.md).
