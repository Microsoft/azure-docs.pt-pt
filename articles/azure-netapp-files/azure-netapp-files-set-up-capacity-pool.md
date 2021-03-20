---
title: Definir um conjunto de capacidade para o Azure NetApp Files | Microsoft Docs
description: Descreve como configurar um conjunto de capacidade para que possa criar volumes no mesmo.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325420"
---
# <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

Configurar um conjunto de capacidade permite-lhe criar volumes no mesmo.  

## <a name="before-you-begin"></a>Antes de começar 

É necessário que já tenha criado uma conta NetApp.   

[Criar uma conta NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Passos 

1. Vá à lâmina de gestão para a sua conta NetApp e, em seguida, a partir do painel de navegação, clique em **pools de capacidade**.  
    
    ![Navegue para a piscina de capacidade](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Clique em **+ Adicionar conjuntos** para criar um novo conjunto de capacidade.   
    É apresentada a janela Novo Conjunto de Capacidade.

3. Forneça as seguintes informações para o novo conjunto de capacidade:  
   * **Nome**  
     Especifique o nome do conjunto de capacidade.  
     O nome de conjunto de capacidade tem de ser exclusivo para cada conta NetApp.

   * **Nível de serviço**   
     Este campo mostra o objetivo de desempenho do conjunto de capacidade.  
     Especificar o nível de serviço para o pool de capacidade: [**Ultra,**](azure-netapp-files-service-levels.md#Ultra) [**Premium**](azure-netapp-files-service-levels.md#Premium)ou [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **Tamanho**     
     Especifique o tamanho do conjunto de capacidade que está a comprar.        
     O tamanho mínimo do conjunto de capacidade é 4 TiB. Pode criar um conjunto com um tamanho que seja um múltiplo de 4 TiB.   

   * **QoS**   
     Especificar se o pool de capacidade deve utilizar **o** manual ou o tipo **Auto** QoS.  

     Consulte [a Hierarquia de Armazenamento](azure-netapp-files-understand-storage-hierarchy.md) e [Considerações de Desempenho](azure-netapp-files-performance-considerations.md) para entender os tipos QoS.  

     > [!IMPORTANT] 
     > A definição **do tipo QoS** para **Manual** é permanente. Não é possível converter uma piscina manual de capacidade QoS para utilizar o Auto QoS. No entanto, pode converter um pool de capacidade auto QoS para utilizar o QoS manual. Consulte [Alterar uma piscina de capacidade para utilizar o QoS manual](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > A utilização do tipo QoS manual para um pool de capacidade requer registo. Consulte [Gerir uma piscina manual de capacidade QoS](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Novo conjunto de capacidade](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Clique em **Criar**.

## <a name="next-steps"></a>Passos seguintes 

- [Hierarquia de Armazenamento](azure-netapp-files-understand-storage-hierarchy.md) 
- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
- [Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
