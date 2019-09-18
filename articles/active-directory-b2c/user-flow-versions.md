---
title: Versões de fluxo de usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre as versões dos fluxos de usuário disponíveis no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7b3ce6a4e72b90c6fd642b92d4a92eb02f4c92c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063188"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de usuário no Azure Active Directory B2C

>[!IMPORTANT]
> Qualquer fluxo de usuário listado no artigo é considerado em visualização pública, a menos que seja identificado como **recomendado**. Você só deve usar fluxos de usuário recomendados para seus aplicativos de produção.

O usuário flui em Azure Active Directory B2C (Azure AD B2C) ajuda a configurar políticas comuns que descrevem totalmente [as](active-directory-b2c-reference-policies.md) experiências de identidade do cliente. Essas experiências incluem inscrição, entrada, redefinição de senha ou edição de perfil. No Azure AD B2C, você pode selecionar de uma coleção de fluxos de usuário recomendados e fluxos de usuário de visualização.

Novos fluxos de usuário são adicionados como novas versões. À medida que os fluxos de usuário se tornam estáveis, eles serão recomendados para uso. Os fluxos de usuário são marcados como **recomendados** se foram totalmente testados. Os fluxos de usuário serão considerados em visualização até serem marcados como recomendados. Use um fluxo de usuário recomendado para qualquer aplicativo de produção, mas escolha entre outras versões para testar a nova funcionalidade à medida que ela se tornar disponível. Você não deve usar versões mais antigas de fluxos de usuário recomendados.

## <a name="v1"></a>V1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reposição de palavra-passe | Sim | Permite que um usuário escolha uma nova senha depois de verificar seu email. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Definições de tokens de compatibilidade</li><li>[Requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edição de perfil | Sim | Permite que um usuário Configure seus atributos de usuário. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li></ul> |
| Proprietário do recurso | Não | Permite que um usuário com uma conta local entre diretamente em aplicativos nativos (nenhum navegador é necessário). Usando esse fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li></ul> |
| Iniciar sessão | Não | Permite que um usuário entre em sua conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>Bloquear entrada</li><li>Forçar redefinição de senha</li><li>Mantenha-me conectado (KMSI)</ul><br>Não é possível personalizar a interface do usuário com esse fluxo de usuário. |
| Aderir | Não | Permite que um usuário crie uma conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Inscrever e iniciar sessão | Sim | Permite que um usuário crie uma conta ou entre em sua conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Repor a palavra-passe v2 | Não | Permite que um usuário escolha uma nova senha depois de verificar seu email. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Definições de tokens de compatibilidade</li><li>[Retenção de idade](basic-age-gating.md)</li><li>[requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Iniciar sessão v2 | Não | Permite que um usuário entre em sua conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Retenção de idade](basic-age-gating.md)</li><li>Personalização da página de entrada</li></ul> |
| Inscrever-se v2 | Não | Permite que um usuário crie uma conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Tempo de vida do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Retenção de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Inscrever-se e entrar em v2 | Não | Permite que um usuário crie uma conta ou entre em sua conta. Usando esse fluxo de usuário, você pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Retenção de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](active-directory-b2c-reference-password-complexity.md)</li></ul> |
