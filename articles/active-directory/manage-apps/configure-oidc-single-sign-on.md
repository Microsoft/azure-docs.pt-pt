---
title: Compreender o sign-on único baseado no OIDC (SSO) para aplicações no Azure Ative Directory
description: Compreenda o sign-on único baseado no OIDC (SSO) para aplicações no Azure Ative Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780064"
---
# <a name="understand-oidc-based-single-sign-on"></a>Compreenda o sign-on único baseado no OIDC
Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. Este artigo entra em mais detalhes sobre apps que usam a norma OpenID Connect para implementar um único sinal de entrada. 

## <a name="before-you-begin"></a>Before you begin
O processo de adição de uma app ao seu inquilino Azure Ative Directory depende do tipo de sinal único da aplicação implementada. Para saber mais sobre as opções de inscrição única disponíveis para aplicações que podem utilizar a Azure AD para gestão de identidade, consulte [as opções de inscrição únicas](sso-options.md). Este artigo abrange aplicações baseadas em OIDC.


## <a name="basic-oidc-configuration"></a>Configuração básica do OIDC
Na [série quickstart,](add-application-portal-setup-oidc-sso.md)há um artigo sobre configurar um único sign-on. Nele, você aprende a adicionar uma aplicação baseada em OIDC ao seu inquilino Azure.

A coisa boa ao adicionar uma aplicação que usa o padrão OIDC para um único sign-on é que a configuração é mínima. Aqui está um pequeno vídeo mostrando como adicionar uma aplicação baseada em OIDC ao seu inquilino.

Adicionar uma aplicação baseada em OIDC no Azure Ative Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Para saber mais sobre o consentimento do utilizador e da administração, consulte [o consentimento do utilizador e da administração.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

## <a name="next-steps"></a>Passos seguintes

- [Quickstart Series em Gestão de Aplicações](add-application-portal-setup-oidc-sso.md)
- [Opções de início de sessão único](sso-options.md)
- [How to: Iniciar sessão de qualquer utilizador do Azure Active Directory com o padrão de aplicação multi-inquilino](../develop/howto-convert-app-to-be-multi-tenant.md)
