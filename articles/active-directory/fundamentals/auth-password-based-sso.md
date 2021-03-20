---
title: Autenticação baseada em palavra-passe com Diretório Ativo Azure
description: Orientação arquitetónica para a obtenção de autenticação baseada em palavra-passe com o Azure Ative Directory.
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172827"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Autenticação baseada em palavra-passe com Diretório Ativo Azure

O Single Sign-On (SSO) baseado em palavra-passe utiliza o processo de autenticação existente para a aplicação. Quando ativa sSO baseado em palavras-passe, o Azure Ative Directory (Azure AD) recolhe, encripta e armazena de forma segura as credenciais de utilizador no diretório. O Azure AD fornece o nome de utilizador e a palavra-passe à aplicação quando o utilizador tenta iniciar sação.

Escolha SSO baseado em palavra-passe quando uma aplicação autenticar com um nome de utilizador e palavra-passe em vez de aceder a tokens e cabeçalhos. O SSO baseado em palavras-passe suporta qualquer aplicação baseada na nuvem que tenha um sinal baseado em HTML na página. 

## <a name="use-when"></a>Utilizar quando

Você precisa proteger com pré-autenticação e fornecer SSO através de abobadagem de senha para aplicações web.

![diagrama arquitetónico](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador:** Acessos formaram uma aplicação baseada nas minhas apps ou visitando diretamente o site. 

* **Web browser**: O componente com o que o utilizador interage para aceder ao URL externo da aplicação. O utilizador acede à aplicação baseada em formulários através da extensão MyApps. 

* **Extensão MyApps**: Identifica a aplicação SSO configurada baseada em palavra-passe e fornece as credenciais ao sinal em forma. A extensão MyApps está instalada no navegador web. 

* **Azure AD**: Autentica o utilizador.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementar SSO baseado em palavra-passe com Azure AD

* [O que é SSO baseado em palavra-passe](../manage-apps/what-is-single-sign-on.md) 

* [Configurar sSO baseado em palavra-passe para aplicações em nuvem ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Configure SSO baseado em palavra-passe para aplicações no local com Proxy de aplicação](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

