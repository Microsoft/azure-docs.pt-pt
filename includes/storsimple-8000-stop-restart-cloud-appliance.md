---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184317"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Para parar e iniciar uma aplicação da cloud

1. Para parar uma aplicação da cloud, vá para a VM da mesma.
    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. A partir da barra de comandos, clique em **Parar**.

    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Quando parar uma VM, esta é desalocada. Enquanto a aplicação da cloud está a parar, o respetivo estado é **A desalocar**. Depois de a aplicação da cloud estar parada, o respetivo estado é **Parada (desalocada)**.

    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Depois de uma VM estar parada, clique em **Iniciar** (o botão torna-se disponível) para iniciar a VM. Depois de a aplicação da cloud ter iniciado, o respetivo estado é **Iniciado**.

    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Utilize os seguintes cmdlets para parar e iniciar uma aplicação da cloud.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Para reiniciar uma aplicação da cloud

Para reiniciar uma aplicação da cloud, vá para a VM da mesma. A partir da barra de comandos, clique em **Reiniciar**. Quando lhe for perguntado, confirme o reinício. Quando a aplicação da cloud estiver pronta para ser utilizada, o seu estado será **Em Execução**.

![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Utilize os seguintes cmdlets para reiniciar uma aplicação da cloud.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

