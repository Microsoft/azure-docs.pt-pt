---
title: Adicionar autenticação aos seus aplicativos móveis com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a configurar a autenticação do usuário e a permitir que os aplicativos móveis se autentiquem com contas sociais, Azure Active Directory e autenticação personalizada.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795760"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicionar autenticação e gerenciar identidades de usuário em seus aplicativos móveis

Ter uma visão do usuário e seu comportamento em seu aplicativo permite que os desenvolvedores envolvam melhor os usuários criando experiências personalizadas para eles. Seja você um desenvolvedor de aplicativos que esteja criando um aplicativo de colaboração para usuários dentro da sua organização ou que esteja criando a próxima plataforma de rede social, você precisará de uma maneira de autenticar usuários e gerenciar identidades de usuário. O uso de um serviço de gerenciamento de identidade é um dos recursos mais importantes de um serviço de back-end móvel.

Use os seguintes serviços para habilitar a autenticação de usuário em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center autenticação](/appcenter/auth/) é um serviço de gerenciamento de identidade baseado em nuvem que permite aos desenvolvedores autenticar usuários e gerenciar identidades de usuário. App Center autenticação também se integra a outras partes do App Center, permitindo que os desenvolvedores aproveitem a identidade do usuário para [exibir dados do usuário](/appcenter/data/index) em outros serviços e até mesmo [enviar notificações por push para os usuários em vez de dispositivos individuais](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Principais recursos**
- **Da plataforma Azure Active Directory B2C (Azure AD B2C)** 
    - Nível empresarial.
    - Altamente disponível.
    - Serviço seguro e global.
- **Traga sua própria identidade** e use outros provedores de gerenciamento de identidade e acesso populares, como Auth0 e firebase.
- **Suporte do AAD** 
    - Conectar locatários do AAD existentes. 
    - Habilite a autenticação em um domínio corporativo.
    - Gerencie o acesso a dados confidenciais.
- Experiência do **usuário simples** e experiência do SDK do mágico encapsulando a biblioteca MSAL com o SDK do App Center.
- **Suporte à plataforma** -Ios, Android, Xamarin, reagir nativo.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Introdução ao App Center auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
O [Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade entre consumidores que permite aos desenvolvedores autenticar seus clientes. Esse serviço de rótulo branco permite que os desenvolvedores personalizem e controlem como os usuários interagem com segurança com seus aplicativos Web, de área de trabalho, móveis ou de página única. Usando Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. Azure AD B2C implementa uma forma do OpenID Connect e dos protocolos OAuth 2,0. 

**Principais recursos**
- Autentique com segurança os clientes com seu provedor de identidade preferencial.
- **Gerenciamento de acesso e identidade do cliente**.
- Logons sociais com suporte, como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo.
- Conecte-se às suas contas de usuário usando **protocolos padrão do setor** , como OpenID Connect ou SAML, para tornar possível o gerenciamento de identidade em uma variedade de plataformas.
- Fornecer registro de marca e experiências de logon.
- Integre-se facilmente a bancos de dados CRM, ferramentas de análise de marketing e sistemas de verificação de contas.
- Capture dados de logon, de preferência e de conversão para clientes.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação do Azure AD B2C](/azure/active-directory-b2c/)
- [Inícios rápidos](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
O [Azure Active Directory (AD do Azure)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft, que ajuda seu funcionário a entrar e acessar.
- Recursos externos, como Microsoft Office 365, o portal do Azure e milhares de outros aplicativos SaaS.
- Recursos internos, como aplicativos em sua rede corporativa e intranet, juntamente com qualquer aplicativo de nuvem desenvolvido por sua própria organização.

**Principais recursos**
- **Acesso contínuo e altamente seguro** conectando os usuários aos aplicativos de que precisam.
- **Proteção de identidade abrangente** e segurança aprimorada para identidades e acesso com base no usuário, no local, no dispositivo, nos dados e no contexto do aplicativo.
- **Milhares de aplicativos pré-instalados** e comerciais, como o Office 365, o Salesforce.com e o box.
- **Gerencie o acesso em escala**.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [O que é o Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Get started with Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Introdução ao Azure Active Directory)
- [Inícios rápidos](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)