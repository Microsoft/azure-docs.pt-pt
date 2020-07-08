---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184210"
---
1. Em Failover Cluster Manager, expanda **funções**e, em seguida, realce o seu grupo de disponibilidade.  

2. No separador **Recursos,** clique com o botão direito no nome do ouvinte e, em seguida, clique em **Propriedades**.

3. Clique no separador **Dependências.** Se vários recursos estiverem listados, verifique se os endereços IP têm ou, não E, dependências.  

4. Clique em **OK**.

5. Clique com o botão direito no nome do ouvinte e, em seguida, clique em **Bring Online**.

6. Depois de o ouvinte estar online, no separador **Recursos,** clique com o botão direito no grupo de disponibilidade e, em seguida, clique em **Propriedades**.
   
    ![Configure o recurso do grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Crie uma dependência do recurso de nome do ouvinte (não o nome de recursos de endereço IP) e, em seguida, clique em **OK**.
   
    ![Adicione dependência do nome do ouvinte](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Inicie o SQL Server Management Studio e, em seguida, ligue-se à réplica primária.

9. Vá ao **AlwaysOn High**  >  **Availability Availability**  >  **\<AvailabilityGroupName\>**  >  **Groups Disponibilidade Grupos Ouvintes**do grupo .  
    O nome de ouvinte que criou no Failover Cluster Manager deve ser exibido.

10. Clique com o botão direito no nome do ouvinte e, em seguida, clique em **Propriedades**.

11. Na caixa **de Porta,** especifique o número da porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort que utilizou anteriormente (neste tutorial, 1433 foi o padrão), e clique em **OK**.

