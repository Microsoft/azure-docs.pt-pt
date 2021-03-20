---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "76020838"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta de Armazenamento Azure ao IoT Hub

Como a aplicação do dispositivo simulado envia um ficheiro para uma bolha, deve ter uma conta [de Armazenamento Azure](../articles/storage/common/storage-account-create.md) associada ao seu hub IoT. Quando associa uma conta de Armazenamento Azure a um hub IoT, o hub IoT gera um SAS URI. Um dispositivo pode utilizar este SAS URI para carregar um ficheiro de forma segura para um recipiente de bolhas. O serviço IoT Hub e o dispositivo SDKs coordenam o processo que gera o SAS URI e disponibiliza-o a um dispositivo para utilizar para carregar um ficheiro.

Siga as instruções nos [uploads de ficheiros Configure utilizando o portal Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Certifique-se de que um recipiente blob está associado ao seu hub IoT e que as notificações de ficheiros estão ativadas.

![Ativar notificações de ficheiros no portal](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
