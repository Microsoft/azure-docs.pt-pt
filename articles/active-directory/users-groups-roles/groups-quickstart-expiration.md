---
title: Política de expiração do grupo quickstart - Azure AD [ Microsoft Docs
description: Expiração para grupos do Office 365 - Azure Active Directory
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
ms.openlocfilehash: 16b6e4f521568c89e415e6e9fa621175052cf34d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026906"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Início rápido: definir a expiração de grupos do Office 365 no Azure Active Directory

Neste início rápido, vai definir a política de expiração dos seus grupos do Office 365. Quando os utilizadores podem configurar os seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma forma de gerir os grupos não utilizados é defini-los para expirarem, de forma a reduzir a manutenção de eliminar manualmente os grupos.

A política de expiração é simples:

- Os grupos com atividades do utilizador são automaticamente renovados à medida que a expiração se aproxima
- Os proprietários do grupo são notificados para renovar um grupo prestes a expirar
- Um grupo que não seja renovado é eliminado
- Um grupo do Office 365 eliminado pode ser restaurado dentro de 30 dias por um proprietário do grupo ou por um administrador do Azure AD

> [!NOTE]
> Os grupos usam agora a inteligência da AD Azure para renovar automaticamente com base no facto de terem sido usados recentemente. Esta decisão de renovação baseia-se na atividade dos utilizadores em grupos em todo o Office 365 serviços como Outlook, SharePoint, Teams, Yammer, entre outros.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

 A função menos privilegiada necessária para a criação de expiração do grupo é o administrador do Utilizador na organização.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de utilizadores para grupos

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de administrador do Utilizador.

2. Selecione **Grupos** e, em seguida, selecione **Geral**.
  
   ![Página de definições de grupo self-service](./media/groups-quickstart-expiration/self-service-settings.png)

3. Defina **Os utilizadores podem criar grupos do Office 365** como **Sim**.

4. Selecione **Guardar** para guardar as definições dos grupos quando terminar.

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Inscreva-se no [portal Azure](https://portal.azure.com), selecione **Azure Ative Directory** > **Groups** > **Expiração** para abrir as definições de validade.
  
   ![Página de definições de expiração para grupo](./media/groups-quickstart-expiration/expiration-settings.png)

2. Defina o intervalo de expiração. Selecione um valor predefinido ou introduza um valor personalizado num período superior a 31 dias. 

3. Forneça um endereço de e-mail para onde as notificações de expiração devem ser enviadas quando um grupo não tiver proprietário.

4. Neste início rápido, defina **Ativar a expiração para estes grupos do Office 365** como **Todos**.

5. Selecione **Guardar** para guardar as definições de expiração quando terminar.

Já está! Neste início rápido, definiu com êxito a política de expiração dos grupos do Office 365 selecionados.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-remove-the-expiration-policy"></a>Para remover a política de expiração

1. Certifique-se de que inicia sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja um Administrador Global do inquilino.
2. Selecione**Expiração**de > **Grupos** > de **DirectórioActivo Azure**.
3. Defina **Ativar a expiração para estes grupos do Office 365** como **Nenhum**.

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação de utilizadores para grupos

1. Selecione **Azure Ative Directory** > **Groups** > **General**. 
2. Defina **Os utilizadores podem criar grupos do Office 365 em portais do Azure** como **Não**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a expiração, incluindo instruções powerShell e restrições técnicas, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Política de expiração PowerShell](groups-lifecycle.md)
