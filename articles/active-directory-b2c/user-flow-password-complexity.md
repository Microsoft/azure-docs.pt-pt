---
title: Configurar requisitos de complexidade de palavras-passe
titleSuffix: Azure AD B2C
description: Como configurar requisitos de complexidade para as palavras-passe fornecidas pelos consumidores no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9c7f0ead3a2f93d7f390106b8e7471d58b709023
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481568"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configure requisitos de complexidade para senhas no Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) suporta a alteração dos requisitos de complexidade das palavras-passe fornecidas por um utilizador final ao criar uma conta. Por padrão, o Azure AD B2C utiliza `Strong` senhas. O Azure AD B2C também suporta opções de configuração para controlar a complexidade das palavras-passe que os clientes podem usar.

## <a name="password-rule-enforcement"></a>Aplicação da regra da palavra-passe

Durante a inscrição ou a redefinição da palavra-passe, um utilizador final deve fornecer uma palavra-passe que cumpra as regras de complexidade. As regras de complexidade da palavra-passe são aplicadas por fluxo de utilizador. É possível que um fluxo de utilizador exija um pino de quatro dígitos durante a inscrição, enquanto outro fluxo de utilizador requer uma corda de oito caracteres durante a inscrição. Por exemplo, pode utilizar um fluxo de utilizador com uma complexidade de senha diferente para adultos do que para crianças.

A complexidade da palavra-passe nunca é aplicada durante a entrada. Os utilizadores nunca são solicitados durante o s-in a alterar a sua palavra-passe porque não cumpre o requisito de complexidade atual.

A complexidade da palavra-passe pode ser configurada nos seguintes tipos de fluxos de utilizador:

- Fluxo de utilizador de inscrição ou de entrada
- Redefinição de senha fluxo de utilizador

Se estiver a utilizar políticas personalizadas, pode[(configurar a complexidade da palavra-passe numa política personalizada).](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Configure a complexidade da palavra-passe

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador**.
2. Selecione um fluxo de utilizador e clique em **Propriedades**.
3. Sob **a complexidade da Palavra-Passe,** altere a complexidade da palavra-passe para este fluxo de utilizador para **Simples,** **Forte**ou **Personalizado**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Uma palavra-passe que é de pelo menos 8 a 64 caracteres. |
| Forte | Uma palavra-passe que é de pelo menos 8 a 64 caracteres. Requer 3 de 4 maiúsculas, maiúsculas, números ou símbolos. |
| Personalizado | Esta opção proporciona o maior controlo sobre as regras de complexidade da palavra-passe.  Permite configurar um comprimento personalizado.  Também permite aceitar senhas só de número (pinos). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite-lhe aceitar apenas dígitos (pinos) ou o conjunto de caracteres completo.

- **Os números só** permitem dígitos (0-9) durante a insintrodução de uma palavra-passe.
- **Tudo** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite-lhe controlar os requisitos de comprimento da palavra-passe.

- **O comprimento mínimo** deve ser de, pelo menos, 4.
- **O comprimento máximo** deve ser maior ou igual ao comprimento mínimo e, no máximo, pode ser de 64 caracteres.

### <a name="character-classes"></a>Aulas de caráter

Permite-lhe controlar os diferentes tipos de caracteres utilizados na palavra-passe.

- **2 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos dois tipos de caracteres. Por exemplo, um número e um caráter minúsculo.
- **3 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém pelo menos três tipos de caracteres. Por exemplo, um número, um caráter minúsculo e um personagem maiúscula.
- **4 de 4: Caracteres minúsculos, caracteres maiúsculas, Número (0-9), Símbolo** garante que a palavra-passe contém tudo para tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do utilizador final. Alguns estudos demonstraram que este requisito não melhora a entropia da palavra-passe. Ver [Diretrizes de Senha do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
