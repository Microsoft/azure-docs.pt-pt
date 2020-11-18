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
ms.openlocfilehash: 3483701d34b8fef5b40a411e22b33a73db2de44b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658745"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Utilização do Azure AD para aplicações não listadas na galeria de aplicações

No Add um quickstart [de aplicação,](add-application-portal.md) você aprende a adicionar uma app ao seu inquilino AZure AD.

Além das escolhas na galeria de [aplicações AZure AD,](../saas-apps/tutorial-list.md)tem a opção de adicionar uma **aplicação não-galeria.** 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Capacidades para apps não listadas na galeria AZure AD

Pode adicionar qualquer aplicação que já exista na sua organização, ou qualquer aplicação de terceiros de um fornecedor que já não faça parte da galeria AZure AD. Dependendo do seu [contrato de licença,](https://azure.microsoft.com/pricing/details/active-directory/)estão disponíveis as seguintes capacidades:

- Integração de autosserviço de qualquer aplicação que suporte a linguagem de marcação de afirmação de [segurança (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) fornecedores de identidade (iniciados por SP ou iniciados pelo IdP)
- Integração de self-service de qualquer aplicação web que tenha uma página de s-in baseada em HTML usando [SSO baseado em palavra-passe](sso-options.md#password-based-sso)
- Ligação self-service de aplicações que utilizam o [protocolo System for Cross-Domain Identity Management (SCIM) para o fornecimento de utilizadores](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicação no [launcher de aplicações do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou [nas Minhas Apps](sso-options.md#linked-sign-on)

Se procura orientação do programador sobre como integrar aplicações personalizadas com AD AZure, consulte [Cenários de Autenticação para AZure AD](../develop/authentication-vs-authorization.md). Quando desenvolve uma aplicação que utiliza um protocolo moderno como [o OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar os utilizadores, pode registá-la com a plataforma de identidade da Microsoft utilizando a experiência de [registos](../develop/quickstart-register-app.md) da App no portal Azure.

## <a name="next-steps"></a>Próximos passos

- [Quickstart Series on App Management](view-applications-portal.md)