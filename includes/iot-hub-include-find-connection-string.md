---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146555"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Depois do seu hub tiver sido criado, obter a cadeia de ligação do hub. Isto é utilizado para ligar dispositivos e aplicações ao seu hub. 

1. Clique no seu hub para ver o painel do IoT Hub com as definições e assim por diante. Clique em **Políticas de acesso partilhado**.
   
2. Em **Políticas de acesso partilhado**, selecione a política **iothubowner**. 

3. Sob **chaves de acesso partilhado**, copie a **cadeia de ligação – chave primária** a ser utilizado mais tarde.

    ![Mostram como obter a cadeia de ligação](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Para obter mais informações, veja [Controlo de acesso](../articles/iot-hub/iot-hub-devguide-security.md) no “Guia do programador do Hub IoT.”
