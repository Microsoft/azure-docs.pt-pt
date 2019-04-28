---
title: Desativar e eliminar um dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Descreve como remover o dispositivo StorSimple de serviço ao primeiro a desativar e, em seguida, excluí-lo.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481980"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e eliminar um dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve como desativar e eliminar um dispositivo do StorSimple que está ligado a um serviço StorSimple Device Manager. As orientações neste artigo aplica-se apenas a dispositivos da série StorSimple 8000 incluindo a StorSimple Cloud Appliances. Se estiver a utilizar o StorSimple Virtual Array, em seguida, aceda a [desativar e eliminar um StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Desativação de servidores a ligação entre o dispositivo e o serviço StorSimple Device Manager correspondente. Pode querer tirar um dispositivo StorSimple fora de serviço (por exemplo, se estiver a substituir ou atualizar o seu dispositivo ou se já não estiver a utilizar o StorSimple). Nesse caso, terá de desativar o dispositivo antes de poder eliminá-lo.

Ao desativar um dispositivo, todos os dados que foi armazenados localmente no dispositivo já não estão acessíveis. Apenas os dados associados ao dispositivo que foram armazenado na cloud podem ser recuperados.

> [!WARNING]
> Desativação é uma operação permanente e não pode ser anulada. Um dispositivo desativado não pode ser registado com o serviço StorSimple Device Manager, a menos que este é reposto para as predefinições de fábrica.
>
> A fábrica de repor o processo elimina todos os dados que foi armazenados localmente no seu dispositivo. Por conseguinte, tem de colocar um instantâneo de cloud de todos os seus dados antes de desativar um dispositivo. Este instantâneo de cloud permite-lhe recuperar todos os dados num estágio posterior.

Depois de ler este tutorial, será capaz de:

* Desativar um dispositivo e eliminar os dados.
* Desativar um dispositivo e mantém os dados.

> [!NOTE]
> Antes de desativar um dispositivo físico StorSimple ou a aplicação da cloud, ou elimina os clientes e os anfitriões que dependem desse dispositivo.


## <a name="deactivate-and-delete-data"></a>Desativar e eliminar dados

Se estiver interessado em eliminar completamente o dispositivo e não pretende manter os dados no dispositivo, em seguida, conclua os passos seguintes.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados

1. Antes de desativar um dispositivo, tem de eliminar o volume de contentores (e os volumes) associados ao dispositivo. Pode eliminar contentores de volumes apenas depois de ter eliminado as cópias de segurança associadas.

    > [!NOTE]
    > Antes de desativar um dispositivo físico StorSimple ou a aplicação da cloud, certifique-se de que os dados a partir do contentor de volume eliminado, na verdade, são eliminados do dispositivo. Pode monitorizar os gráficos de consumo na cloud e ao ver a utilização da cloud drop devido a eliminou as cópias de segurança, em seguida, pode avançar para desativar o dispositivo. Se desativar o dispositivo antes desta operação de soltar ocorre, os dados são encalhados na conta de armazenamento e é acrescido encargos.

2. Desative o dispositivo da seguinte forma:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na **dispositivos** painel, selecione o dispositivo que pretende desativar, botão direito do mouse e clique em **desativar**.

        ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na **Deactivate** painel, escreva o nome de dispositivo para confirmar e clique em **desativar**. O processo de desativar é iniciado e demora alguns minutos a concluir.

        ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Após a desativação, pode eliminar completamente o dispositivo. A eliminar um dispositivo remove-o na lista de dispositivos ligados ao serviço. O serviço, em seguida, pode deixar de gerir o dispositivo eliminado. Utilize os seguintes passos para eliminar o dispositivo:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na **dispositivos** painel, selecione o dispositivo desativado que pretende eliminar, botão direito do mouse e clique em **eliminar**.

        ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na **elimine** painel, escreva o nome de dispositivo para confirmar e clique em **eliminar**. A eliminação demora alguns minutos a concluir.

        ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação foi concluída com êxito, será notificado. Também atualiza a lista de dispositivos para refletir a eliminação.

## <a name="deactivate-and-retain-data"></a>Desativar e manter os dados

Se estiver interessado em ao eliminar o dispositivo, mas pretende manter os dados, em seguida, conclua os seguintes passos:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e mantém os dados
1. Desative o dispositivo. Todos os contentores de volume e os instantâneos do dispositivo permanecem.
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na **dispositivos** painel, selecione o dispositivo que pretende desativar, botão direito do mouse e clique em **desativar**.

         ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na **Deactivate** painel, escreva o nome de dispositivo para confirmar e clique em **desativar**. O processo de desativar é iniciado e demora alguns minutos a concluir.

         ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Agora pode efetuar a ativação pós-falha dos contentores de volume e os instantâneos associados. Para obter os procedimentos, vá para [ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Após a desativação e a ativação pós-falha, pode eliminar completamente o dispositivo. A eliminar um dispositivo remove-o na lista de dispositivos ligados ao serviço. O serviço, em seguida, pode deixar de gerir o dispositivo eliminado. Para eliminar o dispositivo, conclua os seguintes passos:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na **dispositivos** painel, selecione o dispositivo desativado que pretende eliminar, botão direito do mouse e clique em **eliminar**.

       ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na **elimine** painel, escreva o nome de dispositivo para confirmar e clique em **eliminar**. A eliminação demora alguns minutos a concluir.

       ![Desativação do dispositivo do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação foi concluída com êxito, será notificado. Também atualiza a lista de dispositivos para refletir a eliminação.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desativar e eliminar uma aplicação da cloud

Para uma StorSimple Cloud Appliance, a desativação do portal desaloca e elimina a máquina virtual e os recursos criados no aprovisionamento. Depois de a aplicação da cloud ter sido desativada, não é possível restaurá-la para o estado anterior.

![Desativar aplicação da Cloud do StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Resultados da desativação nas seguintes ações:

* A StorSimple Cloud Appliance é removida do serviço.
* A máquina virtual para a StorSimple Cloud Appliance é eliminada.
* O disco do SO e discos de dados criados para a StorSimple Cloud Appliance são retidos. Se não estiver a utilizar estas entidades, deverá eliminá-los manualmente.
* O serviço alojado e a rede Virtual que foram criados durante o aprovisionamento são retidos. Se não estiver a utilizar estas entidades, deverá eliminá-los manualmente.
* Instantâneos de cloud criados pelo StorSimple Cloud Appliance são retidos.

Depois da aplicação da cloud é desativada, pode eliminá-la na lista de dispositivos. Selecione o dispositivo desativado, botão direito do mouse e clique em **eliminar**. StorSimple notifica assim que o dispositivo é eliminado e a lista de atualizações de dispositivos.

## <a name="next-steps"></a>Passos Seguintes

* Para restaurar o dispositivo desativado para as predefinições de fábrica, aceda a [repor o dispositivo para as predefinições de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obter assistência técnica, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

