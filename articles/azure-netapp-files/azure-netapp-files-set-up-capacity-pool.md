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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 7cb9cc6cc5ce98eb88e9b9cdf76f000c33f5509e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780716"
---
# <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

Configurar um conjunto de capacidade permite-lhe criar volumes no mesmo.  

## <a name="before-you-begin"></a>Antes de começar 

É necessário que já tenha criado uma conta NetApp.   

[Criar uma conta NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Passos 

1. Aceda ao painel de gestão para a sua conta de NetApp e, em seguida, no painel de navegação, clique em **conjuntos de capacidade**.  
    
    ![Navegue para o conjunto de capacidade](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Clique em **+ Adicionar conjuntos** para criar um novo conjunto de capacidade.   
    É apresentada a janela Novo Conjunto de Capacidade.

3. Forneça as seguintes informações para o novo conjunto de capacidade:  
  * **Nome**  
    Especifique o nome do conjunto de capacidade.  
    O nome de conjunto de capacidade tem de ser exclusivo para cada conta NetApp.

  * **Nível de serviço**   
    Este campo mostra o objetivo de desempenho do conjunto de capacidade.  
    Especifica o nível de serviço para o conjunto de capacidade: [**Premium** ](azure-netapp-files-service-levels.md#Premium) ou [ **padrão**](azure-netapp-files-service-levels.md#Standard).

  * **Tamanho**     
    Especifique o tamanho do conjunto de capacidade que está a comprar.        
    O tamanho mínimo do conjunto de capacidade é 4 TiB. Pode criar um conjunto com um tamanho que seja um múltiplo de 4 TiB.   
      
    ![Novo conjunto de capacidade](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes 

- [Níveis de serviço para os ficheiros do Azure NetApp](azure-netapp-files-service-levels.md)
- Consulte a [ficheiros do Azure NetApp página de preços](https://azure.microsoft.com/pricing/details/storage/netapp/) pelo preço de níveis de serviço diferentes
- [Delegar uma sub-rede para ficheiros do Azure NetApp](azure-netapp-files-delegate-subnet.md)
