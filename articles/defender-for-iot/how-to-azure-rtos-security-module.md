---
title: Configure e personalize módulo de segurança para Azure RTOS
description: Saiba como configurar e personalizar o seu Módulo de Segurança para Azure RTOS.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937777"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Configure e personalize módulo de segurança para Azure RTOS (pré-visualização)

Utilize este ficheiro seguinte para configurar o comportamento do seu dispositivo.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port.h

 O comportamento predefinido de cada configuração é fornecido nas seguintes tabelas: 

### <a name="general"></a>Geral

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Cadeia | --- | Identificador exclusivo do dispositivo  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Número | 300 | Módulo de segurança pendente em segundos. Se o tempo exceder a mudança de estado para suspender. |

#### <a name="collection"></a>Coleção

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Número | 10 | Colecionadores intervalo de grupo de alta prioridade em segundos. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Número | 30 | Intervalo de grupo de prioridade média dos colecionadores em segundos. |
| ASC_LOW_PRIORITY_INTERVAL | Número | 145,440  | Colecionadores baixo intervalo de grupo de prioridade em segundos. |

#### <a name="collector-network-activity"></a>Atividade da rede de colecionadores

Para personalizar a configuração de atividade da rede de colecionador, utilize o seguinte:

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Booleano | false | Atividade da rede de `TCP` filtros |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Booleano | false | Eventos de atividade da rede de `UDP` filtros |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Booleano | false | Eventos de atividade da rede de `ICMP` filtros |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Booleano | true | Capture apenas pacotes de entrada unicast, quando definidos para captura falsa também Difusão e Multicast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Número | 64 | Número máximo de eventos de rede IPv4 para armazenar na memória |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Número | 64  | Número máximo de eventos de rede IPv6 para armazenar na memória |


## <a name="compile-flags"></a>Compilar bandeiras
Compilar bandeiras permite-lhe anular as configurações predefinidas.

### <a name="collectors"></a>Colecionadores
| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| collector_heartbeat_enabled | Booleano | ON | Ativar o coletor de batimentos cardíacos |
| collector_network_activity_enabled | Booleano | ON | Ativar o coletor de atividades da rede |
| collector_system_information_enabled | Booleano | ON | Ativar o colecionador de informação do sistema |

## <a name="supported-security-alerts-and-recommendations"></a>Alertas e recomendações de segurança apoiados

O Módulo de Segurança do Azure RTOS suporta alertas e recomendações de segurança específicas. Certifique-se de [rever e personalizar os valores de alerta e recomendação relevantes](concept-rtos-security-alerts-recommendations.md) para o seu serviço.

## <a name="log-analytics-optional"></a>Log Analytics (opcional)

Embora opcional e não necessário, ativar e configurar o Log Analytics pode ser útil quando deseja investigar mais eventos e atividades do dispositivo. Leia sobre como configurar e utilizar [o Log Analytics com o serviço Defender para ioT](how-to-security-data-access.md#log-analytics) para saber mais. 

## <a name="next-steps"></a>Passos seguintes

- Rever e personalizar módulo de segurança para [alertas e recomendações de segurança](concept-rtos-security-alerts-recommendations.md) Azure RTOS
- Consulte o [Módulo de Segurança para Azure RTOS API,](azure-rtos-security-module-api.md) conforme necessário.

