---
title: Criar e eliminar contas de utilizadores de consumo Azure AD B2C no portal Azure
description: Aprenda a utilizar o portal Azure para criar e eliminar utilizadores de consumidores no seu diretório Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9d96db5cf062da1b2e8badd80a9a00620b724fd4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840422"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Utilize o portal Azure para criar e eliminar utilizadores de consumidores em Azure AD B2C

Pode haver cenários em que pretende criar manualmente contas de consumo no seu diretório Azure Ative Directory B2C (Azure AD B2C). Embora as contas de consumo num diretório Azure AD B2C sejam mais comumente criadas quando os utilizadores se inscrevem para usar uma das suas aplicações, pode criá-las programáticamente e utilizando o portal Azure. Este artigo centra-se no método do portal Azure de criação e eliminação do utilizador.

Para adicionar ou excluir utilizadores, a sua conta deve ser atribuída ao administrador do *Utilizador* ou à função de *administrador global.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipos de contas de usuário

Tal como descrito na [visão geral das contas dos utilizadores no Azure AD B2C,](user-overview.md)existem três tipos de contas de utilizador que podem ser criadas num diretório Azure AD B2C:

* Trabalho
* Convidado
* Consumidor

Este artigo centra-se em trabalhar com **as contas** de consumo no portal Azure. Para obter informações sobre a criação e eliminação de contas de trabalho e de hóspedes, consulte [Adicionar ou excluir utilizadores utilizando o Diretório Ativo do Azure](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Criar um utilizador de consumo

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione **Novo utilizador**.
1. Selecione **Criar o utilizador AD B2C Azure**.
1. Escolha um **método de 'Sign in'** e introduza um endereço **de e-mail** ou um nome de **utilizador** para o novo utilizador. O método de inscrição que seleciona aqui deve corresponder à definição que especificou para o fornecedor de identidade *de conta local* do seu inquilino Azure AD B2C (consulte **Gerir** > **Fornecedores** de Identidade no seu inquilino Azure AD B2C).
1. Introduza um **Nome** para o utilizador. Este é tipicamente o nome completo (dado e sobrenome) do utilizador.
1. (Opcional) Pode bloquear o **'sign'in** se pretender atrasar a capacidade de o utilizador iniciar sessão. Pode ativar o sessão mais tarde editando o **Perfil** do utilizador no portal Azure.
1. Escolha **a palavra-passe gerada automaticamente** ou **deixe-me criar uma senha**.
1. Especifique o **primeiro nome** e **o apelido**do utilizador .
1. Selecione **Criar**.

A menos que tenha selecionado o **'Block' ( 'Block' (** o utilizador) pode agora iniciar o seu sessão utilizando o método de inscrição (e-mail ou nome de utilizador) que especificou.

## <a name="delete-a-consumer-user"></a>Eliminar um utilizador de consumo

1. No seu diretório Azure AD B2C, selecione **Utilizadores**e, em seguida, selecione o utilizador que pretende eliminar.
1. Selecione **Eliminar**e, **em seguida, Sim** para confirmar a eliminação.

Para mais detalhes sobre a restauração de um utilizador nos primeiros 30 dias após a eliminação, ou para apagar permanentemente um utilizador, consulte Restaurar ou remover um utilizador recentemente eliminado utilizando o [Diretório Ativo Azure](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passos seguintes

Para cenários automatizados de gestão de utilizadores, por exemplo, utilizadores migradores de outro fornecedor de identidade para o seu diretório Azure AD B2C, consulte [Azure AD B2C: Migração](user-migration.md)de utilizadores .
