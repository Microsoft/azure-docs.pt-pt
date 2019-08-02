---
title: Entender a central de segurança do Azure para obter recomendações de segurança de IoT | Microsoft Docs
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas na central de segurança do Azure para IoT.
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
ms.openlocfilehash: 6f1efa365b4f3a1ab65cc89c84af57c88325e9d2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597279"
---
# <a name="security-recommendations"></a>Recomendações de segurança

A central de segurança do Azure para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque. As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações que podem ser disparadas em seus dispositivos de Hub IoT e/ou IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

As recomendações de dispositivo fornecem ideias e sugestões para melhorar a postura de segurança do dispositivo. 

| Severity | Nome                                                      | Origem de Dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Abrir portas no dispositivo                                      | Agente       | Um ponto de extremidade de escuta foi encontrado no dispositivo.                                                                                                                                                        |
| Médio   | Política de firewall permissiva encontrada em uma das cadeias. | Agente       | Política de firewall permitida encontrada (entrada/saída). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Médio   | A regra de firewall permissiva na cadeia de entrada foi encontrada     | Agente       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas.                                                                                    |
| Médio   | A regra de firewall permissiva na cadeia de saída foi encontrada    | Agente       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas.                                                                                   |
| Médio   | Falha na validação da linha de base do sistema operacional           | Agente       | O dispositivo não está em conformidade com os [benchmarks Linux do CIS](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

As recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Severity | Nome                                    | Origem de Dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixa      | O Agent envia mensagens não utilizadas          | Agente       | 10% ou mais de mensagens de segurança foram menores que 4 KB durante as últimas 24 horas.  |
| Baixa      | Configuração de configurações de segurança não ideal | Agente       | A configuração de configurações de segurança não é ideal.                                        |
| Baixa      | Conflito de configuração de configurações de segurança    | Agente       | Os conflitos foram identificados na configuração de configurações de segurança. |                          |


## <a name="recommendations-for-iot-hub"></a>Recomendações para o Hub IoT

Alertas de recomendação fornecem Insight e sugestões para ações para melhorar a postura de segurança do seu ambiente.  

| Severity | Nome                                                     | Origem de Dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticas usadas por vários dispositivos | IoT Hub     | As credenciais de autenticação do Hub IoT são usadas por vários dispositivos. Isso pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. O uso de credenciais duplicadas aumenta o risco de representação do dispositivo por um ator mal-intencionado. |
| Médio   | A política de filtro IP padrão deve ser Deny                  | IoT Hub     | A configuração do filtro IP deve ter regras definidas para tráfego permitido e, por padrão, negar todos os outros tráfego por padrão.                                                                                                     |
| Médio   | A regra de filtro IP inclui um intervalo IP grande                   | IoT Hub     | Um intervalo de IP de origem de regra de filtro IP de permissão é muito grande. Regras excessivamente permissivas podem expor seu hub IoT para atores mal-intencionados.                                                                                       |
| Baixa      | Ativar os registos de diagnóstico no Hub IoT                       | IoT Hub     | Habilite os logs e mantenha-os por até um ano. A retenção de logs permite recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida.                                       |
|

## <a name="next-steps"></a>Passos Seguintes

- [Visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)