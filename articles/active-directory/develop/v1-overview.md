---
title: Visão geral de Azure Active Directory para desenvolvedores (v 1.0)
description: Este artigo fornece uma visão geral da assinatura de contas corporativas e de estudante da Microsoft usando o ponto de extremidade e a plataforma Azure Active Directory v 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e790728cbc7415532754d10773d466e9a0280422
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373916"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Visão geral de Azure Active Directory para desenvolvedores (v 1.0)

O Azure Active Directory (Azure AD) é um serviço de identidade na cloud que permite que os programadores criem aplicações que iniciam a sessão dos utilizadores de forma segura com uma conta escolar ou profissional da Microsoft. O Azure AD suporta programadores que criam aplicações de inquilino único e de linha de negócio (LOB), bem como os programadores que procuram desenvolver aplicações multi-inquilino. Para além do início de sessão básico, o Azure AD também permite que as aplicações chamem APIs da Microsoft, como o [Microsoft Graph](https://docs.microsoft.com/graph/overview), e APIs personalizadas que são criadas na plataforma do Azure AD. Esta documentação mostra como adicionar suporte do Azure AD à sua aplicação através da utilização de protocolos padrão da indústria, como o OAuth2.0 e o OpenID Connect.

> [!NOTE]
> A maior parte do conteúdo nesta página se concentra no ponto de extremidade v 1.0 e na plataforma, que dá suporte apenas a contas corporativas ou de estudante da Microsoft. Se você quiser entrar no consumidor ou em contas pessoais da Microsoft, consulte as informações no [ponto de extremidade v 2.0 e plataforma](v2-overview.md). O ponto de extremidade v 2.0 oferece uma experiência de desenvolvedor unificada para aplicativos que desejam entrar em todas as identidades da Microsoft.

| | |
| --- | --- |
|[Noções básicas de autenticação](v1-authentication-scenarios.md) | Introdução à autenticação com o Azure AD. |
|[Tipos de aplicações](app-types.md) | Descrição geral dos cenários autenticação que são suportado pelo Azure AD. |
| | |

## <a name="get-started"></a>Introdução

Os guias de início rápido e os tutoriais do v 1.0 orientam você pela criação de um aplicativo em sua plataforma preferida usando o SDK da ADAL (biblioteca de autenticação do Azure AD). Consulte os tutoriais de **início rápido v 1.0** e **v 1.0** na [plataforma Microsoft Identity (Azure Active Directory para desenvolvedores)](index.yml) para começar.

## <a name="how-to-guides"></a>Guias de procedimentos

Consulte os **guias de instruções do v 1.0** para obter informações detalhadas e orientações das tarefas mais comuns no Azure AD.

## <a name="reference-topics"></a>Tópicos de referência

Os seguintes artigos fornecem informações detalhadas sobre APIs, mensagens de protocolo e termos que são utilizados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de Autenticação (ADAL)](active-directory-authentication-libraries.md)   | Descrição geral de bibliotecas e SDKs que são fornecidos pelo Azure AD. |
| [Exemplos de código](sample-v1-code.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](developer-glossary.md)                                      | Terminologia e definições de palavras que são utilizadas nesta documentação. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
