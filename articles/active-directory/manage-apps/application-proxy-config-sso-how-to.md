---
title: Como configurar um único sign-on para uma aplicação de Procuração de Aplicação
description: Como pode configurar um único sign-on na sua aplicação de procuração de aplicação rapidamente
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/12/2019
ms.author: kenwith
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54673e0018f83024d1c01599d0096c71b6b1d0ae
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642134"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar um único sign-on para uma aplicação de procuração de aplicação

O único sinal de sso permite aos seus utilizadores aceder a uma aplicação sem autenticar várias vezes. Permite que a autenticação única ocorra na nuvem, contra o Azure Ative Directory, e permite que o serviço ou Conector personiem o utilizador para completar quaisquer desafios adicionais de autenticação da aplicação.

## <a name="how-to-configure-single-sign-on"></a>Como configurar um único sinal
Para configurar o SSO, certifique-se primeiro de que a sua aplicação está configurada para Pré-Autenticação através do Diretório Ativo Azure. Para fazer esta configuração, aceda a **aplicações empresariais do Azure Ative Directory**  - &gt; **Enterprise Applications**  - &gt; **Todas as Aplicações**  - &gt; A sua ** - &gt; aplicação Proxy**. Nesta página, vê o campo "Pré Autenticação" e certifique-se de que está definido para "Azure Ative Directory". 

Para obter mais informações sobre os métodos de pré-autenticação, consulte o passo 4 do [documento de publicação da aplicação.](application-proxy-add-on-premises-application.md)

   ![Método de pré-autenticação no portal Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurar modos de inscrição única para aplicações de procuração de aplicações
Configure o tipo específico de inscrição única. Os métodos de inscrição são classificados com base no tipo de autenticação que a aplicação backend utiliza. As aplicações Proxy da App suportam três tipos de inscrição:

-   **Sign-on baseado em palavra-passe**: O sign-on baseado em palavras-passe pode ser usado para qualquer aplicação que utilize o nome de utilizador e os campos de palavra-passe para iniciar sê-lo. Os passos de configuração estão na [senha configurar um único sinal para uma aplicação de galeria AZure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Autenticação integrada do Windows**: Para aplicações que utilizem autenticação integrada do Windows (IWA), o sign-on único é ativado através da Delegação Restrita kerberos (KCD). Este método dá permissão aos Conectores Proxy da Aplicação em Ative Directory para personificar os utilizadores e enviar e receber fichas em seu nome. Os detalhes sobre a configuração do KCD podem ser encontrados no [Sign-On único com documentação KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Sign-on baseado em cabeçalho**: O sinal baseado no cabeçalho é ativado através de uma parceria e requer alguma configuração adicional. Para obter detalhes sobre a parceria e instruções passo a passo para configurar uma única inscrição para uma aplicação que utilize cabeçalhos para autenticação, consulte o [PingAccess para documentação AD Azure](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Saturação única :** Com um único sinal DE SAML, o Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de inscrição à aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções de aplicação específicas com base nas regras que define nas suas alegações SAML. Para obter informações sobre a configuração do único sinal de marcação [SAML, consulte a SAML para obter um único s-on com o Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Cada uma destas opções pode ser encontrada indo para a sua aplicação em "Aplicações empresariais", e abrindo a página **'Sign-On' único** no menu esquerdo. Note que se a sua aplicação foi criada no portal antigo, poderá não ver todas estas opções.

Nesta página, também vê uma opção adicional de Início de Sômtenção: Iniciar Sôm. Esta opção também é suportada pela Application Proxy. No entanto, esta opção não adiciona um único sinal de inscrição à aplicação. Dito isto, a aplicação pode já ter um único sign-on implementado usando outro serviço, como os Serviços da Federação de Diretórios Ativos. 

Esta opção permite que um administrador crie um link para uma aplicação que os utilizadores primeiro aterram ao aceder à aplicação. Por exemplo, se houver uma aplicação configurada para autenticar utilizadores que utilizem os Serviços da Federação de Diretório Ativo 2.0, um administrador pode utilizar a opção "Linked Sign-On" para criar um link para o mesmo nas Minhas Apps.

## <a name="next-steps"></a>Passos seguintes
- [Abóbada de senha para um único sinal com Proxy de aplicação](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos Delegação Restrita para um único sign-on com Proxy de aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para um único sign-on com Application Proxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para um único sinal de inscrição com Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
