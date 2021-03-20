---
title: Diretório Ativo Azure para desenvolvedores (v1.0) visão geral
description: Este artigo fornece uma visão geral da assinatura em contas de trabalho e escola da Microsoft, utilizando o Azure Ative Directory v1.0 endpoint e plataforma.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117673"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Diretório Ativo Azure para desenvolvedores (v1.0) visão geral

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Active Directory (Azure AD) é um serviço de identidade na cloud que permite que os programadores criem aplicações que iniciam a sessão dos utilizadores de forma segura com uma conta escolar ou profissional da Microsoft. O Azure AD suporta programadores que criam aplicações de inquilino único e de linha de negócio (LOB), bem como os programadores que procuram desenvolver aplicações multi-inquilino. Para além do início de sessão básico, o Azure AD também permite que as aplicações chamem APIs da Microsoft, como o [Microsoft Graph](/graph/overview), e APIs personalizadas que são criadas na plataforma do Azure AD. Esta documentação mostra como adicionar suporte do Azure AD à sua aplicação através da utilização de protocolos padrão da indústria, como o OAuth2.0 e o OpenID Connect.

> [!NOTE]
> A maioria dos conteúdos desta página foca-se no ponto final e plataforma v1.0, que suporta apenas o trabalho da Microsoft ou contas escolares. Se pretender iniciar scontabilidade de consumo ou pessoal da Microsoft, consulte as informações no [ponto final e plataforma v2.0](../develop/v2-overview.md). O ponto final v2.0 oferece uma experiência de desenvolvimento unificada para apps que querem assinar em todas as identidades da Microsoft.

- [Fundamentos de autenticação](v1-authentication-scenarios.md) Uma introdução à autenticação com Azure AD.
- [Tipos de aplicações](app-types.md) Uma visão geral dos cenários de autenticação que são suportados pelo Azure AD.

## <a name="get-started"></a>Introdução

Os v1.0 quickstarts e tutoriais o percorrem através da construção de uma aplicação na sua plataforma preferida utilizando a Azure AD Authentication Library (ADAL) SDK. Consulte os **v1.0 Quickstarts** e **v1.0 Tutorials** na [plataforma de identidade da Microsoft (Azure Ative Directory para desenvolvedores)](index.yml) para começar.

## <a name="how-to-guides"></a>Guias de procedimentos

Consulte os **guias v1.0 Como obter** informações detalhadas e acompanhá-lo às tarefas mais comuns em Azure AD.

## <a name="reference-topics"></a>Tópicos de referência

Os seguintes artigos fornecem informações detalhadas sobre APIs, mensagens de protocolo e termos que são utilizados no Azure AD.

- [Bibliotecas de Autenticação (ADAL)](active-directory-authentication-libraries.md) Uma visão geral das bibliotecas e SDKs que são fornecidos pela Azure AD.
- [Amostras de código](sample-v1-code.md) Uma lista de todas as amostras de código AD Azure.
- [Glossário](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologia e definições de palavras que são usadas ao longo desta documentação.

## <a name="videos"></a>Vídeos

Consulte [vídeos da plataforma de desenvolvimento do Azure Ative Directory](videos.md) para ajudar a migrar para a nova plataforma de identidade da Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]