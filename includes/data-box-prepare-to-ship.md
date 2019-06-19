---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184923"
---
O passo final é preparar o dispositivo para envio. Neste passo, todas as partilhas do dispositivo são colocadas offline. Não não possível aceder as partilhas depois de iniciar este processo.

> [!IMPORTANT]
> Preparação para envio é necessária, como sinalizadores de dados que não estão em conformidade com as convenções de nomenclatura do Azure. A ignorar este passo foi resultem em dados de potenciais falhas de carregamento devido a dados não autorizadas.

1. Aceda a **Preparar para enviar** e clique em **Iniciar preparação**. Por predefinição, as somas de verificação são calculadas, enquanto os dados estão a ser copiados. Preparação para envio conclui o cálculo de soma de verificação e cria a lista de ficheiros ( *ficheiros - BOM*). O cálculo de soma de verificação pode demorar horas a dias dependendo do tamanho dos seus dados. 
   
    ![Preparar para enviar 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se por algum motivo quiser interromper a preparação do dispositivo, clique em **parar preparação**. Pode retomar a preparação para envio posterior.
        
    ![Preparação para envio 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para envio é iniciado e as partilhas de dispositivo offline. Verá um lembrete para transferir a etiqueta quando o dispositivo está pronto.

    ![Baixe o lembrete de etiqueta de envio](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Atualizações do Estado do dispositivo *pronto para enviar* e o dispositivo está bloqueado depois de concluída a preparação do dispositivo.
        
    ![Preparação para envio 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se desejar copiar mais dados para o dispositivo, pode desbloquear o dispositivo, copiar mais dados e execução de preparação para envio de novamente.

    Se houver erros neste passo, terá de transferir o registo de erros e resolva os erros. Depois dos erros são resolvidos, execute **preparação para envio**.

4. Após a preparação para envio concluída com sucesso (sem nenhum erro), baixe a lista de ficheiros (também conhecido como manifesto) copiada nesse processo. Mais tarde, pode utilizar esta lista para verificar os ficheiros carregados para o Azure. Para obter mais informações, consulte [ficheiros de inspecionar BOM durante a preparação para envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Preparar para enviar 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Encerre o dispositivo. Aceda à página **Encerrar ou reiniciar** e clique em **Encerrar**. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

6. Remova os cabos. O passo seguinte é enviar o dispositivo para a Microsoft.
