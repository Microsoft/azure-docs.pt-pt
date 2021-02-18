---
title: Recomendações de segurança para o IoT Hub
description: Conheça o conceito de recomendações de segurança e como são usadas no Defender para ioT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: 4ae479fcff0514b42d46aef455d8a32d6ebfaf23
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636548"
---
# <a name="security-recommendations-for-iot-hub"></a>Recomendações de segurança para o IoT Hub

O Defender for IoT digitaliza os seus recursos Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a sua superfície de ataque.
As recomendações de segurança são exequíveis e visam ajudar os clientes a cumprir as melhores práticas de segurança.

Neste artigo, encontrará uma lista de recomendações, que podem ser desencadeadas no seu IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Construído em recomendações no IoT Hub

Os alertas de recomendação fornecem insights e sugestões para ações para melhorar a postura de segurança do seu ambiente.

| Gravidade | Name | Origem de dados | Description |
|--|--|--|--|
| Alto | Credenciais de autenticação idênticas utilizadas por vários dispositivos | IoT Hub | As credenciais de autenticação IoT Hub são utilizadas por vários dispositivos. Este processo pode indicar um dispositivo ilegítimo que se faça passar por um dispositivo legítimo. O uso de credencial duplicada aumenta o risco de imitação de dispositivo por um ator mal-intencionado. |
| Médio | A política de filtro IP predefinido deve ser negada | IoT Hub | A configuração do filtro IP deve ter regras definidas para tráfego permitido, e deve, por padrão, negar todo o tráfego por padrão. |
| Médio | A regra do filtro IP inclui grande gama de IP | IoT Hub | Um intervalo IP de fonte de fonte de filtro IP permite que seja demasiado grande. Regras excessivamente permissivas podem expor o seu hub IoT a atores mal-intencionados. |
| Baixo | Ativar registos de diagnóstico no IoT Hub | IoT Hub | Ativar os registos e retê-los por um ano. A retenção de registos permite-lhe recriar pistas de atividade para fins de investigação quando ocorre um incidente de segurança ou se a sua rede está comprometida. |

## <a name="next-steps"></a>Passos seguintes

- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
