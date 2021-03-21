---
title: Criar & eliminar contas de consumidores Azure AD B2C no portal Azure
description: Saiba como utilizar o portal Azure para criar e eliminar os utilizadores de consumidores no seu diretório Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033681"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Utilize o portal Azure para criar e eliminar utilizadores de consumidores em Azure AD B2C

Pode haver cenários em que pretende criar manualmente contas de consumidores no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Embora as contas de consumidores num diretório AD B2C Azure sejam mais frequentemente criadas quando os utilizadores se inscrevem para usar uma das suas aplicações, pode criá-las programáticamente e utilizando o portal Azure. Este artigo centra-se no método do portal Azure de criação e eliminação de utilizadores.

Para adicionar ou eliminar utilizadores, a sua conta deve ser atribuída ao *administrador do Utilizador* ou ao papel de administrador *global.*

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
1. Escolha um **método de Sinal e** insira um endereço de **e-mail** ou um **nome de utilizador** para o novo utilizador. O sinal no método que seleciona aqui deve corresponder à definição especificada para o fornecedor *local* de identidade do seu inquilino Azure AD B2C (consulte fornecedores de Identidade **de Gestão**  >   no seu inquilino Azure AD B2C).
1. Introduza um **Nome** para o utilizador. Este é tipicamente o nome completo (dado e apelido) do utilizador.
1. (Opcional) Pode **bloquear o súmis** se desejar atrasar a capacidade de o utilizador iniciar súm. Pode iniciar sessão mais tarde editando o **Perfil** do utilizador no portal Azure.
1. Escolha **a palavra-passe de autogerado ou** **Deixe-me criar senha.**
1. Especifique o **primeiro nome** e **o apelido** do utilizador.
1. Selecione **Criar**.

A menos que tenha selecionado o **bloco de iniciar sedução,** o utilizador pode agora iniciar súmed usando o sinal no método (e-mail ou nome de utilizador) que especificou.

## <a name="reset-a-users-password"></a>Repor a palavra-passe de um utilizador

Como administrador, pode redefinir a palavra-passe de um utilizador, se o utilizador esquecer a sua palavra-passe. Quando repõe a palavra-passe do utilizador, uma palavra-passe temporária é autogerada para o utilizador. A senha temporária nunca expira. Da próxima vez que o utilizador entrar, a palavra-passe continuará a funcionar, independentemente do tempo que passou desde que a senha temporária foi gerada. Em seguida, o utilizador deve redefinir a palavra-passe para uma permanente. 

> [!IMPORTANT]
> Antes de redefinir a palavra-passe de um utilizador, [confiem um fluxo de reset de password de força no Azure Ative Directory B2C,](force-password-reset.md)caso contrário o utilizador não poderá iniciar sação.

Para redefinir a palavra-passe de um utilizador:

1. No seu diretório Azure AD B2C, selecione **Utilizadores** e, em seguida, selecione o utilizador que pretende redefinir a palavra-passe.
1. Procure e selecione o utilizador que necessita do reset e, em seguida, **selecione Reset Password**.

    A página **Alain Charon - Perfil** aparece com a opção **de palavra-passe Reset.**

    ![Página de perfil do utilizador, com a opção de palavra-passe reset realçada](media/manage-users-portal/user-profile-reset-password-link.png)

1. Na página de **palavra-passe 'Redefinir' 'Redefinir',** **selecione Redefinir a palavra-passe**.
1. Copie a palavra-passe e entregue-a ao utilizador. O utilizador será obrigado a alterar a palavra-passe durante o próximo processo de inscrição.


## <a name="delete-a-consumer-user"></a>Eliminar um utilizador de consumo

1. No seu diretório Azure AD B2C, selecione **Utilizadores** e, em seguida, selecione o utilizador que pretende eliminar.
1. **Selecione Eliminar**, e depois **Sim** para confirmar a eliminação.

Para obter mais informações sobre a restauração de um utilizador nos primeiros 30 dias após a eliminação, ou para eliminar permanentemente um utilizador, consulte Restaurar ou remover um utilizador recentemente eliminado utilizando o [Azure Ative Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passos seguintes

Para cenários automatizados de gestão de utilizadores, por exemplo, migrar utilizadores de outro fornecedor de identidade para o seu diretório Azure AD B2C, consulte [Azure AD B2C: Migração de utilizadores](user-migration.md).
