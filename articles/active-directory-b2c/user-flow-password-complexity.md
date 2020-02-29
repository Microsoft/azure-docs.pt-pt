---
title: Configurar requisitos de complexidade de senhas
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade das palavras-passe fornecidas pelos consumidores no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185646"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurar requisitos de complexidade para senhas no Diretório Ativo Azure B2C

O Azure Ative Directory B2C (Azure AD B2C) suporta a alteração dos requisitos de complexidade das palavras-passe fornecidas por um utilizador final ao criar uma conta. Por predefinição, o Azure AD B2C utiliza palavras-passe `Strong`. O Azure AD B2C também suporta opções de configuração para controlar a complexidade das palavras-passe que os clientes podem usar.

## <a name="password-rule-enforcement"></a>Aplicação da regra da palavra-passe

Durante o reset de inscrição ou palavra-passe, um utilizador final deve fornecer uma palavra-passe que cumpra as regras de complexidade. As regras de complexidade da palavra-passe são aplicadas por fluxo de utilizador. É possível que um fluxo de utilizador exija um pino de quatro dígitos durante a inscrição, enquanto outro fluxo de utilizador requer uma cadeia de oito caracteres durante a inscrição. Por exemplo, pode utilizar um fluxo de utilizador com diferente complexidade de senha para adultos do que para crianças.

A complexidade da palavra-passe nunca é aplicada durante o iniciar o sessão. Os utilizadores nunca são solicitados durante o sessão para alterar a sua palavra-passe porque não cumpre o requisito de complexidade atual.

A complexidade da palavra-passe pode ser configurada nos seguintes tipos de fluxos de utilizadores:

- Fluxo de utilizador de inscrição ou de entrada
- Fluxo de utilizador de redefinição de palavra-passe

Se estiver a usar políticas personalizadas, pode[(configurar a complexidade da palavra-passe numa política personalizada).](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Configurar a complexidade da palavra-passe

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)** .
2. Selecione um fluxo de utilizador e clique em **Propriedades**.
3. Em termos de complexidade de **password,** altere a complexidade da palavra-passe para este fluxo de utilizador para **Simple**, **Strong**, ou **Custom**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Uma senha que é de pelo menos 8 a 64 caracteres. |
| Forte | Uma senha que é de pelo menos 8 a 64 caracteres. Requer 3 em cada 4 minúsculas, maiúsculas, números ou símbolos. |
| Personalizado | Esta opção fornece o maior controlo sobre as regras de complexidade da palavra-passe.  Permite configurar um comprimento personalizado.  Também permite aceitar senhas só para números (pinos). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite-lhe aceitar apenas dígitos (pinos) ou o conjunto completo de caracteres.

- **Os números só** permitem dígitos (0-9) ao introduzir uma senha.
- **Tudo** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite-lhe controlar os requisitos de comprimento da palavra-passe.

- **O comprimento mínimo** deve ser de pelo menos 4.
- **O comprimento máximo** deve ser maior ou igual ao comprimento mínimo e, no máximo, pode ser de 64 caracteres.

### <a name="character-classes"></a>Aulas de personagens

Permite-lhe controlar os diferentes tipos de caracteres utilizados na palavra-passe.

- **2 de 4: Personagem minúsculo, caracteres maiúsculos, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos dois tipos de caracteres. Por exemplo, um número e um carácter minúsculo.
- **3 de 4: Personagem minúsculo, caracteres maiúsculos, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos três tipos de caracteres. Por exemplo, um número, um personagem minúsculo e um personagem maiúsculo.
- **4 de 4: Personagem minúsculo, caracteres maiúsculos, Número (0-9), Símbolo** garante que a palavra-passe contém tudo para tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do utilizador final. Alguns estudos demonstraram que esta exigência não melhora a entropia da palavra-passe. Ver [Diretrizes de Senha NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
