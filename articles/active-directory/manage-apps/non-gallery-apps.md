---
title: Utilização do Azure AD para aplicações não listadas na galeria de aplicações
description: Compreenda como integrar aplicações não listadas na galeria Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 300c6e268e3d2f639bf697237253d2b98e5daa15
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352494"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Utilização do Azure AD para aplicações não listadas na galeria de aplicações

No Add um quickstart [de aplicação,](add-application-portal.md) você aprende a adicionar uma app ao seu inquilino AZure AD.

Além das escolhas na galeria de [aplicações AZure AD,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)tem a opção de adicionar uma **aplicação não-galeria.** 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Capacidades para apps não listadas na galeria AZure AD

Pode adicionar qualquer aplicação que já exista na sua organização, ou qualquer aplicação de terceiros de um fornecedor que já não faça parte da galeria AZure AD. Dependendo do seu [contrato de licença,](https://azure.microsoft.com/pricing/details/active-directory/)estão disponíveis as seguintes capacidades:

- Integração de autosserviço de qualquer aplicação que suporte a linguagem de marcação de afirmação de [segurança (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) fornecedores de identidade (iniciados por SP ou iniciados pelo IdP)
- Integração de self-service de qualquer aplicação web que tenha uma página de s-in baseada em HTML usando [SSO baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso)
- Ligação self-service de aplicações que utilizam o [protocolo System for Cross-Domain Identity Management (SCIM) para o fornecimento de utilizadores](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicação no [launcher da aplicação Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso AZure AD](what-is-single-sign-on.md#linked-sign-on)

Se procura orientação do programador sobre como integrar aplicações personalizadas com AD AZure, consulte [Cenários de Autenticação para AZure AD](../develop/authentication-scenarios.md). Quando desenvolve uma aplicação que utiliza um protocolo moderno como [o OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar os utilizadores, pode registá-la com a plataforma de identidade da Microsoft utilizando a experiência de [registos](../develop/quickstart-register-app.md) da App no portal Azure.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart Series on App Management](view-applications-portal.md)

