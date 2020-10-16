---
title: A autenticação OpenID Connect com diretório ativo Azure
description: Orientação arquitetónica para alcançar este padrão de autenticação
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114367"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>A autenticação OpenID Connect com diretório ativo Azure

OpenID Connect (OIDC) é um protocolo de autenticação baseado no protocolo OAuth2 (que é usado para autorização). A OIDC utiliza os fluxos de mensagens padronizados da OAuth2 para fornecer serviços de identidade. 

O objetivo de design da OIDC é "tornar as coisas simples e complicadas possíveis". O OIDC permite que os desenvolvedores autentiquem os seus utilizadores através de websites e aplicações sem terem de possuir e gerir ficheiros de palavras-passe. Isto fornece ao construtor de aplicações uma forma segura de verificar a identidade da pessoa que atualmente utiliza o navegador ou app nativa que está ligada à aplicação.

A autenticação do utilizador deve ser efetuada num fornecedor de identidade onde a sessão ou credenciais do utilizador serão verificadas. Para fazer isso, precisas de um agente de confiança. As aplicações nativas normalmente lançam o navegador do sistema para o efeito. As vistas incorporadas não são consideradas de confiança, uma vez que não há nada que impeça a aplicação de bisbilhotar a palavra-passe do utilizador. 

Além da autenticação, o utilizador pode ser solicitado o seu consentimento. O Consentimento é a permissão explícita do utilizador para permitir que uma aplicação aceda a recursos protegidos. O consentimento é diferente da autenticação porque o consentimento só precisa de ser fornecido uma vez para um recurso. O consentimento permanece válido até que o utilizador ou administrador revogue manualmente a concessão. 

## <a name="use-when"></a>Utilizar quando

Existe a necessidade de consentimento do utilizador e de iniciar sindus.

![diagrama arquitetónico](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador**: Solicita um serviço a partir da aplicação.

* **Agente de confiança**: O componente com o que o utilizador interage. Este agente de confiança é geralmente um navegador web.

* **Aplicação**: A aplicação, ou Servidor de Recursos, é onde reside o recurso ou dados. Confia no fornecedor de identidade para autenticar e autorizar o agente de confiança. 

* **Azure AD**: O fornecedor OIDC, também conhecido como fornecedor de identidade, gere de forma segura qualquer coisa a ver com a informação do utilizador, o seu acesso e as relações de confiança entre as partes num fluxo. Autentica a identidade do utilizador, concede e revoga o acesso aos recursos e altera as fichas. 

## <a name="implement-oidc-with-azure-ad"></a>Implementar OIDC com Azure AD

* [Integrara aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protocolos OAuth 2.0 e OpenID Connect na Plataforma de Identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Plataforma de identidade da Microsoft e protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [Web-in com OpenID Connect em Azure Ative Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [Proteger a sua aplicação com o OpenID Connect e o Azure AD](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
