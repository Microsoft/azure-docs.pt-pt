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
ms.openlocfilehash: e819073c3bc723a66f8beb2f9b2a094a07233ca9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427684"
---
# <a name="understand-oidc-based-single-sign-on"></a>Compreenda o sign-on único baseado no OIDC
Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. Este artigo entra em mais detalhes sobre apps que usam a norma OpenID Connect para implementar um único sinal de entrada. 

## <a name="before-you-begin"></a>Antes de começar
O processo de adição de uma app ao seu inquilino Azure Ative Directory depende do tipo de sinal único da aplicação implementada. Para saber mais sobre as opções de inscrição única disponíveis para aplicações que podem utilizar a Azure AD para gestão de identidade, consulte [as opções de inscrição únicas](sso-options.md). Este artigo abrange aplicações baseadas em OIDC.


## <a name="basic-oidc-configuration"></a>Configuração básica do OIDC
Na [série quickstart,](add-application-portal-setup-oidc-sso.md)há um artigo sobre configurar um único sign-on. Nele, você aprende a adicionar uma aplicação baseada em OIDC ao seu inquilino Azure.

A coisa boa ao adicionar uma aplicação que usa o padrão OIDC para um único sign-on é que a configuração é mínima. Aqui está um pequeno vídeo mostrando como adicionar uma aplicação baseada em OIDC ao seu inquilino.

Adicionar uma aplicação baseada em OIDC no Azure Ative Directory

> [!VIDEO https://www.youtube.com/embed/4kv-upsZCI0]

## <a name="next-steps"></a>Passos seguintes

- [Quickstart Series em Gestão de Aplicações](add-application-portal-setup-oidc-sso.md)
- [Opções de início de sessão único](sso-options.md)
- [How to: Iniciar sessão de qualquer utilizador do Azure Active Directory com o padrão de aplicação multi-inquilino](../develop/howto-convert-app-to-be-multi-tenant.md)