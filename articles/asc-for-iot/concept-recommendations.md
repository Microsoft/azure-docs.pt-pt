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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311550"
---
# <a name="security-recommendations"></a>Recomendações de segurança

O Azure Security Center for IoT digitaliza os seus recursos Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a sua superfície de ataque.
As recomendações de segurança são exequíveis e visam ajudar os clientes no cumprimento das melhores práticas de segurança.

Neste artigo, encontrará uma lista de recomendações que podem ser ativadas no seu Hub IoT e/ou dispositivos IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

As recomendações do dispositivo fornecem insights e sugestões para melhorar a postura de segurança do dispositivo.

| Gravidade | Name                                                      | Origem de Dados | Description                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Portas abertas no dispositivo                                      | Agente       | Foi encontrado um ponto de escuta no dispositivo .                                                                                                                                                        |
| Médio   | Política de firewall permissiva encontrada numa das correntes. | Agente       | Política de firewall permitida encontrada (INPUT/OUTPUT). A política de firewall deve negar todo o tráfego por defeito e definir regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Médio   | Foi encontrada regra de firewall permissiva na cadeia de entrada     | Agente       | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                    |
| Médio   | Foi encontrada regra de firewall permissiva na cadeia de saída    | Agente       | Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                   |
| Médio   | A validação da linha de base do sistema operativo falhou           | Agente       | O dispositivo não está em conformidade com as [referências do CIS Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

As recomendações operacionais fornecem insights e sugestões para melhorar a configuração do agente de segurança.

| Gravidade | Name                                    | Origem de Dados | Description                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixa      | Agente envia mensagens não utetilizadas          | Agente       | 10% ou mais de mensagens de segurança foram menores do que 4 KB durante as últimas 24 horas.  |
| Baixa      | Configuração de twin de segurança não é ideal | Agente       | A configuração de dois de segurança não é a ideal.                                        |
| Baixa      | Conflito de configuração de dupla segurança    | Agente       | Os conflitos foram identificados na configuração de dois de segurança. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Recomendações para ioT Hub

Os alertas de recomendação fornecem insights e sugestões para ações para melhorar a postura de segurança do seu ambiente.

| Gravidade | Name                                                     | Origem de Dados | Description                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticas utilizadas por vários dispositivos | Hub IoT     | As credenciais de autenticação IoT Hub são utilizadas por vários dispositivos. Isto pode indicar um dispositivo ilegítimo que se faz passar por um dispositivo legítimo. O uso de credencial duplicada aumenta o risco de imitação de dispositivo por um ator mal-intencionado. |
| Médio   | A política de filtro IP predefinido deve ser negada                  | Hub IoT     | A configuração do filtro IP deve ter regras definidas para tráfego permitido, e deve, por padrão, negar todo o tráfego por padrão.                                                                                                     |
| Médio   | A regra do filtro IP inclui grande gama de IP                   | Hub IoT     | Um intervalo IP de fonte de fonte de filtro IP permite que seja demasiado grande. Regras excessivamente permissivas podem expor o seu hub IoT a atores mal-intencionados.                                                                                       |
| Baixa      | Ativar registos de diagnóstico no IoT Hub                       | Hub IoT     | Ativar os registos e retê-los por um ano. A retenção de registos permite-lhe recriar pistas de atividade para fins de investigação quando ocorre um incidente de segurança ou se a sua rede está comprometida.                                       |
|

## <a name="next-steps"></a>Passos seguintes

- Centro de Segurança Azure para [visão geral](overview.md) do serviço IoT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
