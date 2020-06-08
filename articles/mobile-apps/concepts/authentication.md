---
title: Adicione autenticação às suas aplicações móveis com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o Visual Studio App Center que ajudam a configurar a autenticação do utilizador e permitem que as aplicações móveis autentem com contas sociais, Diretório Ativo Azure e autenticação personalizada.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: dabf97b4eb1b3c07883f0bd42835c94ce8df44e4
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484850"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicione a autenticação e gere as identidades dos utilizadores nas suas aplicações móveis

Ter uma visão do utilizador e o seu comportamento em toda a sua aplicação capacita os desenvolvedores a envolverem melhor os utilizadores, criando experiências personalizadas para eles. Quer seja um programador de aplicações que esteja a construir uma aplicação de colaboração para utilizadores dentro da sua organização ou se está a criar a próxima plataforma da rede social, precisa de uma forma de autenticar os utilizadores e gerir as identidades dos utilizadores. Um serviço de gestão de identidade é uma das características mais importantes de um serviço de back-end móvel.

Utilize os seguintes serviços para permitir a autenticação do utilizador nas suas aplicações móveis.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[O Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidades business-to-consumer (B2C) que os desenvolvedores podem usar para autenticar os seus clientes. Este serviço de etiqueta branca permite que os desenvolvedores personalizem e controlem como os utilizadores interagem de forma segura com as suas aplicações web, desktop, mobile ou de uma página única. Utilizando o Azure AD B2C, os utilizadores podem inscrever-se, iniciar sessão, redefinir palavras-passe e editar perfis. Azure AD B2C implementa uma forma dos protocolos OpenID Connect e OAuth 2.0. 

**Principais funcionalidades**
- Autenticar seguramente os clientes com o seu fornecedor de identidade preferido.
- Gerir a identidade e o acesso do cliente.
- Ganhe apoio de s-in para redes sociais como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Conecte-se às suas contas de utilizador utilizando protocolos padrão da indústria, como o OpenID Connect ou o SAML, para tornar possível a gestão de identidade em várias plataformas.
- Fornecer registo de marca e experiências de inscrição.
- Integrar-se facilmente com bases de dados de CRM, ferramentas de análise de marketing e sistemas de verificação de conta.
- Capture dados de s-in, preferência e conversão para os clientes.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação Azure AD B2C](/azure/active-directory-b2c/)
- [Guias de Início Rápido](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[O Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a iniciar súmis e a ter acesso a:
- Recursos externos, como o Microsoft Office 365, o portal Azure, e milhares de outros softwares como aplicações de serviço (SaaS).
- Recursos internos, como aplicações na sua rede corporativa e intranet, juntamente com quaisquer aplicações em nuvem desenvolvidas pela sua própria organização.

**Principais funcionalidades**
- Acesso sem emenda e altamente seguro, ligando os utilizadores às aplicações de que necessitam.
- Proteção de identidade abrangente e segurança reforçada para identidades e acesso com base no contexto do utilizador, localização, dispositivo, dados e aplicação.
- Milhares de aplicações pré-integradas para aplicações comerciais e personalizadas, como o Office 365, Salesforce.com e Box.
- Capacidade de gerir o acesso à escala.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [O que é o Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introdução ao Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Guias de Início Rápido](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)