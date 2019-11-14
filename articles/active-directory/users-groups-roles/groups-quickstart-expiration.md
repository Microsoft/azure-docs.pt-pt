---
title: Início rápido da política de expiração do grupo-Azure AD | Microsoft Docs
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026906"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Início rápido: definir a expiração de grupos do Office 365 no Azure Active Directory

Neste início rápido, vai definir a política de expiração dos seus grupos do Office 365. Quando os utilizadores podem configurar os seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma forma de gerir os grupos não utilizados é defini-los para expirarem, de forma a reduzir a manutenção de eliminar manualmente os grupos.

A política de expiração é simples:

- Grupos com atividades do usuário são renovados automaticamente à medida que a expiração é próxima
- Os proprietários do grupo são notificados para renovar um grupo prestes a expirar
- Um grupo que não seja renovado é eliminado
- Um grupo do Office 365 eliminado pode ser restaurado dentro de 30 dias por um proprietário do grupo ou por um administrador do Azure AD

> [!NOTE]
> Os grupos agora usam a inteligência do Azure AD para renovar automaticamente com base no uso recente. Essa decisão de renovação se baseia na atividade do usuário em grupos entre os serviços do Office 365, como Outlook, SharePoint, Teams, Yammer e outros.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

 A função com privilégios mínimos necessária para configurar a expiração do grupo é o administrador do usuário na organização.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de utilizadores para grupos

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário.

2. Selecione **Grupos** e, em seguida, selecione **Geral**.
  
   ![Página Configurações de grupo de autoatendimento](./media/groups-quickstart-expiration/self-service-settings.png)

3. Defina **Os utilizadores podem criar grupos do Office 365** como **Sim**.

4. Selecione **Guardar** para guardar as definições dos grupos quando terminar.

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** **grupos** de >  > **expiração** para abrir as configurações de expiração.
  
   ![Página de configurações de expiração do grupo](./media/groups-quickstart-expiration/expiration-settings.png)

2. Defina o intervalo de expiração. Selecione um valor predefinido ou introduza um valor personalizado num período superior a 31 dias. 

3. Forneça um endereço de e-mail para onde as notificações de expiração devem ser enviadas quando um grupo não tiver proprietário.

4. Neste início rápido, defina **Ativar a expiração para estes grupos do Office 365** como **Todos**.

5. Selecione **Guardar** para guardar as definições de expiração quando terminar.

Já está! Neste início rápido, definiu com êxito a política de expiração dos grupos do Office 365 selecionados.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-remove-the-expiration-policy"></a>Para remover a política de expiração

1. Certifique-se de que inicia sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja um Administrador Global do inquilino.
2. Selecione **Azure Active Directory** > **Grupos** > **Expiração**.
3. Defina **Ativar a expiração para estes grupos do Office 365** como **Nenhum**.

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação de grupos de usuários

1. Selecione **Azure Active Directory** > **Grupos** > **Geral**. 
2. Defina **Os utilizadores podem criar grupos do Office 365 em portais do Azure** como **Não**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre expiração, incluindo instruções do PowerShell e restrições técnicas, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [PowerShell de política de expiração](groups-lifecycle.md)
