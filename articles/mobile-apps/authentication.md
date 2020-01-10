---
title: Adicionar autenticação aos seus aplicativos móveis com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como Visual Studio App Center que ajudam a configurar a autenticação do usuário e a permitir que os aplicativos móveis se autentiquem com contas sociais, Azure Active Directory e autenticação personalizada.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: bc7d8d4aaec6ebe27a0f8d2ecc11ca408266f7ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453232"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicionar autenticação e gerenciar identidades de usuário em seus aplicativos móveis

Ter uma visão do usuário e seu comportamento em seu aplicativo permite que os desenvolvedores envolvam melhor os usuários criando experiências personalizadas para eles. Seja você um desenvolvedor de aplicativos que esteja criando um aplicativo de colaboração para usuários dentro da sua organização ou que esteja criando a próxima plataforma de rede social, você precisa de uma maneira de autenticar usuários e gerenciar identidades de usuário. Um serviço de gerenciamento de identidade é um dos recursos mais importantes de um serviço de back-end móvel.

Use os seguintes serviços para habilitar a autenticação de usuário em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center autenticação](/appcenter/auth/) é um serviço de gerenciamento de identidade baseado em nuvem que os desenvolvedores podem usar para autenticar usuários e gerenciar identidades de usuário. App Center autenticação também se integra a outras partes do Visual Studio App Center. Os desenvolvedores podem usar a identidade do usuário para [exibir dados do usuário](/appcenter/data/index) em outros serviços e até mesmo [enviar notificações por push para os usuários em vez de dispositivos individuais](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Principais funcionalidades**
- Da plataforma Azure Active Directory B2C (Azure AD B2C). 
    - Nível empresarial.
    - Altamente disponível.
    - Serviço seguro e global.
- Traga sua própria identidade e a opção de usar outros provedores de gerenciamento de identidade e acesso populares, como Auth0 e firebase.
- Suporte a Azure Active Directory.
    - Conectar locatários existentes do Azure AD. 
    - Habilite a autenticação em um domínio corporativo.
    - Gerencie o acesso a dados confidenciais.
- Experiência do usuário simples e experiência do SDK do mágico encapsulando a biblioteca de autenticação da Microsoft com o SDK do Visual Studio App Center.
- Suporte de plataforma para iOS, Android, Xamarin e reagir nativo.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Introdução ao App Center auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade entre empresas (B2C) que os desenvolvedores podem usar para autenticar seus clientes. Esse serviço de rótulo de branco permite que os desenvolvedores personalizem e controlem como os usuários interagem com segurança com seus aplicativos Web, de área de trabalho, móveis ou de página única. Usando Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. Azure AD B2C implementa uma forma do OpenID Connect e dos protocolos OAuth 2,0. 

**Principais funcionalidades**
- Autentique com segurança os clientes com seu provedor de identidade preferencial.
- Gerencie a identidade e o acesso do cliente.
- Obter suporte de entrada para mídias sociais, como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Conecte-se às suas contas de usuário usando protocolos padrão do setor, como OpenID Connect ou SAML, para tornar possível o gerenciamento de identidade em uma variedade de plataformas.
- Forneça experiências de registro e de entrada com marca.
- Integre-se facilmente a bancos de dados CRM, ferramentas de análise de marketing e sistemas de verificação de contas.
- Capturar dados de entrada, de preferência e de conversão para clientes.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação do Azure AD B2C](/azure/active-directory-b2c/)
- [Inícios rápidos](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft, que ajuda seus funcionários a entrar e obter acesso a:
- Recursos externos, como Microsoft Office 365, o portal do Azure e milhares de outros aplicativos SaaS (software como serviço).
- Recursos internos, como as aplicações na sua rede empresarial e a intranet, juntamente com quaisquer aplicações na cloud desenvolvida pela sua organização.

**Principais funcionalidades**
- Acesso contínuo e altamente seguro conectando os usuários aos aplicativos de que precisam.
- Proteção de identidade abrangente e segurança aprimorada para identidades e acesso com base no usuário, no local, no dispositivo, nos dados e no contexto do aplicativo.
- Milhares de aplicativos previamente integrados para aplicativos comerciais e personalizados, como o Office 365, o Salesforce.com e o box.
- Capacidade de gerenciar o acesso em escala.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [O que é o Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Get started with Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Introdução ao Azure Active Directory)
- [Inícios rápidos](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)