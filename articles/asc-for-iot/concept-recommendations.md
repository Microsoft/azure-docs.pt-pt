---
title: Recomendações de segurança
description: Conheça o conceito de recomendações de segurança e como são usadas no Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311550"
---
# <a name="security-recommendations"></a>Recomendações de segurança

O Azure Security Center for IoT verifica os seus recursos Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a sua superfície de ataque.
As recomendações de segurança são exequíveis e visam ajudar os clientes no cumprimento das melhores práticas de segurança.

Neste artigo, encontrará uma lista de recomendações que podem ser desencadeadas nos seus dispositivos IoT Hub e/ou IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

As recomendações do dispositivo fornecem insights e sugestões para melhorar a postura de segurança do dispositivo.

| Gravidade | Nome                                                      | Origem de Dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Portas abertas no dispositivo                                      | Agente       | Foi encontrado um ponto final de escuta no dispositivo .                                                                                                                                                        |
| Médio   | Política de firewall permissiva encontrada numa das correntes. | Agente       | Política de firewall permitida encontrada (INPUT/OUTPUT). A política de firewall deve negar todo o tráfego por defeito e definir regras que permitam a comunicação necessária de/para o dispositivo.                               |
| Médio   | Regra de firewall permissiva na cadeia de entrada foi encontrada     | Agente       | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                    |
| Médio   | Regra de firewall permissiva na cadeia de saída foi encontrada    | Agente       | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                   |
| Médio   | A validação da linha de base do sistema de funcionamento falhou           | Agente       | O dispositivo não cumpre os critérios de [referência do CIS Linux.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

Recomendações operacionais fornecem insights e sugestões para melhorar a configuração do agente de segurança.

| Gravidade | Nome                                    | Origem de Dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixa      | Agente envia mensagens não utilizadas          | Agente       | 10% ou mais das mensagens de segurança foram menores do que 4 KB durante as últimas 24 horas.  |
| Baixa      | Configuração de gémeos de segurança não é ideal | Agente       | A configuração de gémeos de segurança não é ótima.                                        |
| Baixa      | Conflito de configuração de dupla seleção de segurança    | Agente       | Os conflitos foram identificados na configuração gémea de segurança. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Recomendações para IoT Hub

Os alertas de recomendação fornecem insights e sugestões para ações para melhorar a postura de segurança do seu ambiente.

| Gravidade | Nome                                                     | Origem de Dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticas utilizadas por vários dispositivos | IoT Hub     | As credenciais de autenticação do IoT Hub são utilizadas por vários dispositivos. Isto pode indicar um dispositivo ilegítimo que se faz passar por um dispositivo legítimo. O uso de credenciais duplicados aumenta o risco de personificação do dispositivo por um ator malicioso. |
| Médio   | A política de filtro IP padrão deve ser negar                  | IoT Hub     | A configuração do filtro IP deve ter regras definidas para o tráfego permitido, e deve, por padrão, negar todo o tráfego por defeito.                                                                                                     |
| Médio   | A regra do filtro IP inclui grande gama IP                   | IoT Hub     | Uma gama IP de fonte de código de filtro IP de ido é demasiado grande. Regras demasiado permissivas podem expor o seu hub ioT a atores maliciosos.                                                                                       |
| Baixa      | Ativar registos de diagnóstico no IoT Hub                       | IoT Hub     | Ative os registos e retenha-os até um ano. A retenção de registos permite-lhe recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou a sua rede está comprometida.                                       |
|

## <a name="next-steps"></a>Passos seguintes

- Azure Security Center for IoT service [Overview](overview.md)
- Saiba como aceder aos [seus dados](how-to-security-data-access.md) de segurança
- Saiba mais sobre [investigar um dispositivo](how-to-investigate-device.md)
