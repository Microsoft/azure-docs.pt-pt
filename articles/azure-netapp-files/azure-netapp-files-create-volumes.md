---
title: Criar um volume para o Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume para o Azure NetApp Files.
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
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 0d4629651e17c917720196c1e5f6152c50c2a33a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770271"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Criar um volume para o Azure NetApp Files

O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.  Pode criar vários volumes num conjunto de capacidade, mas o consumo total de capacidade dos volumes não pode exceder o tamanho do conjunto. 

## <a name="before-you-begin"></a>Antes de começar 
Tem de ter um conjunto de capacidade já configurado.   
[Configure um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede tem de ser delegada a ficheiros do Azure NetApp.  
[Delegar uma sub-rede para ficheiros do Azure NetApp](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Passos 
1.  Clique no painel **Volumes**, a partir do painel Gerir Conjuntos de Capacidade. 
2.  Clique em **+ Adicionar volume** para criar um volume.  
    É apresentada a janela Novo Volume.

3.  Na janela Novo Volume, clique em **Criar** e forneça informações para os seguintes campos:   
    * **Nome**      
        Especifique o nome do volume que está a criar.   

        O nome tem de ser exclusivo dentro de um grupo de recursos. Tem de ser, pelo menos, três carateres de comprimento.  Pode utilizar carateres alfanuméricos.

    * **Caminho do ficheiro**  
        Especifique o caminho de ficheiro que será utilizado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e aceder ao volume.   
     
        O nome de ficheiro pode conter apenas letras, números e hífenes ("-"). Tem de ter entre 16 e 40 carateres.  

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual do Azure (Vnet) a partir da qual pretende aceder ao volume.  

        A Vnet que especificar tem de ter uma sub-rede de delegado ao serviço ficheiros do Azure NetApp. O serviço de ficheiros do Azure NetApp pode ser acedido apenas a partir da mesma Vnet ou de uma Vnet que está na mesma região que o volume através de Vnet peering. Também pode acessar o volume a partir da sua rede no local através de Express Route.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificar tem de ser delegada a ficheiros do Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar **criar novo** no criar um Volume página. Em seguida, na página Criar sub-rede, especifique as informações de sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros de NetApp do Azure. Tenha em atenção que, em cada Vnet, pode ser delegada apenas uma sub-rede de ficheiros de NetApp do Azure.   
 
        ![Novo volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Clique em **OK**. 
 
Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="next-steps"></a>Passos Seguintes  
* [Configurar a política de exportação para um volume (opcional)](azure-netapp-files-configure-export-policy.md)
* [Saiba mais sobre a integração da rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

