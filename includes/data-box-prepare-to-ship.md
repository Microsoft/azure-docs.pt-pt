---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375819"
---
O passo final é preparar o dispositivo para envio. Neste passo, todas as partilhas do dispositivo são colocadas offline. Não é possível aceder às partilhas depois de iniciar este processo.

> [!IMPORTANT]
> A preparação para envio é necessária, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Este passo impede potenciais falhas no carregamento de dados devido a dados não conformes.

1. Aceda a **Preparar para enviar** e clique em **Iniciar preparação**. Por predefinição, as somas de verificação são calculadas enquanto os dados estão a ser copiados. A preparação para envio conclui o cálculo da soma de verificação e cria a lista de ficheiros ( *ficheiros -BOM* ). O cálculo de soma de verificação pode demorar horas ou dias, consoante o tamanho dos dados. 
   
    ![Preparar para enviar 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se, por qualquer motivo, quiser parar a preparação do dispositivo, clique em **Parar preparação**. Pode retomar a preparação para envio mais tarde.
        
    ![Preparar para enviar 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para envio e iniciada e as partilhas do dispositivo ficam offline. <!--You see a reminder to download the shipping label once the device is ready.--> O estado do dispositivo é atualizado para *Pronto para enviar* e o dispositivo fica bloqueado depois de concluída a preparação do dispositivo.
        
    ![Preparar para enviar 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se quiser copiar mais dados para o dispositivo, poderá desbloquear o dispositivo, copiar mais dados e executar a preparação para envio novamente.

    Caso haja erros neste passo, o estado é atualizado para *Análise concluída com erros*. Desbloqueie o dispositivo e aceda à página **Ligar e copiar** , transfira a lista de problemas e resolva os erros.

    ![Preparar para enviar 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Depois de os erros estarem resolvidos, execute a **Preparação para envio**.

4. Depois da conclusão da preparação para envio (sem erros), siga estes passos:

    1. Aponte o número de referência da conclusão. Dependendo do país em que estiver, esse número pode ser necessário para diferentes operações.
    2. Transfira a lista de ficheiros (também conhecida como manifesto) que foram copiados neste processo. Mais tarde, pode utilizar esta lista para verificar os ficheiros carregados para o Azure. Para obter mais informações, veja [Inspecionar ficheiros BOM durante a Preparação para envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Preparar para enviar 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Selecione e transfira as instruções de envio do dispositivo. As instruções de envio são diferentes consoante o país da sua localização.
    4. Se o E-ink não estiver a apresentar a guia de remessa, pode transferir a guia de remessa inversa aqui. 

5. Encerre o dispositivo. Aceda à página **Encerrar ou reiniciar** e clique em **Encerrar**. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

6. Remova os cabos. O passo seguinte é enviar o dispositivo para a Microsoft.
