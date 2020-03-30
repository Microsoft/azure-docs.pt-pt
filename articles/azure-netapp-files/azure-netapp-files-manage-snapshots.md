---
title: Gerir instantâneos utilizando ficheiros Azure NetApp [ Microsoft Docs
description: Descreve como criar instantâneos para um volume ou restaurar de um instantâneo para um novo volume utilizando ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267906"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerir instantâneos utilizando ficheiros Azure NetApp

Pode utilizar ficheiros Azure NetApp para criar manualmente um instantâneo a pedido para um volume ou restaurar de um instantâneo para um novo volume. O serviço Azure NetApp Files não cria automaticamente instantâneos de volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Criar um instantâneo a pedido para um volume

Só se pode criar instantâneos a pedido. As políticas instantâneas não são atualmente apoiadas.

1.  A partir da lâmina volume, clique em **Snapshots**.

    ![Navegar para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique **+ Adicione instantâneo** para criar um instantâneo a pedido para um volume.

    ![Adicione instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela New Snapshot, forneça um nome para o novo instantâneo que está a criar.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

Atualmente, você pode restaurar um instantâneo apenas para um novo volume. 
1. Vá à lâmina **Manage Snapshots** da lâmina volume para exibir a lista de instantâneos. 
2. Selecione um instantâneo para restaurar.  
3. Clique no nome snapshot e selecione **Restaurar para novo volume** a partir da opção menu.  

    ![Restaurar o instantâneo para o novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Na janela New Volume, forneça informações para o novo volume:  
    * **Nome**   
        Especifique o nome do volume que está a criar.  
        
        O nome tem de ser exclusivo dentro de um grupo de recursos. Deve ter pelo menos três caracteres de comprimento.  Pode utilizar carateres alfanuméricos.

    * **Caminho de arquivo**     
        Especifique o caminho de ficheiro que será utilizado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.   
        
        Um destino de montagem é o ponto final do endereço IP do serviço NFS. É gerado automaticamente.   
        
        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres. 

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.

    *   **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  
        O Vnet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. Só pode aceder aos Ficheiros Azure NetApp a partir do mesmo Vnet ou de um Vnet que se encontra na mesma região que o volume através do peering Vnet. Pode aceder ao volume da sua rede no local através da Rota Expresso. 

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especifica deve ser delegada no serviço Deficheiros Azure NetApp. Pode criar uma nova sub-rede selecionando **criar nova** sob o campo Subnet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Clique em **OK**.   
    O novo volume para o qual o instantâneo é restaurado aparece na lâmina Volumes.

## <a name="next-steps"></a>Passos seguintes

[Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
