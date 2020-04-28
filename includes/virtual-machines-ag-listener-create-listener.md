---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184208"
---
Neste passo, cria manualmente o ouvinte do grupo de disponibilidade no Failover Cluster Manager e no SQL Server Management Studio.

1. Open Failover Cluster Manager do nó que acolhe a réplica primária.

2. Selecione o nó **de Redes** e, em seguida, note o nome da rede de cluster. Este nome é usado na variável $ClusterNetworkName no script PowerShell.

3. Expanda o nome do cluster e, em seguida, clique em **Papéis**.

4. No painel **Roles,** clique no nome do grupo de disponibilidade e, em seguida, selecione Adicionar Ponto de Acesso ao**Cliente**de **Recursos** > .
   
    ![Adicionar Ponto de Acesso ao Cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Na caixa **Nome,** crie um nome para este novo ouvinte, clique em **Seguinte** duas vezes e, em seguida, clique em **Terminar**.  
    Não deixe o ouvinte ou o recurso online neste momento.

6. Clique no separador **Recursos** e, em seguida, expanda o ponto de acesso ao cliente que acabou de criar. 
    O recurso de endereço IP para cada rede de cluster no seu cluster é apresentado. Se esta for uma solução só para o Azure, apenas é apresentado um recurso ip address.

7. Efetue um dos seguintes procedimentos:
   
   * Para configurar uma solução híbrida:
     
        a. Clique no recurso de endereço IP que corresponde à sua subnet no local e, em seguida, selecione **Propriedades**. Note o nome do endereço IP e o nome da rede.
   
        b. Selecione **endereço IP estático,** atribua um endereço IP não utilizado e, em seguida, clique em **OK**.
 
   * Para configurar uma solução azure:

        a. Clique no recurso de endereço IP que corresponde à sua subnet Azure e, em seguida, selecione **Propriedades**.
       
       > [!NOTE]
       > Se o ouvinte não entrar mais tarde on-line devido a um endereço IP conflituoso selecionado pelo DHCP, pode configurar um endereço IP estático válido nesta janela de propriedades.
       > 
       > 

       b. Na mesma janela de propriedades ip **Address,** altere o nome do **endereço IP**.  
        Este nome é usado na variável $IPResourceName do script PowerShell. Se a sua solução abranger várias redes virtuais Azure, repita este passo para cada recurso IP.

