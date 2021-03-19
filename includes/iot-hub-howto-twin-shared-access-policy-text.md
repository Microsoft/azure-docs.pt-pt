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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050417"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, cria-se um serviço back-end que adiciona propriedades desejadas a um dispositivo gémeo e, em seguida, consulta o registo de identidade para encontrar todos os dispositivos com propriedades relatadas que tenham sido atualizados em conformidade. O seu serviço necessita da permissão de **ligação** do serviço para modificar as propriedades desejadas de um dispositivo gémeo, e precisa da permissão **de leitura** do registo para consultar o registo de identidade. Não existe uma política de acesso partilhado por defeito que contenha apenas estas duas permissões, pelo que é necessário criar uma.
