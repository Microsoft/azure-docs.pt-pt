---
title: Versões de fluxo de utilizador no Diretório Ativo Do Azure B2C / Microsoft Docs
description: Conheça as versões dos fluxos de utilizadores disponíveis no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185627"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de utilizador no Diretório Ativo Azure B2C

Os fluxos de utilizadores em Azure Ative Directory B2C (Azure AD B2C) ajudam-no a criar [políticas](user-flow-overview.md) comuns que descrevam plenamente as experiências de identidade do cliente. Estas experiências incluem inscrição, inscrição, reset de password ou edição de perfil. No Azure AD B2C, pode selecionar a partir de uma coleção de fluxos de utilizadores recomendados e fluxos de utilizador de pré-visualização.

Novos fluxos de utilizadores são adicionados como novas versões. À medida que os fluxos de utilizador se tornam estáveis, serão recomendados para utilização. Os fluxos do utilizador são marcados como **Recomendados** se tiverem sido cuidadosamente testados. Os fluxos de utilizadores serão considerados em pré-visualização até serem marcados como recomendado. Utilize um fluxo de utilizador recomendado para qualquer aplicação de produção, mas escolha entre outras versões para testar novas funcionalidades à medida que estiver disponível. Não deve utilizar versões mais antigas dos fluxos recomendados de utilizadores.

>[!IMPORTANT]
> A menos que um fluxo de utilizador seja identificado como **Recomendado,** considera-se que está em *pré-visualização*. Deve utilizar apenas fluxos de utilizadores recomendados para as suas aplicações de produção.

## <a name="v1"></a>V1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reposição de palavras-passe | Sim | Permite que um utilizador escolha uma nova senha depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade simbólica</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Edição de perfil | Sim | Permite que um utilizador configure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li></ul> |
| Inscreva-se usando ROPC | Não | Permite a um utilizador com uma conta local iniciar sessão diretamente em aplicações nativas (sem necessidade de navegador). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li></ul> |
| Iniciar sessão | Não | Permite que um utilizador inscrete na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li><li>Bloquear a inscrição</li><li>Reset de palavra-passe de força</li><li>Mantenha-me assinado (KMSI)</ul><br>Não é possível personalizar a interface do utilizador com este fluxo de utilizador. |
| Inscrever-se | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Inscrever-se e iniciar sessão | Sim | Permite que um utilizador crie uma conta ou inscreva-se na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reset de palavra-passe v1.1 | Não | Permite que um utilizador escolha uma nova senha depois de verificar o seu email (novo layout de página disponível). Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade simbólica</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Fluxo de utilizador | Recomendado | Descrição |
| --------- | ----------- | ----------- |
| Reset de palavra-passe v2 | Não | Permite que um utilizador escolha uma nova senha depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Definições de compatibilidade simbólica</li><li>[Gating idade](basic-age-gating.md)</li><li>[requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Edição de perfil v2 | Sim | Permite que um utilizador configure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li></ul> |
| Inscreva-se em v2 | Não | Permite que um utilizador inscrete na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li><li>[Gating idade](basic-age-gating.md)</li><li>Personalização da página de inscrição</li></ul> |
| Inscreva-se v2 | Não | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Token vida](tokens-overview.md)</li><li>Definições de compatibilidade simbólica</li><li>Comportamento da sessão</li><li>[Gating idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
| Inscreva-se e inscreva-se na v2 | Não | Permite que um utilizador crie uma conta ou assine na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Gating idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](user-flow-password-complexity.md)</li></ul> |
