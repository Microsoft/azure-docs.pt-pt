---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "67184356"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Depois de o seu hub ter sido criado, recupere a cadeia de ligação para o centro. Isto é usado para ligar dispositivos e aplicações ao seu hub. 

1. Clique no seu hub para ver o painel IoT Hub com Definições, e assim por diante. Clique em **Políticas de acesso partilhado**.
   
2. Em **Políticas de acesso partilhado**, selecione a política **iothubowner**. 

3. Nas **teclas de acesso partilhadas,** copie a **cadeia De ligação , a chave primária** a ser usada mais tarde.

    ![Mostrar como recuperar a cadeia de ligação](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Para obter mais informações, veja [Controlo de acesso](../articles/iot-hub/iot-hub-devguide-security.md) no “Guia do programador do Hub IoT.”
