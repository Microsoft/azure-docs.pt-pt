---
title: A autenticação OpenID Connect com diretório ativo Azure
description: Orientação arquitetónica para alcançar a autenticação OpenID Connect com diretório ativo Azure.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168666"
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

* [Integrara aplicações com o Azure AD](../saas-apps/tutorial-list.md) 

* [Protocolos OAuth 2.0 e OpenID Connect na Plataforma de Identidade da Microsoft](../develop/active-directory-v2-protocols.md) 

* [Plataforma de identidade da Microsoft e protocolo OpenID Connect](../develop/v2-protocols-oidc.md) 

* [Web-in com OpenID Connect em Azure Ative Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Proteger a sua aplicação com o OpenID Connect e o Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

