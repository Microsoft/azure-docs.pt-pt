---
title: Gerir políticas de backup da série StorSimple 8000 Microsoft Docs
description: Explica como pode utilizar o serviço StorSimple Device Manager para criar e gerir cópias de segurança manuais, horários de backup e retenção de backup num dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515634"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Utilize o serviço StorSimple Device Manager no portal Azure para gerir políticas de backup


## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar a lâmina de **segurança** do serviço StorSimple Device Manager para controlar os processos de backup e a retenção de backup para os seus volumes StorSimple. Também descreve como completar uma cópia de segurança manual.

Quando retrossilho de um volume, pode optar por criar uma imagem local ou uma imagem em nuvem. Se estiver a fazer backup de um volume fixado localmente, recomendamos que especifique uma imagem em nuvem. Tirar um grande número de fotos locais de um volume fixado localmente juntamente com um conjunto de dados que tem muito churn resultará numa situação em que você poderia rapidamente ficar sem espaço local. Se optar por tirar fotografias locais, recomendamos que tire menos fotos diárias para fazer o apoio ao estado mais recente, retenha-as durante um dia e, em seguida, elimine-as.

Quando se tira uma imagem em nuvem de um volume fixado localmente, copia apenas os dados alterados para a nuvem, onde é desduplicado e comprimido.

## <a name="the-backup-policy-blade"></a>A lâmina de política de reserva

A lâmina **de política de backup** para o seu dispositivo StorSimple permite-lhe gerir políticas de backup e agendar instantâneos locais e em nuvem. As políticas de backup são usadas para configurar horários de backup e retenção de backup para uma coleção de volumes. As políticas de backup permitem-lhe tirar uma fotografia de vários volumes simultaneamente. Isto significa que as cópias de segurança criadas por uma política de backup serão cópias consistentes.

A listagem tabular de políticas de backup também permite filtrar as políticas de backup existentes por um ou mais dos seguintes campos:

* **Nome da política** – O nome associado à política. Os diferentes tipos de políticas incluem:

  * Políticas programadas, que são explicitamente criadas pelo utilizador.
  * Políticas importadas, que foram originalmente criadas no StorSimple Snapshot Manager. Estes têm uma etiqueta que descreve o anfitrião do StorSimple Snapshot Manager de que as políticas foram importadas.

  > [!NOTE]
  > As políticas de backup automática ou predefinida já não estão ativadas no momento da criação de volume.

* **Último backup bem sucedido** – A data e hora do último backup bem sucedido que foi levado com esta política.

* **Próximo backup** – A data e hora do próximo backup agendado que será iniciado por esta política.

* **Volumes** – Os volumes associados à política. Todos os volumes associados a uma política de backup são agrupados quando são criados backups.

* **Horários** – O número de horários associados à política de backup.

As operações frequentemente utilizadas que pode executar para políticas de backup são:

* Adicionar uma política de cópias de segurança
* Adicionar ou modificar um horário
* Adicione ou remova um volume
* Excluir uma política de backup
* Pegue uma cópia de segurança manual

## <a name="add-a-backup-policy"></a>Adicionar uma política de cópias de segurança

Adicione uma política de backup para agendar automaticamente as suas cópias de segurança. Quando cria um volume pela primeira vez, não existe uma política de backup padrão associada ao seu volume. É necessário adicionar e atribuir uma política de backup para proteger os dados de volume.

Execute os seguintes passos no portal Azure para adicionar uma política de backup para o seu dispositivo StorSimple. Depois de adicionar a apólice, pode definir um horário (ver [Adicionar ou modificar um horário).](#add-or-modify-a-schedule)

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar um horário

Pode adicionar ou modificar um calendário que esteja ligado a uma política de backup existente no seu dispositivo StorSimple. Execute os seguintes passos no portal Azure para adicionar ou modificar um horário.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Adicione ou remova um volume

Pode adicionar ou remover um volume atribuído a uma política de backup no seu dispositivo StorSimple. Execute os seguintes passos no portal Azure para adicionar ou remover um volume.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Excluir uma política de backup

Execute os seguintes passos no portal Azure para eliminar uma política de backup no seu dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Pegue uma cópia de segurança manual

Execute os seguintes passos no portal Azure para criar uma cópia de segurança (manual) a pedido para um único volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

