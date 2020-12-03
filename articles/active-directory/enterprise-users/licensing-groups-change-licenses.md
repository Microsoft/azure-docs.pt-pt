---
title: Alterar planos de licença para utilizadores e grupos - Azure AD ! Microsoft Docs
description: Como migrar os utilizadores dentro de um grupo para diferentes planos de serviço usando o licenciamento do grupo no Azure Ative Directory
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546544"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Alterar atribuições de licença para um utilizador ou grupo no Azure Ative Directory

Este artigo descreve como mover utilizadores e grupos entre planos de licença de serviço no Azure Ative Directory (Azure AD). O objetivo Azure AD é garantir que não há perda de serviço ou dados durante a mudança de licença. Os utilizadores devem alternar entre os serviços sem problemas. As etapas de atribuição do plano de licença neste artigo descrevem a alteração de um utilizador ou grupo no Office 365 E1 para o Office 365 E3, mas os passos aplicam-se a todos os planos de licença. Quando atualiza as atribuições de licenças para um utilizador ou grupo, as remoção da atribuição de licenças e novas atribuições são feitas simultaneamente para que os utilizadores não percam o acesso aos seus serviços durante as alterações de licença ou vejam conflitos de licença entre planos.

## <a name="before-you-begin"></a>Before you begin

Antes de atualizar as atribuições de licença, é importante verificar que certos pressupostos são verdadeiros para todos os utilizadores ou grupos serem atualizados. Se os pressupostos não forem verdadeiros para todos os utilizadores de um grupo, a migração pode falhar para alguns. Como resultado, alguns dos utilizadores podem perder acesso a serviços ou dados. Certifique-se de que:

- Os utilizadores têm o atual plano de licença (neste caso, Office 365 E1) que é atribuído a um grupo e herdado pelo utilizador e não atribuído diretamente.

- Tem licenças suficientes para o plano de licença que está a atribuir. Se não tiver licenças suficientes, alguns utilizadores podem não ter o novo plano de licença. Pode verificar o número de licenças disponíveis.

- Os utilizadores não têm outras licenças de serviço atribuídas que possam entrar em conflito com a licença pretendida ou impedir a remoção da licença atual. Por exemplo, uma licença de um serviço como Workplace Analytics ou Project Online que tem uma dependência de outros serviços.

- Se gere grupos no local e os sincroniza no Azure AD via Azure AD Connect, então adiciona ou remove os utilizadores utilizando o seu sistema no local. Pode levar algum tempo para que as alterações para sincronizar com a Azure AD sejam recolhidas por licenciamento de grupo.

- Se estiver a utilizar membros do grupo AD AD dinâmicos Azure, adiciona ou remove os utilizadores alterando os seus atributos, mas o processo de atualização para atribuições de licenças permanece o mesmo.

## <a name="change-user-license-assignments"></a>Alterar atribuições de licença de utilizador

Na página de **atribuição de licenças de atualização,** se vir que algumas caixas de verificação estão indisponíveis, indica serviços que não podem ser alterados porque são herdados de uma licença de grupo.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.
1. Selecione **Utilizadores de Diretório Ativo Azure**  >  **Users** e, em seguida, abra a página **de Perfil** para um utilizador.
1. Selecione **Licenças**.
1. Selecione **Atribuições** para editar a atribuição de licença para o utilizador ou grupo. A página **atribuições** é onde pode resolver conflitos de atribuição de licenças.
1. Selecione a caixa de verificação para o Office 365 E3 e certifique-se de que, no mínimo, todos os serviços E1 atribuídos ao utilizador são selecionados.
1. Limpe a caixa de verificação do Office 365 E1.

    ![página de atribuição de licenças para um utilizador que mostra o Office 365 E1 limpo e o Office 365 E3 selecionado](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecione **Guardar**.

A Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para fornecer continuidade de serviço.

## <a name="change-group-license-assignments"></a>Alterar atribuição de licenças de grupo

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.
1. Selecione **Azure Ative Directory**  >  **Groups** e, em seguida, abra a página **'Visão Geral'** para um grupo.
1. Selecione **Licenças**.
1. Selecione o comando **atribuições** para editar a atribuição de licença para o utilizador ou grupo.
1. Selecione a caixa de verificação para o Office 365 E3. Para manter a continuidade do serviço, certifique-se de que seleciona todos os serviços E1 que já estão atribuídos ao utilizador.
1. Limpe a caixa de verificação do Office 365 E1.

    ![Selecione o comando atribuições numa página de licenças de utilizador ou grupo](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecione **Guardar**.

Para fornecer continuidade de serviço, a Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para todos os utilizadores do grupo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros cenários para a gestão de licenças através de grupos nos seguintes artigos:

- [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar utilizadores licenciados individuais para agrupar licenciamento no Azure Ative Directory](licensing-groups-migrate-users.md)
- [Azure Ative Directory group licenciando cenários adicionais](licensing-group-advanced.md)
- [Exemplos powerShell para licenciamento de grupo em Azure Ative Directory](licensing-ps-examples.md)
