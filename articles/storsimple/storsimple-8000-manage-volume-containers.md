---
title: Gerir os seus contentores de volume do StorSimple no dispositivo StorSimple 8000 series | Documentos da Microsoft
description: Explica como pode utilizar a página de contentores de volume de serviço do StorSimple Device Manager para adicionar, modificar ou eliminar um contentor de volumes.
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
ms.openlocfilehash: 7e1a5ac2c2b734c77fc3dbe788206f8c75044953
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60724740"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Utilizar o serviço StorSimple Device Manager para gerir contentores de volumes do StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Device Manager para criar e gerir contentores de volumes do StorSimple.

Um contentor de volume num dispositivo do Microsoft Azure StorSimple contém um ou mais volumes que partilham a conta de armazenamento, encriptação e as definições de consumo de largura de banda. Um dispositivo pode ter vários contentores de volume para todos os seus volumes. 

Um contentor de volumes tem os seguintes atributos:

* **Volumes** – o camadas ou volumes do StorSimple que estão contidos dentro do contentor de volumes afixados localmente. 
* **Encriptação** – uma chave de encriptação que pode ser definida para cada contentor de volume. Esta chave é utilizada para encriptar os dados que são enviados do dispositivo StorSimple para a cloud. Uma chave do nível militar AES-256 bits é utilizada com a chave inseridos pelo usuário. Para proteger os dados, recomendamos que sempre ativar a encriptação de armazenamento da cloud.
* **Conta de armazenamento** – conta de armazenamento do Azure, que é utilizada para armazenar os dados. Todos os volumes que residem num contentor de volume partilham esta conta de armazenamento. Pode escolher uma conta de armazenamento a partir de uma lista existente ou criar uma nova conta ao criar o contentor de volumes e, em seguida, especifique as credenciais de acesso para essa conta.
* **Largura de banda da cloud** – a largura de banda consumida pelo dispositivo quando os dados do dispositivo estão a ser enviados para a cloud. Pode impor um controle de largura de banda ao especificar um valor entre 1 e 1000 Mbps, quando cria este contentor. Se pretender que o dispositivo consuma a largura de banda disponível, defina este campo para **ilimitado**. Também pode criar e aplicar um modelo de largura de banda para alocar a largura de banda com base numa agenda.

Os procedimentos seguintes explicam como utilizar o StorSimple **contentores de volumes** painel para concluir as seguintes operações comuns:

* Adicionar um contentor de volumes
* Modificar um contentor de volume
* Eliminar um contentor de volumes

## <a name="add-a-volume-container"></a>Adicionar um contentor de volumes
Execute os seguintes passos para adicionar um contentor de volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um contentor de volume
Execute os seguintes passos para modificar um contentor de volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminar um contentor de volumes
Um contentor de volumes tem volumes dentro do mesmo. Ele pode ser eliminado apenas se todos os volumes nele contidos são eliminados primeiro. Execute os passos seguintes para eliminar um contentor de volumes.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

