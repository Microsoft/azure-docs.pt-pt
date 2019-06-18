---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165407"
---
Neste passo, vai criar manualmente o serviço de escuta do grupo de disponibilidade no Gestor de clusters de ativação pós-falha e o SQL Server Management Studio.

1. Abra o Gestor de clusters de ativação pós-falha do nó que aloja a réplica primária.

2. Selecione o **redes** nó e, em seguida, tenha em atenção o nome de rede do cluster. Este nome é utilizado na variável $ClusterNetworkName no script do PowerShell.

3. Expanda o nome do cluster e, em seguida, clique em **funções**.

4. Na **funções** painel, clique com o botão direito no nome do grupo de disponibilidade e, em seguida, selecione **adicionar recursos** > **ponto de acesso de cliente**.
   
    ![Adicionar o ponto de acesso de cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Na **nome** caixa, criar um nome para este novo serviço de escuta, clique em **próxima** duas vezes e, em seguida, clique em **concluir**.  
    Não inclua o serviço de escuta ou recurso online neste momento.

6. Clique nas **recursos** separador e, em seguida, expanda o ponto de acesso de cliente que acabou de criar. 
    O recurso de endereço IP para cada rede de cluster no seu cluster é apresentado. Se se tratar de uma solução apenas do Azure, apenas um recurso de endereço IP é apresentado.

7. Efetue um dos seguintes procedimentos:
   
   * Para configurar uma solução híbrida:
     
        a. O recurso de endereço IP que corresponde à sua sub-rede no local com o botão direito e, em seguida, selecione **propriedades**. Tenha em atenção o nome do endereço IP e o nome de rede.
   
        b. Selecione **endereço IP estático**, atribua um endereço IP não utilizado e, em seguida, clique em **OK**.
 
   * Para configurar uma solução apenas do Azure:

        a. O recurso de endereço IP que corresponde à sua sub-rede do Azure com o botão direito e, em seguida, selecione **propriedades**.
       
       > [!NOTE]
       > Se o serviço de escuta falhar, mais tarde, fique online devido a um endereço IP em conflito selecionado pelo DHCP, pode configurar um endereço IP estático válido nesta janela de propriedades.
       > 
       > 

       b. Da mesma **endereço IP** janela de propriedades, altere a **nome do endereço IP**.  
        Este nome é utilizado na variável $IPResourceName do script do PowerShell. Se sua solução abrange várias redes virtuais do Azure, repita este passo para cada recurso IP.

