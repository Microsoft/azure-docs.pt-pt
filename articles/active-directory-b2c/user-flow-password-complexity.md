---
title: Configurar requisitos de complexidade de senha
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade para as senhas fornecidas pelos consumidores no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 59ac2f74d4753c1f647bedfa2c402e729e25ee62
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847813"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurar requisitos de complexidade para senhas no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) dá suporte à alteração dos requisitos de complexidade para as senhas fornecidas por um usuário final ao criar uma conta. Por padrão, Azure AD B2C usa `Strong` senhas. O Azure AD B2C também dá suporte a opções de configuração para controlar a complexidade das senhas que os clientes podem usar.

## <a name="password-rule-enforcement"></a>Imposição de regra de senha

Durante a inscrição ou a redefinição de senha, um usuário final deve fornecer uma senha que atenda às regras de complexidade. As regras de complexidade de senha são impostas por fluxo de usuário. É possível ter um fluxo de usuário que exija um PIN de quatro dígitos durante a inscrição, enquanto outro fluxo de usuário requer uma cadeia de oito caracteres durante a inscrição. Por exemplo, você pode usar um fluxo de usuário com complexidade de senha diferente para adultos do que para crianças.

A complexidade da senha nunca é imposta durante a entrada. Os usuários nunca são solicitados durante a entrada para alterar sua senha porque ele não atende ao requisito de complexidade atual.

A complexidade da senha pode ser configurada nos seguintes tipos de fluxos de usuário:

- Fluxo de usuário de inscrição ou entrada
- Fluxo de usuário de redefinição de senha

Se você estiver usando políticas personalizadas, poderá ([Configurar a complexidade de senha em uma política personalizada](custom-policy-password-complexity.md)).

## <a name="configure-password-complexity"></a>Configurar a complexidade da senha

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
3. Na portal do Azure, procure e selecione **Azure ad B2C**.
4. Selecione **fluxos de usuário (políticas)** .
2. Selecione um fluxo de usuário e clique em **Propriedades**.
3. Em **complexidade da senha**, altere a complexidade da senha desse fluxo de usuário para **simples**, **forte**ou **personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Uma senha que tenha pelo menos 8 a 64 caracteres. |
| Segura | Uma senha que tenha pelo menos 8 a 64 caracteres. Ele requer 3 de 4 de letras minúsculas, letras maiúsculas, números ou símbolos. |
| Personalizados | Essa opção fornece o maior controle sobre as regras de complexidade de senha.  Ele permite configurar um comprimento personalizado.  Ele também permite aceitar senhas somente de número (Pins). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite que você aceite somente dígitos (Pins) ou o conjunto de caracteres completo.

- Os **números só** permitem dígitos (0-9) durante a inserção de uma senha.
- **All** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite que você controle os requisitos de comprimento da senha.

- O **comprimento mínimo** deve ser pelo menos 4.
- O **comprimento máximo** deve ser maior ou igual ao tamanho mínimo e, no máximo, pode ser de 64 caracteres.

### <a name="character-classes"></a>Classes de caracteres

Permite que você controle os diferentes tipos de caracteres usados na senha.

- **2 de 4: caractere minúsculo, caractere maiúsculo, número (0-9), símbolo** garante que a senha contenha pelo menos dois tipos de caracteres. Por exemplo, um número e um caractere minúsculo.
- **3 de 4: caractere minúsculo, caractere maiúsculo, número (0-9), símbolo** garante que a senha contenha pelo menos dois tipos de caracteres. Por exemplo, um número, um caractere minúsculo e um caractere maiúsculo.
- **4 de 4: caractere minúsculo, caractere maiúsculo, número (0-9), símbolo** garante que a senha contenha todos os tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar na frustração do usuário final. Alguns estudos mostraram que esse requisito não melhora a entropia da senha. Consulte as [diretrizes de senha do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
