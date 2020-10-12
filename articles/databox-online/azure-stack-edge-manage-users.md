---
title: Azure Stack Edge Pro gere os utilizadores Microsoft Docs
description: Descreve como usar o portal Azure para gerir os utilizadores no seu Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 3fb82e95d9d7dcfdf59bd05f91beff8e1184955e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904378"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-azure-stack-edge-pro"></a>Utilize o portal Azure para gerir os utilizadores no Azure Azure Stack Edge Pro

Este artigo descreve como gerir os utilizadores no seu Azure Stack Edge Pro. Pode gerir o Azure Stack Edge Pro através do portal Azure ou através da UI web local. Utilize o portal do Azure para adicionar, modificar ou eliminar utilizadores.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar um utilizador
> * Modificar utilizador
> * Eliminar um utilizador

## <a name="about-users"></a>Sobre os utilizadores

Os utilizadores podem ser só de leitura ou ter privilégios máximos. Conforme o nome indica, os utilizadores só de leitura só podem ver os dados de partilha. Os utilizadores com privilégios máximos podem ler dados de partilha, escrever nessas partilhas e modificar ou eliminar os dados de partilha.

 - **Utilizador com privilégios máximos** - um utilizador local com acesso total.
 - **Utilizador só de leitura** - um utilizador local com acesso só de leitura. Estes utilizadores estão associados a partilhas que permitem operações só de leitura.

As permissões de utilizador são definidas pela primeira vez quando o utilizador é criado durante a criação da partilha. Atualmente, não é suportada a modificação das permissões de nível de ação.

## <a name="add-a-user"></a>Adicionar um utilizador

Efetue os seguintes passos no portal do Azure para adicionar um utilizador.

1. No portal Azure, aceda ao seu recurso Azure Stack Edge e, em seguida, vá à **Visão Geral > Utilizadores**. **Selecione + Adicione** o utilizador na barra de comando.

    ![Selecione adicionar utilizador](media/azure-stack-edge-manage-users/add-user-1.png)

2. Especifique o nome de utilizador e a palavra-passe do utilizador que pretende adicionar. Confirme a palavra-passe e **selecione Adicionar**.

    ![Especifique o nome de utilizador e a palavra-passe](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes utilizadores estão reservados pelo sistema e não devem ser utilizados: Administrador, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Convidado.  

3. Uma notificação é mostrada quando a criação do utilizador começa e é concluída. Depois de o utilizador ser criado, a partir da barra de comando, selecione **Refresh** para ver a lista atualizada de utilizadores.


## <a name="modify-user"></a>Modificar utilizador

Pode alterar a palavra-passe associada a um utilizador depois de o utilizador ser criado. Selecione na lista de utilizadores. Insira e confirme a nova senha. Guarde as alterações.
 
![Modificar utilizador](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminar um utilizador

Efetue os seguintes passos no portal do Azure para eliminar um utilizador.


1. No portal Azure, aceda ao seu recurso Azure Stack Edge e, em seguida, vá à **Visão Geral > Utilizadores**.

    ![Selecione o utilizador para eliminar](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Selecione um utilizador na lista de utilizadores e, em seguida, **selecione Delete**.  

   ![Selecione Eliminar](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Quando lhe for perguntado, confirme a eliminação. 

   ![Confirmar eliminação](media/azure-stack-edge-manage-users/delete-user-3.png)

A lista de utilizadores é atualizada para refletir o utilizador eliminado.

![Lista atualizada de utilizadores](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).
