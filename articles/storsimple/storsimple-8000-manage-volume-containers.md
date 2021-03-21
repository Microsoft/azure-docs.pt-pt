---
title: Gerir recipientes de volume para dispositivos da série StorSimple 8000
description: Explica como pode utilizar a página de volume de serviço do Gestor de Dispositivos StorSimple para adicionar, modificar ou eliminar um recipiente de volume.
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
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545396"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Use the StorSimple Device Manager service to manage StorSimple volume containers (Utilizar o serviço Gestor de Dispositivos do StorSimple para gerir contentores de volumes do StorSimple)

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir recipientes de volume StorSimple.

Um recipiente de volume num dispositivo Microsoft Azure StorSimple contém um ou mais volumes que partilham as definições de armazenamento, encriptação e consumo de largura de banda. Um dispositivo pode ter vários recipientes de volume para todos os seus volumes. 

Um recipiente de volume tem os seguintes atributos:

* **Volumes** – Os volumes StorSimple, nivelados ou fixados localmente, contidos no recipiente de volume. 
* **Encriptação** – Uma chave de encriptação que pode ser definida para cada recipiente de volume. Esta chave é utilizada para encriptar os dados enviados do seu dispositivo StorSimple para a nuvem. Uma tecla AES-256 de nível militar é utilizada com a chave inserida no utilizador. Para proteger os seus dados, recomendamos que ative sempre a encriptação de armazenamento na nuvem.
* **Conta de armazenamento** – A conta de armazenamento Azure que é usada para armazenar os dados. Todos os volumes residentes num recipiente de volume partilham esta conta de armazenamento. Pode escolher uma conta de armazenamento a partir de uma lista existente ou criar uma nova conta quando criar o recipiente de volume e, em seguida, especificar as credenciais de acesso para essa conta.
* **Largura de banda em nuvem** – A largura de banda consumida pelo dispositivo quando os dados do dispositivo estão a ser enviados para a nuvem. Pode impor um controlo de largura de banda especificando um valor entre 1 Mbps e 1.000 Mbps quando criar este recipiente. Se pretender que o dispositivo consuma toda a largura de banda disponível, desaponsee este campo a **Ilimitado**. Também pode criar e aplicar um modelo de largura de banda para alocar largura de banda com base no horário.

Os seguintes procedimentos explicam como utilizar a lâmina dos recipientes de volume StorSimple para completar as seguintes operações **comuns:**

* Adicione um recipiente de volume
* Modificar um recipiente de volume
* Apagar um recipiente de volume

## <a name="add-a-volume-container"></a>Adicione um recipiente de volume
Execute os seguintes passos para adicionar um recipiente de volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um recipiente de volume
Execute os seguintes passos para modificar um recipiente de volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Apagar um recipiente de volume
Um recipiente de volume tem volumes dentro dele. Só pode ser eliminado se todos os volumes nele contidos forem eliminados pela primeira vez. Execute os seguintes passos para eliminar um recipiente de volume.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a gestão de volumes StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

