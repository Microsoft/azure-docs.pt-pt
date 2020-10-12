---
title: Criar & eliminar contas de consumidores Azure AD B2C no portal Azure
description: Saiba como utilizar o portal Azure para criar e eliminar os utilizadores de consumidores no seu diretório Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d6d6169f8662c9b973fb7f624a590322f62b0b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387529"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Utilize o portal Azure para criar e eliminar utilizadores de consumidores em Azure AD B2C

Pode haver cenários em que pretende criar manualmente contas de consumidores no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Embora as contas de consumidores num diretório AD B2C Azure sejam mais frequentemente criadas quando os utilizadores se inscrevem para usar uma das suas aplicações, pode criá-las programáticamente e utilizando o portal Azure. Este artigo centra-se no método do portal Azure de criação e eliminação de utilizadores.

Para adicionar ou eliminar utilizadores, a sua conta deve ser atribuída ao *administrador do Utilizador* ou ao papel de administrador *global.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipos de contas de utilizador

Como descrito no [Resumo das contas de utilizador em Azure AD B2C,](user-overview.md)existem três tipos de contas de utilizador que podem ser criadas num diretório Azure AD B2C:

* Trabalho
* Convidado
* Consumidor

Este artigo centra-se no trabalho com **as contas dos consumidores** no portal Azure. Para obter informações sobre a criação e eliminação de contas work and Guest, consulte [Adicionar ou eliminar utilizadores usando o Azure Ative Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Criar um consumidor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione **Novo utilizador**.
1. **Selecione Criar utilizador Azure AD B2C**.
1. Escolha um **método de Sinal e** insira um endereço de **e-mail** ou um **nome de utilizador** para o novo utilizador. O sinal no método que seleciona aqui deve corresponder à definição especificada para o fornecedor *local* de identidade do seu inquilino Azure AD B2C (consulte fornecedores de Identidade **de Gestão**  >  **Identity providers** no seu inquilino Azure AD B2C).
1. Introduza um **Nome** para o utilizador. Este é tipicamente o nome completo (dado e apelido) do utilizador.
1. (Opcional) Pode **bloquear o súmis** se desejar atrasar a capacidade de o utilizador iniciar súm. Pode iniciar sessão mais tarde editando o **Perfil** do utilizador no portal Azure.
1. Escolha **a palavra-passe de geração automática** ou **deixe-me criar senha.**
1. Especifique o **primeiro nome** e **o apelido**do utilizador.
1. Selecione **Criar**.

A menos que tenha selecionado o **bloco de iniciar sedução,** o utilizador pode agora iniciar súmed usando o sinal no método (e-mail ou nome de utilizador) que especificou.

## <a name="delete-a-consumer-user"></a>Eliminar um utilizador de consumo

1. No seu diretório Azure AD B2C, selecione **Utilizadores**e, em seguida, selecione o utilizador que pretende eliminar.
1. **Selecione Eliminar**, e depois **Sim** para confirmar a eliminação.

Para obter mais informações sobre a restauração de um utilizador nos primeiros 30 dias após a eliminação, ou para eliminar permanentemente um utilizador, consulte Restaurar ou remover um utilizador recentemente eliminado utilizando o [Azure Ative Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passos seguintes

Para cenários automatizados de gestão de utilizadores, por exemplo, migrar utilizadores de outro fornecedor de identidade para o seu diretório Azure AD B2C, consulte [Azure AD B2C: Migração de utilizadores](user-migration.md).
