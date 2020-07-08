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
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: d76af4901103b0eed8cd1cffac744f8fb41d9689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483504"
---
# <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

Configurar um conjunto de capacidade permite-lhe criar volumes no mesmo.  

## <a name="before-you-begin"></a>Before you begin 

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
      
     ![Novo conjunto de capacidade](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Clique em **OK**.

## <a name="next-steps"></a>Próximos passos 

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- Consulte a página de preços dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/storage/netapp/) para obter o preço de diferentes níveis de serviço
- [Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
