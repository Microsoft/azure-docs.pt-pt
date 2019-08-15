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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912460"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, você cria um serviço de back-end que adiciona as propriedades desejadas a um dispositivo e, em seguida, consulta o registro de identidade para localizar todos os dispositivos com propriedades relatadas que foram atualizadas de acordo. Seu serviço precisa da permissão de **conexão de serviço** para modificar as propriedades desejadas de um dispositivo e precisa da permissão de **leitura do registro** para consultar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas duas permissões, portanto, você precisa criar uma.
