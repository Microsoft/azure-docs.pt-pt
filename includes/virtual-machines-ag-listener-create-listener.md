---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184208"
---
Neste passo, cria manualmente o ouvinte do grupo de disponibilidade no Failover Cluster Manager e no SQL Server Management Studio.

1. Open Failover Cluster Manager do nó que acolhe a réplica primária.

2. Selecione o nó **'Redes'** e, em seguida, observe o nome da rede de cluster. Este nome é usado na variável $ClusterNetworkName no script PowerShell.

3. Expanda o nome do cluster e, em seguida, clique em **Funções**.

4. No painel **Roles,** clique com o botão direito no nome do grupo de disponibilidade e, em seguida, **selecione Adicionar**Ponto de Acesso ao Cliente de Recurso  >  **Client Access Point**.
   
    ![Adicionar ponto de acesso ao cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Na caixa **Nome,** crie um nome para este novo ouvinte, clique em **Next** duas vezes e, em seguida, clique em **Terminar**.  
    Não leve o ouvinte ou o recurso on-line neste momento.

6. Clique no separador **Recursos** e, em seguida, expanda o ponto de acesso ao cliente que acabou de criar. 
    É apresentado o recurso de endereço IP para cada rede de clusters do seu cluster. Se esta for uma solução apenas para o Azure, apenas é apresentado um recurso de endereço IP.

7. Efetue um dos seguintes procedimentos:
   
   * Para configurar uma solução híbrida:
     
        a. Clique com o botão direito no recurso de endereço IP que corresponde à sua sub-rede no local e, em seguida, selecione **Propriedades**. Note o nome do endereço IP e o nome da rede.
   
        b. Selecione **O Endereço IP Estático**, atribua um endereço IP não uused e, em seguida, clique em **OK**.
 
   * Para configurar uma solução azure-only:

        a. Clique com o botão direito no recurso de endereço IP que corresponde à sua sub-rede Azure e, em seguida, selecione **Propriedades**.
       
       > [!NOTE]
       > Se o ouvinte mais tarde não ficar on-line devido a um endereço IP conflituoso selecionado pelo DHCP, pode configurar um endereço IP estático válido nesta janela de propriedades.
       > 
       > 

       b. Na mesma janela de propriedades **do endereço IP,** altere o **Nome do Endereço IP**.  
        Este nome é usado na variável $IPResourceName do script PowerShell. Se a sua solução abranger várias redes virtuais Azure, repita este passo para cada recurso IP.

