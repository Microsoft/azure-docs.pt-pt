---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "66419574"
---
O passo final é preparar o dispositivo para envio. Neste passo, todas as partilhas do dispositivo são colocadas offline. Não é possível aceder às partilhas depois de iniciar este processo.

> [!IMPORTANT]
> A preparação para envio é necessária, pois sinaliza os dados que não estão em conformidade com as convenções de nomenclatura do Azure. Ignorar este passo pode resultar em possíveis falhas no carregamento dos dados devido à não conformidade dos dados.

1. Aceda a **Preparar para enviar** e clique em **Iniciar preparação**. Por predefinição, as somas de verificação são calculadas enquanto os dados estão a ser copiados. A preparação para envio conclui o cálculo da soma de verificação e cria a lista de ficheiros (também conhecidos como *ficheiros BOM* ou o manifesto). O cálculo de soma de verificação pode demorar horas ou dias, consoante o tamanho dos dados.
   
    ![Preparar para enviar 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Se, por qualquer motivo, quiser parar a preparação do dispositivo, clique em **Parar preparação**. Pode retomar a preparação para envio mais tarde.
        
    ![Preparar para enviar 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. A preparação para envio e iniciada e as partilhas do dispositivo ficam offline. Verá um lembrete para transferir a guia de remessa assim que o dispositivo estiver pronto.

    ![Lembrete para transferir a guia de remessa](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. O estado do dispositivo é atualizado para *Pronto para enviar* e o dispositivo fica bloqueado depois de concluída a preparação do dispositivo.
        
    ![Preparar para enviar 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Se quiser copiar mais dados para o dispositivo, poderá desbloquear o dispositivo, copiar mais dados e executar a preparação para envio novamente.

    Se ocorrerem erros neste passo, transfira o registo de erros e resolva os erros. Depois de os erros estarem resolvidos, execute a **Preparação para envio**.

4. Depois de a preparação para envio estar concluída com êxito (sem erros), transfira a lista de ficheiros (também conhecida como *ficheiros BOM* ou o manifesto) copiada neste processo. 

    ![Transferir a lista de ficheiros ou os ficheiros BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Mais tarde, pode utilizar esta lista para verificar os ficheiros carregados para o Azure. Para obter mais informações, veja [Inspecionar ficheiros BOM durante a Preparação para envio](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Ficheiro BOM de exemplo](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Encerre o dispositivo. Aceda à página **Encerrar ou reiniciar** e clique em **Encerrar**. Quando lhe for pedida a confirmação, clique em **OK** para continuar.

    ![Desligar o primeiro nó do dispositivo](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Repita todos os passos acima para o outro nó de dispositivo.
7. Depois de o dispositivo estar completamente desligado, todos os LEDs na parte posterior do dispositivo estarão apagados. O próximo passo é remover todos os cabos e enviar o dispositivo para a Microsoft.
