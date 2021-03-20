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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "70049028"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Neste artigo, você cria um serviço back-end que agenda um trabalho para invocar um método direto em um dispositivo, agenda um trabalho para atualizar o dispositivo twin, e monitoriza o progresso de cada trabalho. Para realizar estas operações, o seu serviço necessita das permissões de leitura e **de inscrição** do **registo.** Por padrão, todos os hubs IoT são criados com uma política de acesso partilhado chamada **registryReadWrite** que concede estas permissões.
