---
title: Configure e personalize defender-ioT-micro-agente para Azure RTOS
description: Saiba como configurar e personalizar o seu Defender-IoT-micro-agente para a Azure RTOS.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778989"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Configure e personalize o defender-ioT-micro-agente para Azure RTOS (pré-visualização)

Este artigo descreve como configurar o defender-ioT-micro-agente para o seu dispositivo Azure RTOS, para satisfazer os requisitos de rede, largura de banda e memória.

Tem de selecionar um ficheiro de distribuição de alvos que tenha uma `*.dist` extensão do `netxduo/addons/azure_iot/azure_iot_security_module/configs` diretório.  

Ao utilizar um ambiente de compilação CMake, deve definir um parâmetro de linha de comando `IOT_SECURITY_MODULE_DIST_TARGET` para o valor escolhido. Por exemplo, `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

Num ambiente de compilação IAR ou outro não CMake, deve adicionar o `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` caminho a quaisquer caminhos incluídos conhecidos. Por exemplo, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Utilize o seguinte ficheiro para configurar o comportamento do seu dispositivo.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/ \<target distribution> /asc_config.h**

Num ambiente de compilação CMake, tem de alterar a configuração predefinidora editando o `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` ficheiro. Utilize o seguinte formato CMake, `set(ASC_XXX ON)` ou o seguinte ficheiro para todos os `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` outros ambientes. Por exemplo, `#define ASC_XXX`.

O comportamento predefinido de cada configuração é fornecido nas seguintes tabelas: 

## <a name="general"></a>Geral

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-iot-micro-agente | O identificador único do dispositivo.  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | Número | 3.2.1 | A versão. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Número  | 3 | O tempo que o Defender-IoT-micro-agente demorará a enviar a mensagem de segurança após uma falha. (em segundos) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Número | 300 | O Defender-IoT-micro-agente pendente (em segundos). O Estado mudará para suspender, se o tempo for ultrapassado. |

## <a name="collection"></a>Coleção

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Número  | 30  | Compensação do intervalo de recolha de arranque do Collector. Durante o arranque, o valor será adicionado à recolha do sistema de forma a evitar o envio de mensagens de vários dispositivos em simultâneo.  |
| ASC_HIGH_PRIORITY_INTERVAL | Número | 10 | O intervalo de grupo de alta prioridade do colecionador (em segundos). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Número | 30 | Intervalo de grupo de prioridade média do colecionador (em segundos). |
| ASC_LOW_PRIORITY_INTERVAL | Número | 145,440  | O intervalo de grupo de baixa prioridade do colecionador (em segundos). |

#### <a name="collector-network-activity"></a>Atividade da rede de colecionadores

Para personalizar a configuração de atividade da rede de colecionador, utilize o seguinte:

| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Booleano | false | Filtra a atividade da `TCP` rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Booleano | false | Filtra os eventos de atividade da `UDP` rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Booleano | false | Filtra os eventos de atividade da `ICMP` rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Booleano | true | Captura apenas os pacotes de entrada unicast. Quando definido como falso, também capturará tanto a Difusão como o Multicast. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Booleano  | false  | Envia um evento vazio de colecionador. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Número | 64 | O número máximo de eventos de rede IPv4 para armazenar na memória. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Número | 64  | O número máximo de eventos de rede IPv6 para armazenar na memória. |

### <a name="collectors"></a>Colecionadores
| Nome | Tipo | Predefinição | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Booleano | ON | Permite o colecionador de batimentos cardíacos. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Booleano | ON | Permite o coletor de atividades da rede. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Booleano | ON | Permite o colecionador de informação do sistema.  |

Outras configurações as bandeiras são avançadas e têm funcionalidades não suportadas. Contacte o suporte para alterar isto, ou para mais informações.
 
## <a name="supported-security-alerts-and-recommendations"></a>Alertas e recomendações de segurança apoiados

O Defender-IoT-micro-agente da Azure RTOS suporta alertas e recomendações de segurança específicas. Certifique-se de [rever e personalizar os valores de alerta e recomendação relevantes](concept-rtos-security-alerts-recommendations.md) para o seu serviço.

## <a name="log-analytics-optional"></a>Log Analytics (opcional)

Pode ativar e configurar o Log Analytics para investigar eventos e atividades do dispositivo. Leia sobre como configurar e use [o Log Analytics com o serviço Defender para ioT](how-to-security-data-access.md#log-analytics) para saber mais. 

## <a name="next-steps"></a>Passos seguintes


- Rever e personalizar o defender-ioT-micro-agente para [alertas e recomendações](concept-rtos-security-alerts-recommendations.md) de segurança Azure RTOS
- Consulte o [Defender-IoT-micro-agente para a API AZure RTOS,](azure-rtos-security-module-api.md) se necessário.
