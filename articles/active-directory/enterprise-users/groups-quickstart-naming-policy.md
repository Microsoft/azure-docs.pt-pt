---
title: Início rápido da política de nomeação de grupos - Azure Ative Directory / Microsoft Docs
description: Explica como adicionar novos utilizadores ou eliminar os utilizadores existentes no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a594d42485510a525ce4e3c7f03b432554100f8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650602"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início rápido: Política de nomenclatura para grupos no Azure Active Directory

Neste quickstart, irá configurar a política de nomeação na sua organização Azure Ative Directory (Azure AD) para grupos Microsoft 365 criados pelo utilizador, para ajudá-lo a classificar e pesquisar os grupos da sua organização. Por exemplo, pode utilizar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajudar a categorizar grupos no livro de endereços.
* Bloquear a utilização de palavras específicas em nomes de grupos e aliases.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Configurar a política de nomeação do grupo no portal Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Utilizador.
1. Selecione **Grupos,** em seguida, **selecione a política de nomeação** para abrir a página de política de nomeação.

    ![abrir a página de política de nomeação no centro de administração](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomeação prefixo-sufixo

1. Na página **de política de nomeação,** selecione **a política de nomeação do Grupo**.
1. Pode visualizar ou editar as políticas de nomeação de prefixo ou sufixo atuais individualmente selecionando os atributos ou cordas que pretende aplicar como parte da política de nomeação.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou sufixo e, em seguida, selecione **Delete**. Vários itens podem ser eliminados ao mesmo tempo.
1. **Selecione Guardar** para as suas alterações à política entrarem em vigor.

### <a name="view-or-edit-the-custom-blocked-words"></a>Ver ou editar as palavras bloqueadas personalizadas

1. Na página de **política de nomeação,** selecione **palavras bloqueadas**.

    ![editar e carregar lista de palavras bloqueadas para a política de nomeação](./media/groups-quickstart-naming-policy/blockedwords.png)

1. Ver ou editar a lista atual de palavras bloqueadas personalizadas selecionando **Download**.
1. Faça o upload da nova lista de palavras bloqueadas personalizadas selecionando o ícone do ficheiro.
1. **Selecione Guardar** para as suas alterações à política entrarem em vigor.

Já está. Definiu a política de nomenclatura e adicionou palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remova a política de nomeação usando o portal Azure

1. Na página de **política de naming,** selecione **Eliminar a política**.
1. Depois de confirmar a supressão, a política de nomeação é removida, incluindo toda a política de nomeação de prefixos e quaisquer palavras bloqueadas personalizadas.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a definir a política de nomeação para a sua organização AZure AD através do portal Azure.

Avance para o próximo artigo para mais informações, incluindo os cmdlets PowerShell para a política de nomeação, restrições técnicas, adicionando uma lista de palavras bloqueadas personalizadas e as experiências do utilizador final em aplicações da Microsoft 365.
> [!div class="nextstepaction"]
> [Política de nomeação PowerShell](groups-naming-policy.md)