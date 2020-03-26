---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67184923"
---
O passo final é preparar o dispositivo para envio. Neste passo, todas as partilhas do dispositivo são colocadas offline. Não é possível aceder às partilhas depois de iniciar este processo.

> [!IMPORTANT]
> A preparação para envio é necessária, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Ignorar este passo pode resultar em possíveis falhas no carregamento dos dados devido à não conformidade dos dados.

1. Aceda a **Preparar para enviar** e clique em **Iniciar preparação**. Por predefinição, as somas de verificação são calculadas enquanto os dados estão a ser copiados. A preparação para envio conclui o cálculo da soma de verificação e cria a lista de ficheiros (*ficheiros -BOM*). O cálculo de soma de verificação pode demorar horas ou dias, consoante o tamanho dos dados. 
   
    ![Preparar para enviar 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se, por qualquer motivo, quiser parar a preparação do dispositivo, clique em **Parar preparação**. Pode retomar a preparação para envio mais tarde.
        
    ![Preparar para enviar 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para envio e iniciada e as partilhas do dispositivo ficam offline. Verá um lembrete para transferir a guia de remessa assim que o dispositivo estiver pronto.

    ![Lembrete para transferir a guia de remessa](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. O estado do dispositivo é atualizado para *Pronto para enviar* e o dispositivo fica bloqueado depois de concluída a preparação do dispositivo.
        
    ![Preparar para enviar 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se quiser copiar mais dados para o dispositivo, poderá desbloquear o dispositivo, copiar mais dados e executar a preparação para envio novamente.

    Se ocorrerem erros neste passo, terá de transferir o registo de erros e resolver os erros. Depois de os erros estarem resolvidos, execute a **Preparação para envio**.

4. Depois de a preparação para envio estar concluída com êxito (sem erros), transfira a lista de ficheiros (também conhecida como o manifesto) copiada neste processo. Mais tarde, pode utilizar esta lista para verificar os ficheiros carregados para o Azure. Para obter mais informações, veja [Inspecionar ficheiros BOM durante a Preparação para envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Preparar para enviar 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Encerre o dispositivo. Aceda à página **Encerrar ou reiniciar** e clique em **Encerrar**. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

6. Remova os cabos. O passo seguinte é enviar o dispositivo para a Microsoft.
