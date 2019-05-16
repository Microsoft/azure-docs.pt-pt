---
title: Adicionar a nomenclatura de política de início rápido para grupos do Office 365 - Azure Active Directory | Documentos da Microsoft
description: Explica como adicionar novos utilizadores ou eliminar os utilizadores existentes no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e600204479bc54a590df6bf1bbcd634eaac7fc
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605633"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início rápido: Política de nomenclatura para os grupos no Azure Active Directory

Neste início rápido, irá configurar a política de nomenclatura no inquilino do Azure Active Directory (Azure AD) para grupos do Office 365 criados pelo utilizador, para o ajudar a ordenar e procurar os grupos do seu inquilino. Por exemplo, pode utilizar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajudar a categorizar grupos no livro de endereços.
* Bloquear a utilização de palavras específicas em nomes de grupos e aliases.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>Configurar o grupo de política de nomenclatura para um inquilino com o portal do Azure (pré-visualização)

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta de administrador.
1. Selecione **grupos**, em seguida, selecione **política de nomenclatura** para abrir a página de política de nomenclatura.

    ![Abra a página de política de nomenclatura no Centro de administração](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomenclatura de sufixo de prefixo

1. Sobre o **política de atribuição de nomes** página, selecione **nomenclatura de política de grupo**.
1. Pode ver ou editar o prefixo ou sufixo atual nomenclatura políticas individualmente ao selecionar os atributos ou cadeias de caracteres que deseja impor como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo na lista, selecione o prefixo ou sufixo, em seguida, selecione **eliminar**. Vários itens podem ser eliminados ao mesmo tempo.
1. Selecione **guardar** para as suas alterações à política para entrar em vigor.

### <a name="view-or-edit-the-custom-blocked-words"></a>Visualizar ou editar as palavras bloqueadas personalizadas

1. Sobre o **política de atribuição de nomes** página, selecione **bloqueado palavras**.

    ![editar e carregar a lista de palavras bloqueadas para atribuir nomes a política](./media/groups-naming-policy/blockedwords-preview.png)

1. Visualizar ou editar a lista atual de palavras bloqueadas personalizadas selecionando **transferir**.
1. Carregar a nova lista de palavras bloqueadas personalizadas ao selecionar o ícone de ficheiro.
1. Selecione **guardar** para as suas alterações à política para entrar em vigor.

Já está. Definiu a política de nomenclatura e adicionou palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Remover a política de nomes com o portal do Azure (pré-visualização)

1. Sobre o **política de atribuição de nomes** página, selecione **Eliminar política**.
1. Depois de confirmar a eliminação, a política de nomes for removida, incluindo todos os de sufixo de prefixo de nomenclatura de política e quaisquer palavras bloqueadas personalizadas.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como definir a política de nomes para a sua organização do Azure AD através do portal do Azure.

Avance para o artigo seguinte para obter mais informações, incluindo os cmdlets do PowerShell para atribuir nomes a política, restrições técnicas, adicionando uma lista de palavras bloqueadas personalizadas e as experiências de usuário final em aplicações do Office 365.
> [!div class="nextstepaction"]
> [Atribuição de nomes de política do PowerShell](groups-naming-policy.md)