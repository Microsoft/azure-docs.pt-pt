---
title: Início rápido da política de nomenclatura de grupo-Azure Active Directory | Microsoft Docs
description: Explica como adicionar novos utilizadores ou eliminar os utilizadores existentes no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026942"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início rápido: Política de nomenclatura para grupos no Azure Active Directory

Neste início rápido, irá configurar a política de nomenclatura no inquilino do Azure Active Directory (Azure AD) para grupos do Office 365 criados pelo utilizador, para o ajudar a ordenar e procurar os grupos do seu inquilino. Por exemplo, pode utilizar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajudar a categorizar grupos no livro de endereços.
* Bloquear a utilização de palavras específicas em nomes de grupos e aliases.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Configurar a política de nomenclatura de grupo para um locatário usando portal do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta de administrador de usuário.
1. Selecione **grupos**e, em seguida, selecione **política de nomenclatura** para abrir a página de política de nomenclatura.

    ![abrir a página de política de nomenclatura no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Exibir ou editar a política de nomenclatura de sufixo de prefixo

1. Na página **política de nomenclatura** , selecione **política de nomenclatura de grupo**.
1. Você pode exibir ou editar o prefixo atual ou as políticas de nomenclatura de sufixo individualmente selecionando os atributos ou as cadeias de caracteres que deseja impor como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou sufixo e, em seguida, selecione **excluir**. Vários itens podem ser excluídos ao mesmo tempo.
1. Selecione **salvar** para que as alterações na política entrem em vigor.

### <a name="view-or-edit-the-custom-blocked-words"></a>Exibir ou editar as palavras bloqueadas personalizadas

1. Na página **política de nomenclatura** , selecione **palavras bloqueadas**.

    ![editar e carregar a lista de palavras bloqueadas para a política de nomenclatura](./media/groups-naming-policy/blockedwords.png)

1. Exiba ou edite a lista atual de palavras bloqueadas personalizadas selecionando **baixar**.
1. Carregue a nova lista de palavras bloqueadas personalizadas selecionando o ícone arquivo.
1. Selecione **salvar** para que as alterações na política entrem em vigor.

Já está. Definiu a política de nomenclatura e adicionou palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remover a política de nomenclatura usando portal do Azure

1. Na página **política de nomenclatura** , selecione **excluir política**.
1. Depois de confirmar a exclusão, a política de nomenclatura é removida, incluindo toda a política de nomenclatura de sufixo de prefixo e quaisquer palavras bloqueadas personalizadas.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a definir a política de nomenclatura para sua organização do Azure AD por meio do portal do Azure.

Avance para o próximo artigo para obter mais informações, incluindo os cmdlets do PowerShell para a política de nomenclatura, restrições técnicas, adição de uma lista de palavras bloqueadas personalizadas e as experiências do usuário final em todos os aplicativos do Office 365.
> [!div class="nextstepaction"]
> [Política de nomenclatura do PowerShell](groups-naming-policy.md)