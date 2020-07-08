---
title: Versões de fluxo de utilizador em Azure Ative Directory B2C / Microsoft Docs
description: Conheça as versões dos fluxos de utilizador disponíveis no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acebf5239bf8a180f637e4c12c4e03509edb93c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383993"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de utilizador em Azure Ative Directory B2C

Os fluxos de utilizador no Azure Ative Directory B2C (Azure AD B2C) ajudam-no a definir [políticas](user-flow-overview.md) comuns que descrevem plenamente as experiências de identidade do cliente. Estas experiências incluem inscrição, inscrição, reset de palavra-passe ou edição de perfis. No Azure AD B2C, pode selecionar a partir de uma coleção de fluxos de utilizador recomendados e fluxos de pré-visualização do utilizador.

Novos fluxos de utilizadores são adicionados como novas versões. À medida que os fluxos de utilizador se tornarem estáveis, serão recomendados para utilização. Os fluxos do utilizador são marcados como **Recomendado** se tiverem sido testados exaustivamente. Os fluxos do utilizador serão considerados em pré-visualização até que sejam marcados como recomendado. Utilize um fluxo de utilizador recomendado para qualquer aplicação de produção, mas escolha entre outras versões para testar uma nova funcionalidade à medida que se torna disponível. Não deve utilizar versões mais antigas dos fluxos recomendados do utilizador.

>[!IMPORTANT]
> A menos que um fluxo de utilizador seja identificado como **Recomendado,** considera-se que está em *pré-visualização*. Deve utilizar apenas fluxos de utilizador recomendados para as suas aplicações de produção.

## <a name="v1"></a>V1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reposição de palavras-passe | Sim | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Edição de perfis | Sim | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Inscreva-se usando ROPC | Não | Permite que um utilizador com uma conta local assine diretamente em aplicações nativas (sem necessidade de navegador). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li></ul> |
| Iniciar sessão | Não | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>Iniciar s-in</li><li>Redefinição da palavra-passe da força</li><li>Mantenha-me inscrito (KMSI)</ul><br>Não é possível personalizar a interface do utilizador com este fluxo de utilizador. |
| Inscrever-se | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Inscrever-se e iniciar sessão | Sim | Permite que um utilizador crie uma conta ou inscreva-se na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Senha reposta v1.1 | Não | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu email (novo layout de página disponível). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha v2 | Não | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Edição de perfil v2 | Sim | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Assinar em v2 | Não | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>Personalização de página de inscrição</li></ul> |
| Inscreva-se v2 | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Inscreva-se e inscreva-se em v2 | Não | Permite que um utilizador crie uma conta ou assine na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-factor authentication](custom-policy-multi-factor-authentication.md)</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
