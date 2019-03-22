---
title: Início rápido de política de expiração para grupos do Office 365 - Azure Active Directory | Documentos da Microsoft
description: Expiração para grupos do Office 365 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0573448c753c763e818d641216033dbeacb9e9a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199312"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Início rápido: Conjunto de grupos do Office 365 para expirar no Azure Active Directory

Neste início rápido, vai definir a política de expiração dos seus grupos do Office 365. Quando os utilizadores podem configurar os seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma forma de gerir os grupos não utilizados é defini-los para expirarem, de forma a reduzir a manutenção de eliminar manualmente os grupos.

A política de expiração é simples:

* Os proprietários do grupo são notificados para renovar um grupo prestes a expirar
* Um grupo que não seja renovado é eliminado
* Um grupo do Office 365 eliminado pode ser restaurado dentro de 30 dias por um proprietário do grupo ou por um administrador do Azure AD

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

Tem de ser um Administrador Global ou administrador de utilizadores na organização para configurar a expiração de grupo.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de utilizadores para grupos

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um Administrador Global ou administrador de utilizadores para a organização.

2. Selecione **Grupos** e, em seguida, selecione **Geral**.
  
   ![Página de definições de grupos self-service](./media/groups-quickstart-expiration/self-service-settings.png)

3. Defina **Os utilizadores podem criar grupos do Office 365** como **Sim**.

4. Selecione **Guardar** para guardar as definições dos grupos quando terminar.

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Inicie sessão para o [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** > **grupos** > **expiração** para Abra as definições de expiração.
  
   ![Página de definições de expiração de grupo](./media/groups-quickstart-expiration/expiration-settings.png)

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

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação do utilizador para grupos

1. Selecione **Azure Active Directory** > **Grupos** > **Geral**. 
2. Defina **Os utilizadores podem criar grupos do Office 365 em portais do Azure** como **Não**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a expiração, incluindo restrições técnicas, adicionar uma lista de palavras bloqueadas personalizadas e experiências de utilizador final nas aplicações do Office 365, veja o artigo seguinte que contém detalhes da política de expiração:

> [!div class="nextstepaction"]
> [Todos os detalhes da política de expiração](groups-lifecycle.md)
