---
title: Gerir políticas de cópia de segurança de série 8000 do StorSimple | Documentos da Microsoft
description: Explica como pode utilizar o serviço StorSimple Device Manager para criar e gerir cópias de segurança manuais, agendas de cópia de segurança e retenção de cópia de segurança num dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819068"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Utilizar o serviço StorSimple Device Manager no portal do Azure para gerir políticas de cópia de segurança


## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço de Gestor de dispositivos do StorSimple **política de cópia de segurança** painel para controlar os processos de cópia de segurança e retenção de cópia de segurança para os volumes do StorSimple. Também descreve como concluir uma cópia de segurança manual.

Quando cria cópias de segurança de um volume, pode optar por criar um instantâneo local ou um instantâneo de cloud. Se estiver fazendo o backup de um volume localmente afixado, recomendamos que especifica um instantâneo de cloud. Tendo um grande número de instantâneos locais de um volume afixado localmente, juntamente com um conjunto de dados que tem inúmeras alterações a dados resultará numa situação em que poderia rapidamente ficar sem espaço local. Se optar por colocar os instantâneos locais, recomendamos que menos instantâneos diários para cópia de segurança do estado mais recente, mantenha-los durante um dia e, em seguida, eliminá-los.

Quando pega um instantâneo de cloud de um volume afixado localmente, copiar apenas os dados alterados para a cloud, onde é com eliminação de duplicados e comprimido.

## <a name="the-backup-policy-blade"></a>O painel de política de cópia de segurança

O **política de cópia de segurança** painel para o dispositivo StorSimple permite-lhe gerir políticas de cópia de segurança e agendar locais e instantâneos da cloud. Políticas de cópia de segurança são utilizadas para configurar as agendas de cópia de segurança e retenção de cópia de segurança para uma coleção de volumes. Políticas de cópia de segurança permitem-lhe tirar um instantâneo de vários volumes em simultâneo. Isso significa que as cópias de segurança criadas por uma política de cópia de segurança será cópias consistentes de falhas.

Também o da lista tabular de políticas de cópia de segurança permite-lhe filtrar as políticas de cópia de segurança existentes por uma ou mais dos seguintes campos:

* **Nome da política** – o nome associado a política. Os diferentes tipos de políticas incluem:

  * Políticas agendadas, que são criadas explicitamente pelo utilizador.
  * Políticas importadas, que foram criadas originalmente no Snapshot Manager do StorSimple. Estes têm uma marca que descreve o anfitrião do Snapshot Manager do StorSimple que as políticas foram importadas de.

  > [!NOTE]
  > Políticas de cópia de segurança automática ou padrão já não estão ativadas no momento da criação do volume.

* **Última cópia de segurança bem-sucedida** – a data e hora da última com êxito cópia de segurança foi feita com esta política.

* **Cópia de segurança seguinte** – a data e hora do próximo backup agendado será iniciado por esta política.

* **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de cópia de segurança estão agrupados em conjunto, quando são criadas cópias de segurança.

* **Agendas** – o número de agendas associadas à política de cópia de segurança.

As operações utilizadas com frequência, que pode efetuar para políticas de cópia de segurança são:

* Adicionar uma política de cópias de segurança
* Adicionar ou modificar uma agenda
* Adicionar ou remover um volume
* Eliminar uma política de cópia de segurança
* Faça uma cópia de segurança manual

## <a name="add-a-backup-policy"></a>Adicionar uma política de cópias de segurança

Adicione uma política de cópia de segurança para agendar automaticamente as cópias de segurança. Quando cria pela primeira vez um volume, não existe nenhuma política de cópia de segurança padrão associada com o volume. Terá de adicionar e atribuir uma política de cópia de segurança para proteger os dados de volume.

Execute os seguintes passos no portal do Azure para adicionar uma política de cópia de segurança para o dispositivo StorSimple. Depois de adicionar a política, pode definir uma agenda (consulte [adicionar ou modificar uma agenda](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar uma agenda

Pode adicionar ou modificar uma agenda que está ligada a uma política de cópia de segurança existente no dispositivo StorSimple. Execute os seguintes passos no portal do Azure para adicionar ou modificar uma agenda.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Adicionar ou remover um volume

Pode adicionar ou remover um volume atribuído a uma política de cópia de segurança no dispositivo StorSimple. Execute os seguintes passos no portal do Azure para adicionar ou remover um volume.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança

Execute os seguintes passos no portal do Azure para eliminar uma política de cópia de segurança no dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Faça uma cópia de segurança manual

Execute os seguintes passos no portal do Azure para criar uma cópia de segurança a pedido (manual) de um único volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

