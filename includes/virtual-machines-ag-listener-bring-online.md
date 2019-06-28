---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184210"
---
1. No Gestor de clusters de ativação pós-falha, expanda **funções**e, em seguida, realce o grupo de disponibilidade.  

2. Sobre o **recursos** separador, o nome do serviço de escuta com o botão direito e, em seguida, clique em **propriedades**.

3. Clique nas **dependências** separador. Se forem apresentados vários recursos, certifique-se de que os endereços IP têm ou, não AND, as dependências.  

4. Clique em **OK**.

5. O nome do serviço de escuta com o botão direito e, em seguida, clique em **colocar Online**.

6. Depois do serviço de escuta está online, no **recursos** separador, o grupo de disponibilidade com o botão direito e, em seguida, clique em **propriedades**.
   
    ![Configure o recurso do grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Criar uma dependência no recurso de nome do serviço de escuta (não o recursos nome do endereço IP) e, em seguida, clique em **OK**.
   
    ![Adicionar dependência no nome do serviço de escuta](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Inicie o SQL Server Management Studio e, em seguida, ligue para a réplica primária.

9. Aceda a **AlwaysOn elevada disponibilidade** > **grupos de disponibilidade** >  **\<AvailabilityGroupName\>**   >  **Serviços de escuta do grupo de disponibilidade**.  
    O nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha deverá ser apresentado.

10. O nome do serviço de escuta com o botão direito e, em seguida, clique em **propriedades**.

11. Na **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com o $EndpointPort que utilizou anteriormente (neste tutorial, 1433 era o padrão) e, em seguida, clique em **OK**.

