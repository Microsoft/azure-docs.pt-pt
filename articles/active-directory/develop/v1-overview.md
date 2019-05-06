---
title: O Azure Active Directory para descrição geral de programadores (versão 1.0)
description: Este artigo fornece uma descrição geral do início de sessão Microsoft trabalhar contas escolares ou profissionais com o ponto final de versão 1.0 do Azure Active Directory e a plataforma.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2af95d979e90f25c013f5112322d830232199a4b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067652"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>O Azure Active Directory para descrição geral de programadores (versão 1.0)

O Azure Active Directory (Azure AD) é um serviço de identidade na cloud que permite que os programadores criem aplicações que iniciam a sessão dos utilizadores de forma segura com uma conta escolar ou profissional da Microsoft. O Azure AD suporta programadores que criam aplicações de inquilino único e de linha de negócio (LOB), bem como os programadores que procuram desenvolver aplicações multi-inquilino. Para além do início de sessão básico, o Azure AD também permite que as aplicações chamem APIs da Microsoft, como o [Microsoft Graph](https://docs.microsoft.com/graph/overview), e APIs personalizadas que são criadas na plataforma do Azure AD. Esta documentação mostra como adicionar suporte do Azure AD à sua aplicação através da utilização de protocolos padrão da indústria, como o OAuth2.0 e o OpenID Connect.

> [!NOTE]
> A maioria do conteúdo desta página enfoca a v1.0 ponto final e a plataforma, que oferece suporte apenas a Microsoft contas escolares ou. Se quiser iniciar sessão em contas Microsoft pessoais ou de consumidor, consulte as informações sobre o [plataforma e de ponto final v2.0](v2-overview.md). O ponto final v2.0 oferece uma experiência de programação unificada para as aplicações que pretende iniciar sessão em todas as identidades da Microsoft.

| | |
| --- | --- |
|[Noções básicas de autenticação](authentication-scenarios.md) | Introdução à autenticação com o Azure AD. |
|[Tipos de aplicações](app-types.md) | Descrição geral dos cenários autenticação que são suportado pelo Azure AD. |
| | |

## <a name="get-started"></a>Introdução

O v1.0 guias de introdução e tutoriais explicam como criar uma aplicação na sua plataforma preferida com o Azure AD Authentication Library (ADAL) SDK. Consulte a **v1.0 inícios Rápidos** e **v1.0 tutoriais** na [plataforma de identidade (Azure Active Directory para programadores) da Microsoft](index.yml) para começar a utilizar.

## <a name="how-to-guides"></a>Guias de procedimentos

Consulte a **v1.0 guias de procedimentos** para informações detalhadas e orientações passo a passo das tarefas mais comuns no Azure AD.

## <a name="reference-topics"></a>Tópicos de referência

Os seguintes artigos fornecem informações detalhadas sobre APIs, mensagens de protocolo e termos que são utilizados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de Autenticação (ADAL)](active-directory-authentication-libraries.md)   | Descrição geral de bibliotecas e SDKs que são fornecidos pelo Azure AD. |
| [Exemplos de código](sample-v1-code.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](developer-glossary.md)                                      | Terminologia e definições de palavras que são utilizadas nesta documentação. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
