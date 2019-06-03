---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419574"
---
O passo final é preparar o dispositivo para envio. Neste passo, todas as partilhas do dispositivo são colocadas offline. Não não possível aceder as partilhas depois de iniciar este processo.

> [!IMPORTANT]
> Preparação para envio é necessária, como sinalizadores de dados que não estão em conformidade com as convenções de nomenclatura do Azure. A ignorar este passo foi resultem em dados de potenciais falhas de carregamento devido a dados não autorizadas.

1. Aceda a **Preparar para enviar** e clique em **Iniciar preparação**. Por predefinição, as somas de verificação são calculadas, enquanto os dados estão a ser copiados. Preparação para envio conclui o cálculo de soma de verificação e cria a lista de ficheiros (também conhecido como *ficheiros BOM* ou o manifesto). O cálculo de soma de verificação pode demorar horas a dias dependendo do tamanho dos seus dados.
   
    ![Preparar para enviar 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Se por algum motivo quiser interromper a preparação do dispositivo, clique em **parar preparação**. Pode retomar a preparação para envio posterior.
        
    ![Preparação para envio 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para envio é iniciado e as partilhas de dispositivo offline. Verá um lembrete para transferir a etiqueta quando o dispositivo está pronto.

    ![Baixe o lembrete de etiqueta de envio](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Atualizações do Estado do dispositivo *pronto para enviar* e o dispositivo está bloqueado depois de concluída a preparação do dispositivo.
        
    ![Preparação para envio 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Se desejar copiar mais dados para o dispositivo, pode desbloquear o dispositivo, copiar mais dados e execução de preparação para envio de novamente.

    Se houver erros neste passo, transfira o registo de erros e resolva os erros. Depois dos erros são resolvidos, execute **preparação para envio**.

4. Após a preparação para envio é download concluído com êxito (sem nenhum erro), a lista de ficheiros (também conhecido como *ficheiros BOM* ou o manifesto) copiados nesse processo. 

    ![Transferir a lista de ficheiros ou ficheiros BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Mais tarde, pode utilizar esta lista para verificar os ficheiros carregados para o Azure. Para obter mais informações, consulte [ficheiros de inspecionar BOM durante a preparação para envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Ficheiro de BOM de exemplo](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Encerre o dispositivo. Aceda à página **Encerrar ou reiniciar** e clique em **Encerrar**. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

    ![Encerrar o primeiro nó de dispositivo](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Repita os passos acima para o outro nó do dispositivo.
7. Depois do dispositivo completamente foi encerrado, todos os LEDs parte traseira desse dispositivo seriam tiver desativado. A próxima etapa é remover todos os cabos e enviar o dispositivo para a Microsoft.
