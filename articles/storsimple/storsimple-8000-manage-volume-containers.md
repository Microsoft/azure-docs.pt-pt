---
title: Gerir recipientes de volume para dispositivos da série StorSimple 8000
description: Explica como pode utilizar a página de recipientes de volume do Gestor de Dispositivos StorSimple para adicionar, modificar ou eliminar um recipiente de volume.
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
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467033"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Utilize o serviço StorSimple Device Manager para gerir recipientes de volume StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como usar o serviço StorSimple Device Manager para criar e gerir recipientes de volume StorSimple.

Um recipiente de volume num dispositivo Microsoft Azure StorSimple contém um ou mais volumes que partilham definições de armazenamento, encriptação e consumo de largura de banda. Um dispositivo pode ter vários recipientes de volume para todos os seus volumes. 

Um recipiente de volume tem os seguintes atributos:

* **Volumes** – Os volumes StorSimple nitentes ou fixados localmente que estão contidos no recipiente de volume. 
* **Encriptação** – Uma chave de encriptação que pode ser definida para cada recipiente de volume. Esta chave é utilizada para encriptar os dados enviados do seu dispositivo StorSimple para a nuvem. Uma tecla aes-256 de grau militar é usada com a chave inserida pelo utilizador. Para proteger os seus dados, recomendamos que ative sempre a encriptação de armazenamento em nuvem.
* **Conta** de armazenamento – A conta de armazenamento Azure que é utilizada para armazenar os dados. Todos os volumes residentes num contentor de volume partilham esta conta de armazenamento. Pode escolher uma conta de armazenamento a partir de uma lista existente, ou criar uma nova conta quando criar o recipiente de volume e, em seguida, especificar as credenciais de acesso para essa conta.
* **Largura de banda** da nuvem – A largura de banda consumida pelo dispositivo quando os dados do dispositivo estão a ser enviados para a nuvem. Pode impor um controlo de largura de banda especificando um valor entre 1 Mbps e 1.000 Mbps quando criar este recipiente. Se pretender que o dispositivo consuma toda a largura de banda disponível, defina este campo para **Ilimitado**. Também pode criar e aplicar um modelo de largura de banda para alocar largura de banda com base no horário.

Os seguintes procedimentos explicam como utilizar a lâmina dos **recipientes** StorSimple Volume para completar as seguintes operações comuns:

* Adicione um recipiente de volume
* Modificar um recipiente de volume
* Eliminar um recipiente de volume

## <a name="add-a-volume-container"></a>Adicione um recipiente de volume
Execute os seguintes passos para adicionar um recipiente de volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um recipiente de volume
Execute os seguintes passos para modificar um recipiente de volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminar um recipiente de volume
Um recipiente de volume tem volumes dentro dele. Só pode ser suprimido se todos os volumes nele contidos forem eliminados pela primeira vez. Execute os seguintes passos para eliminar um recipiente de volume.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a gestão de [volumes StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

