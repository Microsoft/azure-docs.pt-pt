---
title: Gerir instantâneos utilizando ficheiros Azure NetApp Microsoft Docs
description: Descreve como criar instantâneos para um volume ou restaurar de uma imagem para um novo volume utilizando ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483606"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerir instantâneos com o Azure NetApp Files

Pode utilizar os Ficheiros Azure NetApp para criar manualmente um instantâneo a pedido para um volume ou restaurar de uma imagem instantânea para um novo volume. O serviço Azure NetApp Files não cria automaticamente instantâneos de volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Crie um instantâneo a pedido para um volume

Só pode criar instantâneos a pedido. Atualmente, as políticas de snapshot não são apoiadas.

1.  A partir da lâmina volume, clique em **Snapshots**.

    ![Navegue para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique **+ Adicione instantâneo** para criar uma imagem a pedido para um volume.

    ![Adicionar instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela New Snapshot, forneça um nome para o novo instantâneo que está a criar.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

Atualmente, pode restaurar uma imagem apenas para um novo volume. 
1. Aceda à lâmina **'Gerir instantâneos'** da lâmina volume para visualizar a lista de instantâneos. 
2. Selecione uma foto para restaurar.  
3. Clique com o botão direito no nome do instantâneo e **selecione Restaurar para novo volume** a partir da opção menu.  

    ![Restaurar o instantâneo para um novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Na janela New Volume, forneça informações sobre o novo volume:  
    * **Nome**   
        Especifique o nome do volume que está a criar.  
        
        O nome tem de ser exclusivo dentro de um grupo de recursos. Deve ter pelo menos três caracteres de comprimento.  Pode utilizar carateres alfanuméricos.

    * **Caminho do arquivo**     
        Especifique o caminho de ficheiro que será utilizado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.   
        
        Um destino de montagem é o ponto final do endereço IP do serviço NFS. É gerado automaticamente.   
        
        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres. 

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.

    *   **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  
        O Vnet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. Pode aceder aos Ficheiros Azure NetApp apenas a partir do mesmo Vnet ou de um Vnet que se encontra na mesma região que o volume através do espreitamento da Vnet. Pode aceder ao volume a partir da sua rede no local através da Rota Expresso. 

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificou deve ser delegada no serviço Azure NetApp Files. Pode criar uma nova sub-rede selecionando **Criar novo** no campo Subnet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Clique em **OK**.   
    O novo volume ao qual o instantâneo é restaurado aparece na lâmina Volumes.

## <a name="next-steps"></a>Passos seguintes

[Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
