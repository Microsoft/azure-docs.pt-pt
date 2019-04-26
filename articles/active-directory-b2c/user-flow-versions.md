---
title: Versões de fluxo de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre as versões de fluxos de utilizador disponíveis no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b3750768eb7346938faec7e3c1ead232923a68bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359760"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de utilizador no Azure Active Directory B2C

>[!IMPORTANT]
> Qualquer fluxo de utilizador listado no artigo é considerado como estando em pré-visualização pública, a menos que identificado como **recomendado**. Só deve utilizar fluxos de utilizador recomendado para as suas aplicações de produção.

Fluxos de utilizador no Azure Active Directory (Azure AD) B2C ajudá-lo a configurar comum [políticas](active-directory-b2c-reference-policies.md) que descrevem totalmente experiências de identidade do cliente. Estas experiências incluem a reposição de palavra-passe de inscrição, início de sessão, ou a edição de perfis. No Azure AD B2C, pode selecionar a partir de uma coleção de fluxos de utilizador recomendada e fluxos de utilizador de pré-visualização. 

Novos fluxos de utilizador são adicionados como novas versões. Como os fluxos de utilizador se tornam estáveis, vai ser recomendados para utilização. Fluxos de utilizador são marcados como **recomendado** se tiver sido totalmente testadas. Fluxos do utilizador serão considerados em pré-visualização até marcada como recomendado. Utilizar um fluxo de utilizador recomendada para qualquer aplicativo de produção, mas a escolha de outras versões para testar a nova funcionalidade, assim que estiver disponível. Não deve usar as versões mais antigas de fluxos de utilizador recomendada.

## <a name="v1"></a>V1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reposição de palavras-passe | Sim | Permite que um utilizador escolher uma nova palavra-passe depois de verificar o respetivo e-mail. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Definições de tokens de compatibilidade</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edição de perfil | Sim | Permite que um utilizador configurar seus atributos de utilizador. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li></ul> |
| proprietário do recurso | Não | Permite que um utilizador com uma conta local iniciar sessão diretamente em aplicativos nativos (não foi necessário nenhum navegador). Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li></ul> |
| Iniciar sessão | Não | Permite que um utilizador inicie sessão na sua conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>Bloquear início de sessão</li><li>Forçar a reposição de palavra-passe</li><li>Manter sessão iniciada (KMSI)</ul><br>Não é possível personalizar a interface de utilizador com este fluxo de utilizador. |
| Inscrever-se | Não | Permite que um usuário criar uma conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Inscrever-se e iniciar sessão | Sim | Permite que um usuário criar uma conta ou iniciar sessão na sua conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Repor a palavra-passe v2 | Não | Permite que um utilizador escolher uma nova palavra-passe depois de verificar o respetivo e-mail. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Definições de tokens de compatibilidade</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Iniciar sessão v2 | Não | Permite que um utilizador inicie sessão na sua conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>Personalização de página de início de sessão</li></ul> |
| Inscrever-se v2 | Não | Permite que um usuário criar uma conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Duração do token](active-directory-b2c-reference-tokens.md)</li><li>Definições de tokens de compatibilidade</li><li>Comportamento da sessão</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Inscrever e iniciar sessão v2 | Não | Permite que um usuário criar uma conta ou inicie sessão na sua conta. Utilizar este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Controlo de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavra-passe](active-directory-b2c-reference-password-complexity.md)</li></ul> |
