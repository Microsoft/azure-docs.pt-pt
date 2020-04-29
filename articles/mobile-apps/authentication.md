---
title: Adicione autenticação às suas aplicações móveis com visual studio app center e serviços Azure
description: Conheça os serviços como o Visual Studio App Center que ajudam a configurar a autenticação do utilizador e permitem que as aplicações móveis se autentiquem com contas sociais, Diretório Ativo Azure e autenticação personalizada.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241040"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicione a autenticação e gerencie as identidades dos utilizadores nas suas aplicações móveis

Ter uma visão do utilizador e o seu comportamento em toda a sua aplicação capacita os desenvolvedores a envolverem melhor os utilizadores, criando experiências personalizadas para eles. Quer seja um desenvolvedor de aplicações que esteja a construir uma aplicação de colaboração para utilizadores dentro da sua organização ou esteja a criar a próxima plataforma de rede social, precisa de uma forma de autenticar os utilizadores e gerir as identidades dos utilizadores. Um serviço de gestão de identidade é uma das características mais importantes de um serviço de back-end móvel.

Utilize os seguintes serviços para permitir a autenticação do utilizador nas suas aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Auth](/appcenter/auth/) é um serviço de gestão de identidade baseado na nuvem que os desenvolvedores podem usar para autenticar os utilizadores e gerir as identidades dos utilizadores. O App Center Auth também se integra com outras partes do Visual Studio App Center. Os desenvolvedores podem usar a identidade do utilizador para [visualizar os dados](/appcenter/data/index) dos utilizadores noutros serviços e até [enviar notificações push aos utilizadores em vez de dispositivos individuais.](/appcenter/push/push-to-user#setting-user-identity) 

**Principais funcionalidades**
- Alimentado por Azure Ative Directory B2C (Azure AD B2C). 
    - Grau de empresa.
    - Altamente disponível.
    - Serviço seguro e global.
- Traga a sua própria identidade e a opção de usar outros fornecedores populares de identidade e gestão de acesso, como o Auth0 e firebase.
- Suporte do Diretório Ativo Azure.
    - Ligue os inquilinos da Azure AD existentes. 
    - Permitir a autenticação contra um domínio corporativo.
    - Gerir o acesso a dados sensíveis.
- Experiência simples do utilizador e experiência mágica do SDK envolvendo a Microsoft Authentication Library com o Visual Studio App Center SDK.
- Suporte de plataforma para iOS, Android, Xamarin e React Native.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Começar com app center Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[O Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidade entre empresas e consumidores (B2C) que os desenvolvedores podem usar para autenticar os seus clientes. Este serviço de etiquetabranca permite que os desenvolvedores personalizem e controlem a forma como os utilizadores interagem de forma segura com as suas aplicações web, desktop, mobile ou de uma só página. Utilizando o Azure AD B2C, os utilizadores podem inscrever-se, iniciar sessão, redefinir palavras-passe e editar perfis. O Azure AD B2C implementa uma forma dos protocolos OpenID Connect e OAuth 2.0. 

**Principais funcionalidades**
- Autenticar de forma segura os clientes com o seu fornecedor de identidade preferido.
- Gerir a identidade e o acesso do cliente.
- Obtenha suporte de inscrição para redes sociais como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Conecte-se às suas contas de utilizador utilizando protocolos padrão da indústria, como o OpenID Connect ou o SAML, para tornar possível a gestão de identidade numa variedade de plataformas.
- Forneça registo de marca e experiências de inscrição.
- Integrar facilmente com bases de dados crm, ferramentas de análise de marketing e sistemas de verificação de contas.
- Capture dados de inscrição, preferência e conversão para os clientes.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação Azure AD B2C](/azure/active-directory-b2c/)
- [Guias de Início Rápido](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[O Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a iniciarem sessão e a terem acesso a:
- Recursos externos, como o Microsoft Office 365, o portal Azure, e milhares de outros softwares como aplicações de serviço (SaaS).
- Recursos internos, como aplicações na sua rede corporativa e intranet, juntamente com quaisquer aplicações em nuvem desenvolvidas pela sua própria organização.

**Principais funcionalidades**
- Acesso sem emenda e altamente seguro ligando os utilizadores às aplicações de que necessitam.
- Proteção completa da identidade e segurança reforçada para identidades e acesso com base no contexto de utilizador, localização, dispositivo, dados e aplicação.
- Milhares de aplicações pré-integradas para aplicações comerciais e personalizadas, como office 365, Salesforce.com e Box.
- Capacidade de gerir o acesso à escala.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [O que é o Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introdução ao Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Guias de Início Rápido](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)