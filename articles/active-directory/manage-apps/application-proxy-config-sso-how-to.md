---
title: Como configurar um único início de sessão numa aplicação proxy de aplicação
description: Como pode configurar um único início de sessão na sua aplicação de procuração de aplicação rapidamente
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712026"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar um único sinal para uma aplicação de procuração de aplicação

O único sinal de inscrição (SSO) permite que os seus utilizadores acedam a uma aplicação sem autenticar várias vezes. Permite que a autenticação única ocorra na nuvem, contra o Diretório Ativo Azure, e permite que o serviço ou Conector se personifiquem do utilizador para completar quaisquer desafios adicionais de autenticação a partir da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar um único sinal
Para configurar o SSO, certifique-se primeiro de que a sua aplicação está configurada para pré-autenticação através do Diretório Ativo Azure. Para fazer esta configuração, vá ao **Azure Ative Diretório**  - &gt; **Enterprise Applications**  - &gt; **Todas as aplicações**  - &gt; A sua ** - &gt; aplicação Proxy**. Nesta página, você vê o campo "Pré-Autenticação" e certifique-se de que está definido para "Azure Ative Directory. 

Para obter mais informações sobre os métodos de Pré-Autenticação, consulte o passo 4 do documento de publicação da [aplicação](application-proxy-add-on-premises-application.md).

   ![Método de pré-autenticação no portal Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar modos de inscrição simples para aplicações de procuração de aplicações
Configure o tipo específico de inscrição individual. Os métodos de inscrição são classificados com base no tipo de autenticação que a aplicação backend utiliza. As aplicações de Procuração de Aplicações suportam três tipos de inscrição:

-   **Sign-On baseado em palavra-passe**: O sinal de acesso baseado em palavras-passe pode ser usado para qualquer aplicação que utilize o nome de utilizador e os campos de palavra-passe para iniciar sessão. Os passos de configuração estão na [palavra-passe Configuração Um único sinal para uma aplicação de galeria Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Autenticação Integrada do Windows**: Para aplicações que utilizem autenticação integrada do Windows (IWA), o único sinal é ativado através da Delegação Limitada kerberos (KCD). Este método dá permissão aos Conectores proxy de aplicação no Diretório Ativo para personificar os utilizadores e enviar e receber fichas em seu nome. Os detalhes sobre a configuração do KCD podem ser encontrados no [Single Sign-On com documentação KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Sign-On baseado em cabeçalho:** O sign-on baseado em cabeçalho é ativado através de uma parceria e requer alguma configuração adicional. Para obter mais informações sobre a parceria e instruções passo a passo para configurar um único sinal para uma aplicação que utilize cabeçalhos para autenticação, consulte o [PingAccess para documentação ad.](application-proxy-configure-single-sign-on-with-ping-access.md)

-   **SAML single sign-on**: Com o único sinal da SAML, a Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de início de sessão à aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML. Para obter informações sobre a criação de um único sinal da SAML, consulte [o SAML para obter um único sinal com procuração](application-proxy-configure-single-sign-on-on-premises-apps.md)de aplicação .

Cada uma destas opções pode ser encontrada indo para a sua aplicação em "Aplicações Empresariais", e abrindo a página **De Sessão Única** no menu esquerdo. Note que se a sua aplicação foi criada no portal antigo, poderá não ver todas estas opções.

Nesta página, consulte também uma opção adicional de Início de Sessão: Linked Sign-On. Esta opção também é suportada por Application Proxy. No entanto, esta opção não adiciona um único sinal ao pedido. Dito isto, a aplicação pode já ter um único sign-on implementado usando outro serviço, como os Serviços da Federação de Diretório Ativo. 

Esta opção permite que um administrador crie uma ligação a uma aplicação que os utilizadores aterram pela primeira vez ao aceder à aplicação. Por exemplo, se houver uma aplicação configurada para autenticar utilizadores utilizando os Serviços da Federação de Diretórios Ativos 2.0, um administrador pode utilizar a opção "Linked Sign-On" para criar um link para o mesmo no painel de acesso.

## <a name="next-steps"></a>Passos seguintes
- [Abóbada de senha para inscrição única com Procuração de Aplicação](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação limitada kerberos para inscrição única com procuração de pedido](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para inscrição única com procuração de aplicação](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para um único sinal com procuração de aplicação](application-proxy-configure-single-sign-on-on-premises-apps.md).
