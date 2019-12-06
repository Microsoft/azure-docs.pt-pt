---
title: Como proibir senhas fracas no Azure AD – Azure Active Directory
description: Proíba senhas fracas de seu envirionment com o Azure AD banido dinamicamente passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c58371edffa4400c2c2b3efd37c3ce6d7c3cad95
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847920"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurando a lista personalizada de senhas banidas

Muitas organizações acham que seus usuários criam senhas usando palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa, deixando-as fáceis de adivinhar. A lista de senhas banidas personalizada da Microsoft permite que as organizações adicionem cadeias de caracteres para avaliar e bloquear, além da lista de senhas globais banidas, quando os usuários e administradores tentam alterar ou redefinir uma senha.

## <a name="add-to-the-custom-list"></a>Adicionar à lista personalizada

A configuração da lista de senhas excluídas personalizada requer uma licença Azure Active Directory Premium P1 ou P2. Para obter informações mais detalhadas sobre Azure Active Directory licenciamento, consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory**, métodos de **autenticação**e proteção por **senha**.
1. Defina a opção **impor lista personalizada**para **Sim**.
1. Adicionar cadeias de caracteres à **lista personalizada de senhas banidas**, uma cadeia de texto por linha
   * A lista de senhas excluídas personalizada pode conter até 1000 termos.
   * A lista de senhas proibidas personalizada não diferencia maiúsculas de minúsculas.
   * A lista de senhas excluídas personalizada considera a substituição de caracteres comum.
      * Exemplo: "o" e "0" ou "a" e "\@"
   * O comprimento mínimo da cadeia de caracteres é de quatro caracteres e o máximo é 16 caracteres.
1. Depois de adicionar todas as cadeias de caracteres, clique em **salvar**.

> [!NOTE]
> Pode levar várias horas para que as atualizações da lista de senhas excluídas personalizada sejam aplicadas.

> [!NOTE]
> A lista de senhas excluídas personalizada é limitada a ter no máximo 1000 termos. Ele não foi projetado para bloquear listas de senhas extremamente grandes. Para aproveitar totalmente os benefícios da lista personalizada de senhas banidas, a Microsoft recomenda que você primeiro revise e entenda o design e o uso pretendidos da lista de senhas excluídas personalizadas (consulte a [lista de senhas excluídas personalizada](concept-password-ban-bad.md#custom-banned-password-list)) e também o algoritmo de avaliação de senha (consulte [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Modifique a lista personalizada de senha banida em métodos de autenticação no portal do Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Como funciona

Cada vez que um usuário ou administrador redefine ou altera uma senha do Azure AD, ele flui pelas listas de senhas banidas para confirmar que ela não está em uma lista. Essa verificação é incluída em qualquer senha definida ou alterada usando o Azure AD.

## <a name="what-do-users-see"></a>O que os utilizadores veem

Quando um usuário tenta redefinir uma senha para algo que seria banido, ele vê uma das seguintes mensagens de erro:

* Infelizmente, sua senha contém uma palavra, frase ou padrão que torna sua senha facilmente adivinhada. Tente novamente com uma senha diferente.
* Infelizmente, você não pode usar essa senha porque ela contém palavras ou caracteres que foram bloqueados pelo administrador. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Passos seguintes

[Visão geral conceitual das listas de senhas banidas](concept-password-ban-bad.md)

[Visão geral conceitual da proteção de senha do Azure AD](concept-password-ban-bad-on-premises.md)

[Habilitar a integração local com as listas de senhas banidas](howto-password-ban-bad-on-premises.md)
