---
title: Alterar planos de licença para utilizadores e grupos - Azure AD / Microsoft Docs
description: Como migrar utilizadores dentro de um grupo para diferentes planos de serviço utilizando licenciamento de grupo em Diretório Ativo Azure
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025893"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Alterar atribuições de licença para um utilizador ou grupo no Diretório Ativo Azure

Este artigo descreve como mover utilizadores e grupos entre planos de licença de serviço em Azure Ative Directory (Azure AD). O objetivo da AD Azure é garantir que não há perda de serviço ou dados durante a mudança de licença. Os utilizadores devem alternar entre serviços sem problemas. As etapas de atribuição do plano de licença neste artigo descrevem a alteração de um utilizador ou grupo no Office 365 E1 para o Office 365 E3, mas as etapas aplicam-se a todos os planos de licença. Quando atualiza as atribuições de licença para um utilizador ou grupo, as mudanças de atribuição de licenças e novas atribuições são feitas simultaneamente para que os utilizadores não percam acesso aos seus serviços durante as alterações de licença ou vejam conflitos de licenças entre planos.

## <a name="before-you-begin"></a>Antes de começar

Antes de atualizar as atribuições de licença, é importante verificar se certos pressupostos são verdadeiros para que todos os utilizadores ou grupos sejam atualizados. Se os pressupostos não forem verdadeiros para todos os utilizadores de um grupo, a migração pode falhar para alguns. Como resultado, alguns dos utilizadores podem perder acesso a serviços ou dados. Certifique-se de que:

- Os utilizadores têm o plano de licença atual (neste caso, Office 365 E1) que é atribuído a um grupo e herdado pelo utilizador e não atribuído diretamente.

- Tem licenças disponíveis suficientes para o plano de licença que está a atribuir. Se não tiver licenças suficientes, alguns utilizadores podem não ter o novo plano de licença. Pode verificar o número de licenças disponíveis.

- Os utilizadores não têm outras licenças de serviço atribuídas que possam entrar em conflito com a licença pretendida ou impedir a remoção da licença atual. Por exemplo, uma licença de um serviço como Workplace Analytics ou Project Online que tem uma dependência de outros serviços.

- Se gere grupos no local e os sincroniza no Azure AD Connect via Azure AD Connect, então adicione ou remova os utilizadores utilizando o seu sistema no local. Pode levar algum tempo para que as alterações de sincronização com a Azure AD sejam captadas por licenciamento de grupo.

- Se estiver a utilizar membros dinâmicos do grupo Azure AD, adicione ou remova os utilizadores alterando os seus atributos, mas o processo de atualização para atribuições de licenças permanece o mesmo.

## <a name="change-user-license-assignments"></a>Alterar as atribuições de licença de utilizador

Na página de atribuição de **licenças Update,** se vir que algumas caixas de verificação estão indisponíveis, indica serviços que não podem ser alterados porque são herdados de uma licença de grupo.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.
1. Selecione > **Utilizadores**de **Diretório Ativo Azure**e, em seguida, abra a página **Perfil** para um utilizador.
1. Selecione **Licenças**.
1. Selecione **Atribuições** para editar atribuição de licença para o utilizador ou grupo. A página **de Atribuições** é onde pode resolver conflitos de atribuição de licenças.
1. Selecione a caixa de verificação do Office 366 E3 e certifique-se de que, no mínimo, todos os serviços E1 atribuídos ao utilizador são selecionados.
1. Limpe a caixa de verificação do Escritório 365 E1.

    ![página de atribuições de licença para um utilizador que mostre o Office 365 E1 ilibado e o Office 365 E3 selecionado](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecione **Guardar**.

A Azure AD aplica as novas licenças e remove as antigas licenças simultaneamente para fornecer continuidade de serviço.

## <a name="change-group-license-assignments"></a>Alterar atribuições de licençade grupo

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.
1. Selecione **Azure Ative Directory** > **Groups**e, em seguida, abra a página de **visão geral** para um grupo.
1. Selecione **Licenças**.
1. Selecione o comando **de Atribuição para** editar a atribuição de licença para o utilizador ou grupo.
1. Selecione a caixa de verificação para o Office 366 E3. Para manter a continuidade do serviço, certifique-se de que seleciona todos os serviços E1 que já estão atribuídos ao utilizador.
1. Limpe a caixa de verificação do Escritório 365 E1.

    ![Selecione o comando de Atribuição seletiva numa página de Licenças de utilizador ou grupo](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecione **Guardar**.

Para fornecer continuidade de serviço, a Azure AD aplica as novas licenças e remove as antigas licenças simultaneamente para todos os utilizadores do grupo.

## <a name="next-steps"></a>Passos seguintes

Conheça outros cenários de gestão de licenças através de grupos nos seguintes artigos:

- [Atribuir licenças a um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Como migrar utilizadores licenciados individuais para licenciamento em grupo no Diretório Ativo Azure](../users-groups-roles/licensing-groups-migrate-users.md)
- [Grupo Azure Ative Diretório licenciando cenários adicionais](../users-groups-roles/licensing-group-advanced.md)
- [Exemplos da PowerShell para licenciamento em grupo em Diretório Ativo Azure](../users-groups-roles/licensing-ps-examples.md)
