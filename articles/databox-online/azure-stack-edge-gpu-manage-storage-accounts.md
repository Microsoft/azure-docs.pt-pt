---
title: Azure Stack Edge Pro GPU gestão de conta de armazenamento | Microsoft Docs
description: Descreve como usar o portal Azure para gerir a conta de armazenamento no Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201283"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Utilizar o portal do Azure para gerir contas de armazenamento do Edge no Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como gerir as contas de armazenamento Edge no seu Azure Stack Edge Pro. Pode gerir o Azure Stack Edge Pro através do portal Azure ou através da UI web local. Utilize o portal Azure para adicionar ou eliminar contas de armazenamento Edge no seu dispositivo.

## <a name="about-edge-storage-accounts"></a>Sobre contas de armazenamento Edge

Pode transferir dados do seu dispositivo Azure Stack Edge Pro através dos protocolos SMB, NFS ou REST. Para transferir dados para o armazenamento Blob utilizando as APIs REST, precisa de criar contas de armazenamento Edge no seu Azure Stack Edge Pro. 

As contas de armazenamento Edge que adiciona no dispositivo Azure Stack Edge Pro estão mapeadas para contas de Armazenamento Azure. Quaisquer dados escritos nas contas de armazenamento Edge são automaticamente empurrados para a nuvem.

Um diagrama que detalha os dois tipos de contas e como os dados fluem de cada uma destas contas para a Azure é mostrado abaixo:

![Diagrama para contas de armazenamento blob](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicione uma conta de armazenamento Edge
> * Eliminar uma conta de armazenamento Edge


## <a name="add-an-edge-storage-account"></a>Adicione uma conta de armazenamento Edge

Para criar uma conta de armazenamento Edge, faça o seguinte procedimento:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Eliminar uma conta de armazenamento Edge

Tome os seguintes passos para eliminar uma conta de armazenamento Edge.

1. Vá às **contas de > Desenhamento** de Configuração no seu recurso. Na lista de contas de armazenamento, selecione a conta de armazenamento que pretende eliminar. A partir da barra de comando superior, **selecione Eliminar a conta de armazenamento**.

    ![Ir à lista de contas de armazenamento](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. Na lâmina da **conta de armazenamento Eliminar,** confirme a conta de armazenamento para eliminar e selecionar **Eliminar**.

    ![Confirmar e apagar conta de armazenamento](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

A lista de contas de armazenamento é atualizada para refletir a supressão.


## <a name="add-delete-a-container"></a>Adicionar, eliminar um recipiente

Também pode adicionar ou apagar os recipientes para estas contas de armazenamento.

Para adicionar um recipiente, tome os seguintes passos:

1. Selecione a conta de armazenamento que pretende gerir. A partir da barra de comando superior, selecione **+ Adicionar o recipiente**.

    ![Selecione conta de armazenamento para adicionar recipiente](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Forneça um nome para o seu recipiente. Este recipiente é criado na sua conta de armazenamento Edge, bem como na conta de armazenamento Azure mapeada nesta conta. 

    ![Adicionar recipiente Edge](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

A lista de contentores é atualizada para refletir o recipiente recém-adicionado.

![Lista atualizada de contentores](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

Pode agora selecionar um recipiente desta lista e selecionar **+ Eliminar o recipiente** da barra de comando superior. 

![Eliminar um contentor](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Pode sincronizar as teclas de acesso para as contas de armazenamento Edge (local) do seu dispositivo. 

Para sincronizar a chave de acesso à conta de armazenamento, tome os seguintes passos:

1. No seu recurso, selecione a conta de armazenamento que pretende gerir. A partir da barra de comando superior, selecione **a tecla de armazenamento Sync**.

    ![Selecione a chave de armazenamento de sincronização](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Quando solicitado para confirmação, selecione **Sim**.

    ![Selecione a tecla de armazenamento de sincronização 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](azure-stack-edge-gpu-manage-users.md).
