---
title: Grupos dinâmicos e colaboração B2B - Azure Ative Directory Microsoft Docs
description: Mostra como usar grupos dinâmicos AD AD com colaboração B2B do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b21f14bc6f80445d1119f34d24281d764fff38c8
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369170"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e colaboração B2B do Diretório Ativo Azure

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da adesão ao grupo de segurança para o Azure Ative Directory (Azure AD) está disponível [no portal Azure](https://portal.azure.com). Os administradores podem definir regras para povoar grupos que são criados em Azure AD com base em atributos do utilizador (como userType, departamento ou país/região). Os membros podem ser automaticamente adicionados ou removidos de um grupo de segurança com base nos seus atributos. Estes grupos podem fornecer acesso a aplicações ou recursos em nuvem (sites, documentos sharePoint) e atribuir licenças aos membros. Leia mais sobre grupos dinâmicos em [grupos dedicados no Azure Ative Directory](../active-directory-accessmanagement-dedicated-groups.md).

O [licenciamento Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) apropriado é necessário para criar e utilizar grupos dinâmicos. Saiba mais no artigo [Crie regras baseadas em atributos para a adesão dinâmica do grupo no Azure Ative Directory](../enterprise-users/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Criar um grupo dinâmico "todos os utilizadores"
Você pode criar um grupo que contenha todos os utilizadores dentro de um inquilino usando uma regra de adesão. Quando os utilizadores são adicionados ou removidos do arrendatário no futuro, a adesão do grupo é ajustada automaticamente.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é atribuída à função de administrador global ou administrador de utilizador no arrendatário.
1. Selecione **Azure Active Directory**.
2. Em **Gestão**, selecione **Grupos**e, em seguida, selecione **Novo grupo**.
1. Na página New **Group,** sob **o tipo grupo,** selecione **Security**. Insira o **nome do grupo** e a **descrição do grupo** para o novo grupo. 
2. Sob **o tipo de membro**, selecione Dynamic **User**e, em seguida, selecione Adicionar **consulta dinâmica**. 
4. Acima da caixa de texto de **sintaxe regra,** selecione **Editar**. Na página **de sintaxe de regra de edição,** digite a seguinte expressão na caixa de texto:

   ```
   user.objectId -ne null
   ```
1. Selecione **OK**. A regra aparece na caixa de sintaxe regra:

   ![Sintaxe de regra para todos os utilizadores grupo dinâmico](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selecione **Guardar**. O novo grupo dinâmico passará a incluir utilizadores convidados B2B, bem como utilizadores membros.


1. Selecione **Criar** na página **de grupo Nova** para criar o grupo.

## <a name="creating-a-group-of-members-only"></a>Criar um grupo de membros apenas

Se pretender que o seu grupo exclua os utilizadores convidados e inclua apenas membros do seu inquilino, crie um grupo dinâmico como descrito acima, mas na caixa **de sintaxe regra,** insira a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

A imagem a seguir mostra a sintaxe de regra para um grupo dinâmico modificado para incluir apenas membros e excluir os hóspedes.

![Mostra regra onde o tipo de utilizador é igual a membro](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Criar um grupo de convidados apenas

Também pode achar útil criar um novo grupo dinâmico que contenha apenas utilizadores convidados, para que possa aplicar políticas (como políticas de acesso condicionado AD AD) a eles. Crie um grupo dinâmico como descrito acima, mas na caixa **de sintaxe regra,** introduza a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

A imagem a seguir mostra a sintaxe de regra para um grupo dinâmico modificado para incluir apenas os hóspedes e excluir os utilizadores dos membros.

![Mostra regra onde o tipo de utilizador é igual ao hóspede](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Passos seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)
- [Adicionar um utilizador de colaboração B2B a uma função](add-guest-to-role.md)
- [Acesso Condicional para utilizadores de colaboração B2B](conditional-access.md)

