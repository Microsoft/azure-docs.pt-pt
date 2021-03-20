---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a01f91a81629800d3f03b907c65f05433b6163e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376162"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Para parar e iniciar uma aplicação da cloud

1. Para parar uma aplicação da cloud, vá para a VM da mesma.
    ![Máquina Virtual do StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. A partir da barra de comandos, clique em **Parar**.

    ![Máquina virtual de eletrodoméstico storSimple cloud 2](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Máquina virtual de aparelho em nuvem StorSimple 3](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Quando parar uma VM, esta é desalocada. Enquanto a aplicação da cloud está a parar, o respetivo estado é **A desalocar**. Depois de a aplicação da cloud estar parada, o respetivo estado é **Parada (desalocada)**.

    ![Máquina virtual de aparelho em nuvem StorSimple 4](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Depois de uma VM estar parada, clique em **Iniciar** (o botão torna-se disponível) para iniciar a VM. Depois de a aplicação da cloud ter iniciado, o respetivo estado é **Iniciado**.

    ![Máquina virtual de aparelho em nuvem StorSimple 5](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Utilize os seguintes cmdlets para parar e iniciar uma aplicação da cloud.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Para reiniciar uma aplicação da cloud

Para reiniciar uma aplicação da cloud, vá para a VM da mesma. A partir da barra de comandos, clique em **Reiniciar**. Quando lhe for perguntado, confirme o reinício. Quando a aplicação da cloud estiver pronta para ser utilizada, o seu estado será **Em Execução**.

![Máquina virtual de aparelho em nuvem StorSimple 6](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Utilize os seguintes cmdlets para reiniciar uma aplicação da cloud.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

