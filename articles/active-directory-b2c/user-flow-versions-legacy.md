---
title: Versões de fluxo de utilizador legado no Azure Ative Directory B2C Microsoft Docs
description: Saiba mais sobre as versões antigas dos fluxos de utilizador disponíveis no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7cdfd6cb8947fb3b56fcbfe92874c8b9b27a8db1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108132"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de utilizador legado no Azure Ative Directory B2C

> [!IMPORTANT]
> Este artigo descreve o método de versão legado para os fluxos de utilizador, que oferece versões V1 (prontas para a produção) e versões V1.1 e V2 (pré-visualização) dos fluxos de utilizador. Ambientes que não a nuvem pública Azure continuarão a usar este método de versão antiga. Na nuvem pública Azure, este método é substituído por [novas versões **Recomendadas** e **de Pré-visualização.**](user-flow-versions.md)
> 
Os fluxos de utilizador no Azure Ative Directory B2C (Azure AD B2C) ajudam-no a definir [políticas](user-flow-overview.md) comuns que descrevem plenamente as experiências de identidade do cliente. Estas experiências incluem inscrição, inscrição, reset de palavra-passe ou edição de perfis.

Na tabela abaixo, a menos que um fluxo de utilizador seja identificado como **Recomendado,** considera-se que está em *pré-visualização*. Deve utilizar apenas fluxos de utilizador recomendados para as suas aplicações de produção.

## <a name="v1"></a>V1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reposição de palavras-passe | Sim | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Edição de perfis | Sim | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Inscreva-se usando ROPC | Não | Permite que um utilizador com uma conta local assine diretamente em aplicações nativas (sem necessidade de navegador). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li></ul> |
| Iniciar sessão | Não | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>Iniciar s-in</li><li>Redefinição da palavra-passe da força</li><li>Mantenha-me inscrito (KMSI)</ul><br>Não é possível personalizar a interface do utilizador com este fluxo de utilizador. |
| Inscrever-se | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Inscrever-se e iniciar sessão | Sim | Permite que um utilizador crie uma conta ou inscreva-se na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Senha reposta v1.1 | Não | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu email (novo layout de página disponível). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha v2 | Não | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Edição de perfil v2 | Sim | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Assinar em v2 | Não | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>Personalização de página de inscrição</li></ul> |
| Inscreva-se v2 | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Inscreva-se e inscreva-se em v2 | Não | Permite que um utilizador crie uma conta ou assine na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |