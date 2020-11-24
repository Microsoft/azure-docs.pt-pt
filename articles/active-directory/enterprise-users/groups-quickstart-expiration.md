---
title: Quickstart política de expiração do grupo - Azure AD / Microsoft Docs
description: Expiração para grupos Microsoft 365 - Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0b9e4ccd595e90ab941cb061a016f8c4d4f3d0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503029"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Quickstart: Desconfiem dos grupos Microsoft 365 para expirar no Azure Ative Directory

Neste arranque rápido, define a política de expiração para os seus grupos Microsoft 365. Quando os utilizadores podem configurar os seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma forma de gerir os grupos não utilizados é defini-los para expirarem, de forma a reduzir a manutenção de eliminar manualmente os grupos.

A política de expiração é simples:

- Os grupos com atividades de utilizador são automaticamente renovados à medida que a expiração se aproxima
- Os proprietários do grupo são notificados para renovar um grupo prestes a expirar
- Um grupo que não seja renovado é eliminado
- Um grupo Microsoft 365 eliminado pode ser restaurado dentro de 30 dias por um proprietário do grupo ou por um administrador AD AZure

> [!NOTE]
> Os grupos agora usam a inteligência AD AD do Azure para renovar automaticamente com base no facto de terem sido usados recentemente. Esta decisão de renovação baseia-se na atividade do utilizador em grupos em todos os serviços da Microsoft 365, como Outlook, SharePoint, Teams, Yammer, entre outros.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

 A função menos privilegiada necessária para a criação da expiração do grupo é administrador do utilizador na organização.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de utilizadores para grupos

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de administrador do Utilizador.

2. Selecione **Grupos** e, em seguida, selecione **Geral**.
  
   ![Página de definições de grupo de self-service](./media/groups-quickstart-expiration/self-service-settings.png)

3. Definir  **Os utilizadores podem criar grupos Microsoft 365** para **Sim**.

4. Selecione **Guardar** para guardar as definições dos grupos quando terminar.

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Inscreva-se no [portal Azure](https://portal.azure.com), selecione **Azure Ative Directory**  >  **Groups**  >  **Expiration** para abrir as definições de expiração.
  
   ![Página de definições de expiração para grupo](./media/groups-quickstart-expiration/expiration-settings.png)

2. Defina o intervalo de expiração. Selecione um valor predefinido ou introduza um valor personalizado num período superior a 31 dias. 

3. Forneça um endereço de e-mail para onde as notificações de expiração devem ser enviadas quando um grupo não tiver proprietário.

4. Para este arranque rápido, **desemote-se ativar a expiração destes grupos Microsoft 365** para **All**.

5. Selecione **Guardar** para guardar as definições de expiração quando terminar.

Já está! Neste arranque rápido, definiu com sucesso a política de expiração para os grupos microsoft 365 selecionados.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="to-remove-the-expiration-policy"></a>Para remover a política de expiração

1. Certifique-se de que está inscrito no [portal Azure](https://portal.azure.com) com uma conta que é o Administrador Global da sua organização AZure AD.
2. Selecione **Azure Ative Directory**  >  **Groups**  >  **Expiration**.
3. Definir **Ativar a expiração destes grupos Microsoft 365** a **Nenhum**.

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação de utilizadores para grupos

1. Selecione **Azure Ative Directory**  >  **Groups**  >  **General**. 
2. Definir **Os utilizadores podem criar grupos Microsoft 365 em portais Azure** para **No**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a expiração, incluindo instruções powerShell e restrições técnicas, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Política de expiração PowerShell](groups-lifecycle.md)
