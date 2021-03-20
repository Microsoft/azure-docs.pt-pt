---
title: Instale a Atualização 5.1 no dispositivo da série StorSimple 8000 | Microsoft Docs
description: Explica como instalar o StorSimple 8000 Series Update 5.1 no seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91575967"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Instale a atualização 5.1 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição Geral

Este tutorial explica como instalar o Update 5.1 num dispositivo StorSimple que executa uma versão de software anterior através do portal Azure. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

A atualização 5.1 inclui atualizações de segurança não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal Azure <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * A atualização 5.1 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte [as notas de lançamento do Update 5.1](storsimple-update51-release-notes.md).
> * Um conjunto de pré-verificações manuais e automáticas são feitos antes da instalação para determinar a saúde do dispositivo em termos de estado de hardware e conectividade de rede. Estas pré-verificações só são realizadas se aplicar as atualizações a partir do portal Azure.
> * Se pretender instalar utilizando o método hotfix, contacte o [Microsoft Support](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Instalar atualização 5.1 através do portal Azure

Execute os seguintes passos para atualizar o seu dispositivo para [atualizar 5.1](storsimple-update51-release-notes.md).

> [!NOTE]
> A Microsoft retira informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo.

    ![Selecione dispositivo](./media/storsimple-8000-install-update-51/update1.png)

2. Navegue para **as definições do dispositivo** As  >  **atualizações do dispositivo**.

    ![Screenshot da lâmina de Definições com a opção de atualização do dispositivo chamada.](./media/storsimple-8000-install-update-51/update2.png)

3. Uma notificação aparece se novas atualizações estiverem disponíveis. Em alternativa, na lâmina de atualização do **dispositivo,** clique em **'Actualizar' 'Scan's**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.

    ![A screenshot da lâmina de Definições com a opção de atualizações do Dispositivo chamada e a lâmina de atualização do dispositivo com as novas atualizações regulares estão disponíveis mensagens chamadas.](./media/storsimple-8000-install-update-51/update3.png)

4. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo. Para aplicar atualizações, clique **em Instalar atualizações**. Na lâmina **de atualizações regulares Confirm,** reveja os pré-requisitos para completar antes de aplicar atualizações. Selecione a caixa de verificação para indicar que está pronto para atualizar o dispositivo e, em seguida, clique em **Instalar**.

    ![Screenshot do dispositivo atualiza a lâmina com a opção de atualizações de instalação chamada e as atualizações regulares confirmam as atualizações regulares com a opção "Acordar" e a opção de instalação chamada.](./media/storsimple-8000-install-update-51/update4.png)

5. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.

     A atualização é descarregada e instalada apenas se todas as verificações forem concluídas com sucesso. Será notificado quando as verificações estiverem em curso. Se os pré-verificações falharem, serão fornecidos os motivos do fracasso. Resolva estas questões e, em seguida, re-tentar a operação. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.

7. Após a conclusão dos pré-testes com sucesso, é criado um trabalho de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Screenshot da Notificação que diz: "Iniciar o trabalho de atualizações de software."](./media/storsimple-8000-install-update-51/update6.png)
   
    A atualização é então aplicada ao seu dispositivo.

9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.

    ![Screenshot do dispositivo atualiza a lâmina com o Download e instalação de atualizações de software em curso chamadas e a lâmina de atualizações de instalação.](./media/storsimple-8000-install-update-51/update8.png)

     Também pode monitorizar o progresso do trabalho de atualização a partir das **definições** do Dispositivo > Jobs . Na lâmina **Jobs,** pode ver o progresso da atualização.

     ![Screenshot da lâmina De Definições com Jobs chamada e a lâmina Jobs mostrando o progresso da atualização.](./media/storsimple-8000-install-update-51/update7.png)

10. Depois de concluído o trabalho, navegue para as definições do **dispositivo > atualizações do dispositivo**. A versão do software deve agora ser atualizada.


Verifique se o seu dispositivo está a executar **StorSimple 8000 Series Update 5.1 (6.3.9600.17885)**. A **última data atualizada** deve ser alterada.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [versão Update 5.1](storsimple-update51-release-notes.md).
