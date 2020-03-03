---
title: Grupos dinâmicos e colaboração B2B - Diretório Ativo Azure / Microsoft Docs
description: Mostra como usar grupos dinâmicos Azure AD com colaboração Azure Ative Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226893"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e colaboração Azure Ative Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da adesão ao grupo de segurança para o Azure Ative Directory (Azure AD) está disponível [no portal Azure](https://portal.azure.com). Os administradores podem definir regras para povoar grupos que são criados em AD Azure com base em atributos de utilizador (como userType, departamento ou país/região). Os membros podem ser automaticamente adicionados ou removidos de um grupo de segurança com base nos seus atributos. Estes grupos podem fornecer acesso a aplicações ou recursos na nuvem (sites, documentos do SharePoint) e atribuir licenças aos membros. Leia mais sobre grupos dinâmicos em [grupos dedicados no Diretório Ativo Azure.](../active-directory-accessmanagement-dedicated-groups.md)

O [licenciamento Azure Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) adequado é necessário para criar e utilizar grupos dinâmicos. Saiba mais no artigo [Criar regras baseadas em atributos para a adesão](../users-groups-roles/groups-dynamic-membership.md)dinâmica do grupo no Diretório Ativo azure.

## <a name="creating-an-all-users-dynamic-group"></a>Criar um grupo dinâmico "todos os utilizadores"
Você pode criar um grupo contendo todos os utilizadores dentro de um inquilino usando uma regra de adesão. Quando os utilizadores são adicionados ou removidos do inquilino no futuro, a adesão do grupo é ajustada automaticamente.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é atribuída ao administrador global ou ao papel de administrador de utilizador no inquilino.
1. Selecione **Azure Active Directory**.
2. Under **Manage**, selecione **Grupos**, e, em seguida, selecione **Novo grupo**.
1. Na página **do Novo Grupo,** em **tipo de grupo,** selecione **Segurança**. Insira um nome de **grupo** e **descrição** do grupo para o novo grupo. 
2. No **tipo de Membro,** selecione **'Utilizador Dinâmico'** ' e, em seguida, selecione **Adicionar consulta dinâmica**. 
4. Acima da caixa de texto **sintaxe regra,** selecione **Editar**. Na página de **sintaxe** da regra Editar, digite a seguinte expressão na caixa de texto:

   ```
   user.objectId -ne null
   ```
1. Selecione **OK**. A regra aparece na caixa de sintaxe de regra:

   ![Sintaxe de regras para todos os utilizadores grupo dinâmico](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selecione **Guardar**. O novo grupo dinâmico passará a incluir utilizadores convidados B2B, bem como utilizadores membros.


1. Selecione **Criar** na página do **novo grupo** para criar o grupo.

## <a name="creating-a-group-of-members-only"></a>Criar apenas um grupo de membros

Se quiser que o seu grupo exclua os utilizadores convidados e inclua apenas membros do seu inquilino, crie um grupo dinâmico como acima descrito, mas na caixa de **sintaxe de regra,** insira a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

A imagem seguinte mostra a sintaxe de regra para um grupo dinâmico modificado para incluir apenas membros e excluir os hóspedes.

![Mostra regra em que o tipo de utilizador é igual a membro](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Criar apenas um grupo de hóspedes

Também pode achar útil criar um novo grupo dinâmico que contenha apenas utilizadores convidados, para que possa aplicar políticas (como as políticas de Acesso Condicional Azure AD) aos mesmos. Crie um grupo dinâmico como descrito acima, mas na caixa de **sintaxe** de regra, insira a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

A imagem que se segue mostra a sintaxe de regras para um grupo dinâmico modificado para incluir apenas os hóspedes e excluir os utilizadores membros.

![Mostra regra onde o tipo de utilizador é igual a hóspede](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Passos seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)
- [Adicionar um utilizador de colaboração B2B a um papel](add-guest-to-role.md)
- [Acesso Condicional para utilizadores de colaboração B2B](conditional-access.md)

