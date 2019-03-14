---
title: Como configurar o início de sessão único para uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Como configurar o início de sessão único para a sua aplicação de proxy de aplicação rapidamente
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9c4f2c618851b596cd8f2dfa1fd45820a05049
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791686"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o início de sessão único para uma aplicação de Proxy de aplicações

Início de sessão único (SSO) permite aos utilizadores aceder a uma aplicação sem autenticar o várias vezes. Ele permite que a autenticação única ocorrer na cloud, relativamente ao Azure Active Directory e permite que o serviço ou o conector para representar o utilizador para concluir qualquer desafios de autenticação adicionais da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o início de sessão único
Para configurar o SSO, certifique-se de que seu aplicativo está configurado para pré-autenticação através do Azure Active Directory. Para fazer esta configuração, aceda a **do Azure Active Directory**  - &gt; **aplicações empresariais**  - &gt; **todas as aplicações**   - &gt; Seu aplicativo  **- &gt; Proxy de aplicações**. Nesta página, veja o campo de "Pré-autenticação" e certifique-se de que está definido como "Azure Active Directory. 

Para obter mais informações sobre os métodos de pré-autenticação, consulte o passo 4 do [documento de publicação de aplicação](application-proxy-add-on-premises-application.md).

   ![Método de pré-autenticação no portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar os modos de início de sessão únicos para aplicações de Proxy de aplicação
Configure o tipo específico de início de sessão único. Os métodos de início de sessão são classificados com base no tipo de autenticação a aplicação de back-end utiliza. Aplicações de Proxy de aplicação suportam três tipos de início de sessão:

-   **Com base em palavra-passe de início de sessão**: Com base em palavra-passe de início de sessão pode ser usado para qualquer aplicativo que usa campos de nome de utilizador e palavra-passe para início de sessão. Passos de configuração estão na [configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD](configure-password-single-sign-on-gallery-applications.md).

-   **Autenticação Windows integrada**: Para aplicativos que utilizam a autenticação integrada do Windows (IWA), início de sessão único está ativado por meio de Kerberos Constrained Delegation (KCD). Este método dá permissão de Conetores Proxy de aplicações no Active Directory para representar os utilizadores e para enviar e receber tokens em seu nome. Pode encontrar detalhes sobre como configurar o KCD no [início de sessão único com a documentação de KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Com base no cabeçalho de início de sessão**: Com base no cabeçalho de início de sessão é ativada através de uma parceria e requer configuração adicional. Para obter detalhes sobre a parceria e instruções passo a passo para configurar o início de sessão único para um aplicativo que usa cabeçalhos para autenticação, consulte a [PingAccess para obter a documentação do Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML início de sessão único**: Com o SAML início de sessão único, o Azure AD autentica para o aplicativo usando a conta de utilizador do Azure AD. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Com baseado em SAML início de sessão único, pode mapear os utilizadores a funções de aplicação específica, com base nas regras que definir nas afirmações SAML. Para obter informações sobre como configurar SAML início de sessão único, consulte [SAML de início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-on-premises-apps.md).

Cada uma destas opções pode ser encontrada ao aceder ao seu aplicativo no "Aplicações empresariais" e abrir o **Single Sign-On** página no menu da esquerda. tenha em atenção que, se a aplicação foi criada no portal antigo, não poderá ver todas estas opções.

Nesta página, verá também uma opção de início de sessão adicional: Logon ligado. Esta opção também é suportada pelo Proxy de aplicações. No entanto, esta opção não adiciona o início de sessão único para a aplicação. Dito que a aplicação pode já ter o início de sessão único implementado através de outro serviço, como serviços de Federação do Active Directory. 

Esta opção permite que um administrador para criar uma ligação a uma aplicação que terra primeiro dos utilizadores no quando o acesso à aplicação. Por exemplo, se existir uma aplicação que está configurada para autenticar os utilizadores com o Active Directory Federation Services 2.0, um administrador pode utilizar a opção "ligado início de sessão" para criar uma ligação ao mesmo no painel de acesso.

## <a name="next-steps"></a>Passos Seguintes
- [Palavra-passe vaulting para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação restrita de Kerberos para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML de início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-on-premises-apps.md).
