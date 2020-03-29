---
title: Azure Data Box Edge gere os utilizadores Microsoft Docs
description: Descreve como utilizar o portal Azure para gerir os utilizadores no seu Edge de Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946129"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Utilize o portal Azure para gerir os utilizadores no seu Edge de Caixa de Dados Azure

Este artigo descreve como gerir os utilizadores no seu Edge de Caixa de Dados Azure. Pode gerir o Azure Data Box Edge através do portal Azure ou através da UI web local. Utilize o portal do Azure para adicionar, modificar ou eliminar utilizadores.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar um utilizador
> * Modificar utilizador
> * Eliminar um utilizador

## <a name="about-users"></a>Sobre os utilizadores

Os utilizadores podem ser só de leitura ou ter privilégios máximos. Conforme o nome indica, os utilizadores só de leitura só podem ver os dados de partilha. Os utilizadores com privilégios máximos podem ler dados de partilha, escrever nessas partilhas e modificar ou eliminar os dados de partilha.

 - **Utilizador com privilégios máximos** - um utilizador local com acesso total.
 - **Utilizador só de leitura** - um utilizador local com acesso só de leitura. Estes utilizadores estão associados a partilhas que permitem operações só de leitura.

As permissões de utilizador são definidas pela primeira vez quando o utilizador é criado durante a criação da partilha. Atualmente, a modificação das permissões de nível de partilha não é suportada.

## <a name="add-a-user"></a>Adicionar um utilizador

Efetue os seguintes passos no portal do Azure para adicionar um utilizador.

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Overview > Utilizadores**. Selecione **+ Adicione** o utilizador na barra de comando.

    ![Selecione adicionar utilizador](media/data-box-edge-manage-users/add-user-1.png)

2. Especifique o nome de utilizador e a palavra-passe do utilizador que pretende adicionar. Confirme a palavra-passe e selecione **Adicionar**.

    ![Especificar o nome de utilizador e a palavra-passe](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes utilizadores estão reservados pelo sistema e não devem ser utilizados: Administrador, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Convidado.  

3. Uma notificação é mostrada quando a criação do utilizador começa e é concluída. Depois de criado o utilizador, a partir da barra de comando, selecione **Refresh** para visualizar a lista atualizada de utilizadores.


## <a name="modify-user"></a>Modificar utilizador

Pode alterar a palavra-passe associada a um utilizador depois de o utilizador ser criado. Selecione na lista de utilizadores. Introduza e confirme a nova senha. Guarde as alterações.
 
![Modificar utilizador](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminar um utilizador

Efetue os seguintes passos no portal do Azure para eliminar um utilizador.


1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá ao **Overview > Utilizadores**.

    ![Selecione utilizador para eliminar](media/data-box-edge-manage-users/delete-user-1.png)

2. Selecione um utilizador na lista de utilizadores e, em seguida, selecione **Eliminar**.  

   ![Selecione Excluir](media/data-box-edge-manage-users/delete-user-2.png)

3. Quando lhe for perguntado, confirme a eliminação. 

   ![Confirmar eliminação](media/data-box-edge-manage-users/delete-user-3.png)

A lista de utilizadores é atualizada para refletir o utilizador eliminado.

![Lista atualizada de utilizadores](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
