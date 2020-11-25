---
title: Desativar e apagar um dispositivo da série StorSimple 8000 Microsoft Docs
description: Saiba como desativar e eliminar um dispositivo StorSimple que esteja ligado a um serviço StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017190"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e eliminar um dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve como desativar e eliminar um dispositivo StorSimple que está ligado a um serviço StorSimple Device Manager. A orientação deste artigo aplica-se apenas aos dispositivos da série StorSimple 8000, incluindo os StorSimple Cloud Appliances. Se estiver a utilizar uma Matriz Virtual StorSimple, vá para [desativar e elimine um StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

A desativação corta a ligação entre o dispositivo e o serviço de Gestor de Dispositivos StorSimple correspondente. Pode desejar retirar um dispositivo StorSimple de serviço (por exemplo, se estiver a substituir ou atualizar o seu dispositivo ou se já não estiver a utilizar o StorSimple). Em caso afirmativo, é necessário desativar o dispositivo antes de o poder eliminar.

Quando desativa um dispositivo, quaisquer dados que foram armazenados localmente no dispositivo já não estão acessíveis. Apenas os dados associados ao dispositivo que foi armazenado na nuvem podem ser recuperados.

> [!WARNING]
> A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registado com o serviço StorSimple Device Manager a menos que seja reposto para padrão de fábrica.
>
> O processo de reset da fábrica elimina todos os dados que foram armazenados localmente no seu dispositivo. Portanto, deve tirar uma imagem em nuvem de todos os seus dados antes de desativar um dispositivo. Esta imagem em nuvem permite-lhe recuperar todos os dados numa fase posterior.

> [!NOTE]
>
> - Antes de desativar um dispositivo físico StorSimple ou um aparelho em nuvem, certifique-se de que os dados do recipiente de volume eliminado são efetivamente eliminados do dispositivo. Pode monitorizar os gráficos de consumo de nuvem e quando vir a queda de utilização da nuvem por causa das cópias de segurança que apagou, poderá proceder à desativar o dispositivo. Se desativar o dispositivo antes desta queda ocorrer, os dados ficam retidos na conta de armazenamento e acumulam encargos.
>
> - Antes de desativar um dispositivo físico StorSimple ou um aparelho em nuvem, pare ou elimine clientes e anfitriões que dependam desse dispositivo.
>
> - Se a conta de armazenamento ou os contentores na conta de armazenamento associados aos recipientes de volume já forem eliminados antes de eliminar os dados do dispositivo, receberá um erro e poderá não conseguir apagar os dados. Recomendamos que elimine os dados do dispositivo antes de eliminar a Conta de Armazenamento ou os recipientes neles. No entanto, nesta situação, terá de proceder à desativação e eliminação do dispositivo, assumindo que os dados já estão removidos da conta de armazenamento.

Depois de ler este tutorial, poderá:

- Desativar um dispositivo e apagar os dados.
- Desativar um dispositivo e reter os dados.

## <a name="deactivate-and-delete-data"></a>Desativar e apagar dados

Se estiver interessado em eliminar completamente o dispositivo e não pretender reter os dados no dispositivo, então complete os seguintes passos.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados

1. Antes de desativar um dispositivo, deve eliminar todos os recipientes de volume (e os volumes) associados ao dispositivo. Só pode eliminar recipientes de volume depois de ter eliminado as cópias de segurança associadas. Consulte a nota na visão geral acima antes de desativar um dispositivo físico StorSimple ou um aparelho em nuvem.

2. Desativar o dispositivo da seguinte forma:

   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** selecione o dispositivo que deseja desativar, clicar com o botão direito e, em seguida, clicar em **Desativar**.

        ![Dispositivo StorSimple desativado](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na lâmina **Desativada, digite** o nome do dispositivo para confirmar e, em seguida, clique em **Desativar**. O processo de desativar começa e demora alguns minutos a ser concluído.

        ![Desativar o dispositivo StorSimple 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Após a desativação, pode eliminar completamente o dispositivo. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço não pode então gerir o dispositivo eliminado. Utilize os seguintes passos para eliminar o dispositivo:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** selecione o dispositivo desativado que pretende eliminar, clique com o botão direito e, em seguida, clique em **Eliminar**.

        ![Desativar o dispositivo StorSimple 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na lâmina **Eliminar,** digite o nome do dispositivo para confirmar e, em seguida, clique em **Eliminar**. A eliminação leva alguns minutos para ser concluída.

        ![Desativar o dispositivo StorSimple 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Depois de a eliminação estar concluída com sucesso, é notificado. A lista de dispositivos também atualiza para refletir a eliminação.

## <a name="deactivate-and-retain-data"></a>Desativar e reter dados

Se estiver interessado em eliminar o dispositivo mas quiser reter os dados, então complete os seguintes passos:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e reter os dados

1. Desativar o dispositivo. Todos os recipientes de volume e as imagens do dispositivo permanecem.
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** selecione o dispositivo que deseja desativar, clicar com o botão direito e, em seguida, clicar em **Desativar**.

         ![Desativar o dispositivo StorSimple 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na lâmina **Desativada, digite** o nome do dispositivo para confirmar e, em seguida, clique em **Desativar**. O processo de desativar começa e demora alguns minutos a ser concluído.

         ![Desativar o dispositivo StorSimple 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Pode agora falhar sobre os recipientes de volume e as imagens associadas. Para procedimentos, vá ao [Failover e à recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Após a desativação e falha, pode eliminar completamente o dispositivo. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço não pode então gerir o dispositivo eliminado. Para eliminar o dispositivo, complete os seguintes passos:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** selecione o dispositivo desativado que pretende eliminar, clique com o botão direito e, em seguida, clique em **Eliminar**.

       ![Desativar o dispositivo StorSimple 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na lâmina **Eliminar,** digite o nome do dispositivo para confirmar e, em seguida, clique em **Eliminar**. A eliminação leva alguns minutos para ser concluída.

       ![Desativar o dispositivo StorSimple 8](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Depois de a eliminação estar concluída com sucesso, é notificado. A lista de dispositivos também atualiza para refletir a eliminação.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desativar e eliminar uma aplicação da cloud

Para um StorSimple Cloud Appliance, a desativação do portal desativa e elimina a máquina virtual, bem como os recursos criados quando foi a provisionado. Depois de a aplicação da cloud ter sido desativada, não é possível restaurá-la para o estado anterior.

![Desativar o aparelho de nuvem StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

A desativação resulta nas seguintes ações:

* O aparelho StorSimple Cloud é removido do serviço.
* A máquina virtual para o aparelho de nuvem StorSimple é eliminada.
* O disco de so e os discos de dados criados para o StorSimple Cloud Appliance são mantidos. Se não estiver a utilizar estas entidades, deverá eliminá-las manualmente.
* O serviço hospedado e a Rede Virtual que foram criadas durante o fornecimento são mantidos. Se não estiver a utilizar estas entidades, deverá eliminá-las manualmente.
* As imagens em nuvem criadas pelo StorSimple Cloud Appliance são mantidas.

Depois de desativar o aparelho em nuvem, pode eliminá-lo da lista de dispositivos. Selecione o dispositivo desativado, clique à direita e, em seguida, clique em **Eliminar**. O StorSimple notifica-o assim que o dispositivo for eliminado e a lista de atualizações dos dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para restaurar o dispositivo desativado para as falhas de fábrica, vá [para redefinir o dispositivo para as definições predefinidos da fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para assistência técnica, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

