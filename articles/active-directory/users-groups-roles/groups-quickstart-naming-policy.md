---
title: Política de nomeação de grupo quickstart - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
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
ms.openlocfilehash: 4253f5bd702abd061cf1cddd4badd68c9cd5d475
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582831"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início rápido: Política de nomenclatura para grupos no Azure Active Directory

Neste arranque rápido, você vai configurar a política de nomeação na sua organização Azure Ative Directory (Azure AD) para grupos criados pelo utilizador Office 365, para ajudá-lo a classificar e pesquisar os grupos da sua organização. Por exemplo, pode utilizar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajudar a categorizar grupos no livro de endereços.
* Bloquear a utilização de palavras específicas em nomes de grupos e aliases.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Configure a política de nomeação do grupo no portal Azure

1. Inscreva-se no [centro de administração da AD Azure](https://aad.portal.azure.com) com uma conta de administrador do Utilizador.
1. Selecione **Grupos**, em seguida, selecione **a política de nomeação** para abrir a página de política de Nomeação.

    ![abrir a página política de nomeação no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomeação prefixo-sufixo

1. Na página política de **Naming,** selecione Política de **nomeação do Grupo**.
1. Pode visualizar ou editar as políticas de nomeação de prefixo ou sufixo sufixo sufixo sufixo sufixo individualmente, selecionando os atributos ou cordas que pretende aplicar como parte da política de nomeação.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou o sufixo e, em seguida, **selecione Delete**. Vários itens podem ser eliminados ao mesmo tempo.
1. Selecione **Guardar** para que as suas alterações na política entrem em vigor.

### <a name="view-or-edit-the-custom-blocked-words"></a>Ver ou editar as palavras bloqueadas personalizadas

1. Na página política de **Naming,** selecione **palavras bloqueadas**.

    ![editar e carregar lista de palavras bloqueadas para a política de nomeação](./media/groups-naming-policy/blockedwords.png)

1. Ver ou editar a lista atual de palavras bloqueadas personalizadas selecionando **Download**.
1. Faça upload da nova lista de palavras bloqueadas personalizadas selecionando o ícone do ficheiro.
1. Selecione **Guardar** para que as suas alterações na política entrem em vigor.

Já está. Definiu a política de nomenclatura e adicionou palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remova a política de nomeação usando o portal Azure

1. Na página de **política de Nomeação,** selecione **a política Eliminar**.
1. Depois de confirmar a eliminação, a política de nomeação é removida, incluindo toda a política de nomeação de prefixo-sufixo e quaisquer palavras bloqueadas personalizadas.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a definir a política de nomeação para a sua organização Azure AD através do portal Azure.

Avance para o próximo artigo para mais informações, incluindo os cmdlets PowerShell para nomear a política, constrangimentos técnicos, adicionar uma lista de palavras bloqueadas personalizadas, e as experiências de utilizador final em todas as aplicações do Office 365.
> [!div class="nextstepaction"]
> [Política de nomeação PowerShell](groups-naming-policy.md)