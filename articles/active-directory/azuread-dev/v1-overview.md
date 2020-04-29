---
title: Diretório Ativo Azure para programadores (v1.0) visão geral
description: Este artigo fornece uma visão geral da assinatura nas contas de trabalho e escola da Microsoft utilizando o ponto final do Azure Ative Directory v1.0 e plataforma.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154496"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Diretório Ativo Azure para programadores (v1.0) visão geral

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Active Directory (Azure AD) é um serviço de identidade na cloud que permite que os programadores criem aplicações que iniciam a sessão dos utilizadores de forma segura com uma conta escolar ou profissional da Microsoft. O Azure AD suporta programadores que criam aplicações de inquilino único e de linha de negócio (LOB), bem como os programadores que procuram desenvolver aplicações multi-inquilino. Para além do início de sessão básico, o Azure AD também permite que as aplicações chamem APIs da Microsoft, como o [Microsoft Graph](https://docs.microsoft.com/graph/overview), e APIs personalizadas que são criadas na plataforma do Azure AD. Esta documentação mostra como adicionar suporte do Azure AD à sua aplicação através da utilização de protocolos padrão da indústria, como o OAuth2.0 e o OpenID Connect.

> [!NOTE]
> A maioria dos conteúdos desta página foca-se no ponto final v1.0 e plataforma, que suporta apenas o trabalho da Microsoft ou contas escolares. Se quiser assinar contas de consumo ou de Microsoft pessoais, consulte as informações no [ponto final e plataforma v2.0](../develop/v2-overview.md). O ponto final v2.0 oferece uma experiência unificada de desenvolvedor para apps que queiram assinar em todas as identidades da Microsoft.

| | |
| --- | --- |
|[Noções básicas sobre autenticação](v1-authentication-scenarios.md) | Introdução à autenticação com o Azure AD. |
|[Tipos de aplicações](app-types.md) | Descrição geral dos cenários autenticação que são suportado pelo Azure AD. |
| | |

## <a name="get-started"></a>Introdução

Os quickstarts e tutoriais v1.0 passam por você através da construção de uma app na sua plataforma preferida usando o Azure AD Authentication Library (ADAL) SDK. Consulte os **quickstarts v1.0** e **os tutoriais v1.0** na [plataforma de identidade da Microsoft (Azure Ative Directory para desenvolvedores)](index.yml) para começar.

## <a name="how-to-guides"></a>Guias de procedimentos

Consulte os **guias v1.0 Como obter** informações detalhadas e passar as tarefas mais comuns em Azure AD.

## <a name="reference-topics"></a>Tópicos de referência

Os seguintes artigos fornecem informações detalhadas sobre APIs, mensagens de protocolo e termos que são utilizados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de Autenticação (ADAL)](active-directory-authentication-libraries.md)   | Descrição geral de bibliotecas e SDKs que são fornecidos pelo Azure AD. |
| [Amostras de código](sample-v1-code.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Terminologia e definições de palavras que são utilizadas nesta documentação. |
|  |  |

## <a name="videos"></a>Vídeos

Consulte vídeos da plataforma de [desenvolvimento do Azure Ative Directory](videos.md) para ajudar a migrar para a nova plataforma de identidade da Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
