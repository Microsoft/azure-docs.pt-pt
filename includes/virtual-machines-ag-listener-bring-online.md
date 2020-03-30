---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184210"
---
1. No Failover Cluster Manager, expanda **as Funções**e destaque o seu grupo de disponibilidade.  

2. No separador **Recursos,** clique no nome do ouvinte e clique em **Propriedades**.

3. Clique no separador **Dependências.** Se vários recursos estiverem listados, verifique se os endereços IP têm dependências de OR, não E, e.  

4. Clique em **OK**.

5. Clique no nome do ouvinte e clique em **Bring Online**.

6. Depois de o ouvinte estar online, no separador **Recursos,** clique no grupo de disponibilidade e clique em **Propriedades**.
   
    ![Configure o recurso do grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Crie uma dependência do recurso de nome do ouvinte (não o nome dos recursos de endereço IP) e, em seguida, clique EM **OK**.
   
    ![Adicione dependência do nome do ouvinte](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Inicie o Estúdio de Gestão de Servidores SQL e, em seguida, ligue-se à réplica primária.

9. Vá a **AlwaysOn High Availability** > **Availability Availability Group** > **\<\>AvailabilityGroupName** > **Availability Group Listeners**.  
    O nome do ouvinte que criou no Failover Cluster Manager deve ser exibido.

10. Clique no nome do ouvinte e, em seguida, clique em **Propriedades**.

11. Na caixa **do Porto,** especifique o número de porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort que utilizou anteriormente (neste tutorial, 1433 foi o padrão), e, em seguida, **clique**OK .

