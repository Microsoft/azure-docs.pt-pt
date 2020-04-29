---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049028"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Neste artigo, cria-se um serviço back-end que programa um trabalho para invocar um método direto num dispositivo, agenda um trabalho para atualizar o dispositivo twin, e monitoriza o progresso de cada trabalho. Para realizar estas operações, o seu serviço necessita da **leitura do registo** e do registo de permissões de **escrita.** Por padrão, cada hub IoT é criado com uma política de acesso partilhado chamada **registryReadWrite** que concede estas permissões.
