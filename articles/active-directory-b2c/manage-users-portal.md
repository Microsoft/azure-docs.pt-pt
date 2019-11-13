---
title: Criar & Excluir Azure AD B2C contas de usuário do consumidor no portal do Azure
description: Saiba como usar o portal do Azure para criar e excluir usuários do consumidor em seu diretório Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 259ce2b1881c31c2558539fed34513575d193a48
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961991"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Use o portal do Azure para criar e excluir usuários do consumidor no Azure AD B2C

Pode haver cenários nos quais você deseja criar manualmente contas de consumidor em seu diretório Azure Active Directory B2C (Azure AD B2C). Embora as contas de consumidor em um diretório de Azure AD B2C sejam criadas com mais frequência quando os usuários se inscrevem para usar um de seus aplicativos, você pode criá-las programaticamente e usando o portal do Azure. Este artigo se concentra no método portal do Azure de criação e exclusão de usuários.

Para adicionar ou excluir usuários, sua conta deve ser atribuída à função *administrador de usuário* ou *administrador global* .

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipos de contas de usuário

Conforme descrito em [visão geral das contas de usuário no Azure ad B2C](user-overview.md), há três tipos de contas de usuário que podem ser criadas em um diretório Azure ad B2C:

* Funciona
* Convidado
* Consumidor

Este artigo se concentra em trabalhar com **contas de consumidor** na portal do Azure. Para obter informações sobre como criar e excluir contas de trabalho e convidado, consulte [Adicionar ou excluir usuários usando Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Criar um usuário do consumidor

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione **novo usuário**.
1. Selecione **criar Azure ad B2C usuário**.
1. Escolha um **método de entrada** e insira um endereço de **email** ou um **nome** de usuário para o novo. O método de entrada selecionado aqui deve corresponder à configuração que você especificou para o provedor de identidade da *conta local* do Azure ad B2C locatário (consulte **gerenciar** > **provedores de identidade** no seu locatário Azure ad B2C).
1. Insira um **nome** para o usuário. Normalmente, esse é o nome completo (fornecido e sobrenome) do usuário.
1. Adicional Você pode **bloquear a entrada** se desejar atrasar a capacidade para o usuário entrar. Você pode habilitar a entrada mais tarde editando o **perfil** do usuário no portal do Azure.
1. Escolha **gerar senha automaticamente** ou **deixe-me criar senha**.
1. Especifique o **nome** e o **sobrenome**do usuário.
1. Selecione **Criar**.

A menos que você tenha selecionado **bloquear entrada**, o usuário pode agora entrar usando o método de entrada (email ou nome de usuário) que você especificou.

## <a name="delete-a-consumer-user"></a>Excluir um usuário consumidor

1. No diretório Azure AD B2C, selecione **usuários**e, em seguida, selecione o usuário que deseja excluir.
1. Selecione **excluir**e **Sim** para confirmar a exclusão.

Para obter detalhes sobre como restaurar um usuário nos primeiros 30 dias após a exclusão, ou para excluir permanentemente um usuário, consulte [restaurar ou remover um usuário excluído recentemente usando Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passos seguintes

Para cenários de gerenciamento de usuários automatizados, por exemplo, migrar usuários de outro provedor de identidade para seu diretório Azure AD B2C, consulte [Azure ad B2C: migração de usuário](active-directory-b2c-user-migration.md).
