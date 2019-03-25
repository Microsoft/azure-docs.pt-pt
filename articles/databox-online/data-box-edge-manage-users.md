---
title: Edge de caixa de dados do Azure, gerir utilizadores | Documentos da Microsoft
description: Descreve como utilizar o portal do Azure para gerir utilizadores no seu limite de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 1fae648b4dc946227564e588c6cd9b70b910b2f7
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403447"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Utilizar o portal do Azure para gerir utilizadores no seu limite de caixa de dados do Azure

Este artigo descreve como gerir os utilizadores no seu limite de caixa de dados do Azure. Pode gerir o limite de caixa de dados do Azure através do portal do Azure ou através do local da interface do Usuário da web. Utilize o portal do Azure para adicionar, modificar ou eliminar utilizadores.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar um utilizador
> * Modificar utilizador
> * Eliminar um utilizador

## <a name="about-users"></a>Sobre os utilizadores

Os utilizadores podem ser só de leitura ou ter privilégios máximos. Conforme o nome indica, os utilizadores só de leitura só podem ver os dados de partilha. Os utilizadores com privilégios máximos podem ler dados de partilha, escrever nessas partilhas e modificar ou eliminar os dados de partilha.

 - **Utilizador com privilégios máximos** - um utilizador local com acesso total.
 - **Utilizador só de leitura** - um utilizador local com acesso só de leitura. Estes utilizadores estão associados a partilhas que permitem operações só de leitura.

As permissões de utilizador são definidas pela primeira vez quando o utilizador é criado durante a criação da partilha. Depois de as permissões associadas a um utilizador serem criadas, podem ser modificadas com o Explorador de Ficheiros. 


## <a name="add-a-user"></a>Adicionar um utilizador

Efetue os seguintes passos no portal do Azure para adicionar um utilizador.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **descrição geral > utilizadores**. Selecione **+ adicionar utilizador** na barra de comandos.

    ![Selecione adicionar utilizador](media/data-box-edge-manage-users/add-user-1.png)

2. Especifique o nome de utilizador e a palavra-passe do utilizador que pretende adicionar. Confirme a palavra-passe e selecione **adicionar**.

    ![Especifique o nome de utilizador e palavra-passe](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes utilizadores estão reservados pelo sistema e não devem ser utilizados: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Uma notificação é apresentada quando a criação de utilizador é iniciado e é concluída. Depois do utilizador é criado, na barra de comandos, selecione **atualizar** para ver a lista atualizada de utilizadores.


## <a name="modify-user"></a>Modificar utilizador

Pode alterar a palavra-passe associada a um utilizador depois de o utilizador ser criado. Selecione a lista de utilizadores. Introduza e confirme a palavra-passe nova. Guarde as alterações.
 
![Modificar utilizador](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminar um utilizador

Efetue os seguintes passos no portal do Azure para eliminar um utilizador.


1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **descrição geral > utilizadores**.

    ![Selecione o utilizador eliminar](media/data-box-edge-manage-users/delete-user-1.png)

2. Selecione um utilizador na lista de utilizadores e, em seguida, selecione **eliminar**.  

   ![Selecione Delete](media/data-box-edge-manage-users/delete-user-2.png)

3. Quando lhe for perguntado, confirme a eliminação. 

   ![Confirmar eliminação](media/data-box-edge-manage-users/delete-user-3.png)

A lista de utilizadores é atualizada para refletir o utilizador eliminado.

![Lista atualizada de utilizadores](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
