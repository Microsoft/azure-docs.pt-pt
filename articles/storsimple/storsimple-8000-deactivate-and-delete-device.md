---
title: Desative e apague um dispositivo da série StorSimple 8000 [ StorSimple 8000] Microsoft Docs
description: Descreve como remover o dispositivo StorSimple do serviço, desativando-o primeiro e, em seguida, eliminando-o.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481980"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e eliminar um dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve como desativar e eliminar um dispositivo StorSimple que está ligado a um serviço StorSimple Device Manager. A orientação neste artigo aplica-se apenas aos dispositivos da série StorSimple 8000, incluindo os Eletrodomésticos StorSimple Cloud. Se estiver a utilizar um Matriz Virtual StorSimple, vá então para [Desativar e eliminar um StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

A desativação corta a ligação entre o dispositivo e o serviço correspondente do Gestor de Dispositivos StorSimple. Pode desejar tirar um dispositivo StorSimple de serviço (por exemplo, se estiver a substituir ou a atualizar o seu dispositivo ou se já não estiver a utilizar o StorSimple). Em caso afirmativo, tem de desativar o dispositivo antes de o poder eliminar.

Quando desativa um dispositivo, os dados armazenados localmente no dispositivo já não são acessíveis. Só os dados associados ao dispositivo que foi armazenado na nuvem podem ser recuperados.

> [!WARNING]
> A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registado no serviço StorSimple Device Manager a menos que seja reiniciado para as predefinições da fábrica.
>
> O processo de reset da fábrica elimina todos os dados armazenados localmente no seu dispositivo. Por isso, deve tirar uma imagem em nuvem de todos os seus dados antes de desativar um dispositivo. Este instantâneo de nuvem permite-lhe recuperar todos os dados numa fase posterior.

Depois de ler este tutorial, poderá:

* Desative um dispositivo e elimine os dados.
* Desative um dispositivo e retenha os dados.

> [!NOTE]
> Antes de desativar um dispositivo físico StorSimple ou um aparelho em nuvem, pare ou elimine clientes e anfitriões que dependam desse dispositivo.


## <a name="deactivate-and-delete-data"></a>Desativar e eliminar dados

Se estiver interessado em apagar completamente o dispositivo e não pretender reter os dados do dispositivo, complete os seguintes passos.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados

1. Antes de desativar um dispositivo, deve eliminar todos os recipientes de volume (e os volumes) associados ao dispositivo. Só pode eliminar os recipientes de volume depois de ter eliminado as cópias de segurança associadas.

    > [!NOTE]
    > Antes de desativar um dispositivo físico StorSimple ou um aparelho em nuvem, certifique-se de que os dados do recipiente de volume eliminado são efetivamente eliminados do dispositivo. Pode monitorizar as tabelas de consumo de nuvem e quando vir a queda de utilização da nuvem por causa das cópias de segurança que eliminou, pode proceder à desativação do dispositivo. Se desativar o dispositivo antes desta queda ocorrer, os dados ficam encalhados na conta de armazenamento e acumulam encargos.

2. Desativar o dispositivo da seguinte forma:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **do Dispositivo,** selecione o dispositivo que pretende desativar, clique à direita e, em seguida, clique em **Desativar**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na lâmina **Desativar,** digite o nome do dispositivo para confirmar e, em seguida, clique em **Desativar**. O processo de desativação começa e demora alguns minutos a ser concluído.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Após a desativação, pode eliminar completamente o dispositivo. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço já não pode gerir o dispositivo apagado. Utilize os seguintes passos para eliminar o dispositivo:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **do Dispositivo,** selecione o dispositivo desativado que pretende eliminar, clique à direita e, em seguida, clique em **Eliminar**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na lâmina **Eliminar,** escreva o nome do dispositivo para confirmar e, em seguida, clique em **Apagar**. A supressão leva alguns minutos para ser completada.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação concluída com sucesso, é notificado. A lista de dispositivos também atualiza para refletir a eliminação.

## <a name="deactivate-and-retain-data"></a>Desativar e reter dados

Se estiver interessado em apagar o dispositivo, mas quiser reter os dados, complete os seguintes passos:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e reter os dados
1. Desativar o dispositivo. Todos os recipientes de volume e as imagens do dispositivo permanecem.
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **do Dispositivo,** selecione o dispositivo que pretende desativar, clique à direita e, em seguida, clique em **Desativar**.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Na lâmina **Desativar,** digite o nome do dispositivo para confirmar e, em seguida, clique em **Desativar**. O processo de desativação começa e demora alguns minutos a ser concluído.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Pode agora falhar sobre os recipientes de volume e as imagens associadas. Para os procedimentos, vá ao [Failover e à recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Após a desativação e a falha, pode eliminar completamente o dispositivo. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço já não pode gerir o dispositivo apagado. Para eliminar o dispositivo, preencha os seguintes passos:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **do Dispositivo,** selecione o dispositivo desativado que pretende eliminar, clique à direita e, em seguida, clique em **Eliminar**.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Na lâmina **Eliminar,** escreva o nome do dispositivo para confirmar e, em seguida, clique em **Apagar**. A supressão leva alguns minutos para ser completada.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação concluída com sucesso, é notificado. A lista de dispositivos também atualiza para refletir a eliminação.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desative e apague um aparelho em nuvem

Para um StorSimple Cloud Appliance, a desativação do portal desafeta e elimina a máquina virtual, e os recursos criados quando foi provisionado. Depois de a aplicação da cloud ter sido desativada, não é possível restaurá-la para o estado anterior.

![Desative o aparelho de nuvem StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

A desativação resulta nas seguintes ações:

* O StorSimple Cloud Appliance é removido do serviço.
* A máquina virtual do StorSimple Cloud Appliance é eliminada.
* Os discos de osso e os discos de dados criados para o StorSimple Cloud Appliance são mantidos. Se não estiver a utilizar estas entidades, deve eliminá-las manualmente.
* O serviço hospedado e a Rede Virtual que foram criadas durante o fornecimento são mantidos. Se não estiver a utilizar estas entidades, deve eliminá-las manualmente.
* Os instantâneos em nuvem criados pelo StorSimple Cloud Appliance são mantidos.

Depois de o aparelho em nuvem ser desativado, pode eliminá-lo da lista de dispositivos. Selecione o dispositivo desativado, clique à direita e, em seguida, clique em **Eliminar**. O StorSimple notifica-o assim que o dispositivo é eliminado e a lista de atualizações dos dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para restaurar o dispositivo desativado às predefinições de fábrica, vá para [redefinir o dispositivo para as definições predefinidas](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)da fábrica .
* Para assistência técnica, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .
* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

