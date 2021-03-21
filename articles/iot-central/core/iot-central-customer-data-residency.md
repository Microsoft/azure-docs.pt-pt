---
title: Residência de dados do cliente na Azure IoT Central | Microsoft Docs
description: Este artigo descreve a residência de dados dos clientes nas aplicações Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280706"
---
# <a name="azure-iot-central-customer-data-residency"></a>Residência de dados do cliente Azure IoT Central

A IoT Central não armazena dados de clientes fora da geografia especificada pelo cliente, exceto para os seguintes cenários:

- Quando um novo utilizador é adicionado a uma aplicação IoT Central existente, o ID de e-mail do utilizador pode ser armazenado fora da geografia até que o utilizador convidado aceda à aplicação pela primeira vez.

- Os azulejos do mapa do painel central IoT utilizam [mapas Azure.](../../azure-maps/about-azure-maps.md) Quando adicionar um azulejo de mapa a uma aplicação IoT Central existente, os dados de localização podem ser processados ou armazenados de acordo com as regras de geolocalização do serviço Azure Maps.
