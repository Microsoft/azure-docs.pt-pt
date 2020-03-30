---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050417"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, cria-se um serviço back-end que adiciona propriedades desejadas a um dispositivo twin e, em seguida, questiona o registo de identidade para encontrar todos os dispositivos com propriedades reportadas que foram atualizados em conformidade. O seu serviço necessita da permissão de **ligação** do serviço para modificar as propriedades desejadas de um gémeo dispositivo, e precisa da permissão de leitura do **registo** para consultar o registo de identidade. Não existe uma política de acesso partilhado por defeito que contenha apenas estas duas permissões, pelo que precisa de criar uma.
