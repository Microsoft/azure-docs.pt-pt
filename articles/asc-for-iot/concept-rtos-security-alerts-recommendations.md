---
title: Módulo de Segurança para Azure RTOS incorporado & alertas e recomendações personalizáveis
description: Saiba mais sobre alertas de segurança e remediação recomendada utilizando o Módulo de Segurança Azure IoT -RTOS.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ebb4edf10433cce981b4718835676d0a5d76ba40
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514948"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Módulo de segurança para alertas e recomendações de segurança Azure RTOS (pré-visualização)

O Módulo de Segurança para O Azure RTOS analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para potenciais atividades maliciosas e modificações suspeitas do sistema. Também pode criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo e das linhas de base.

Um módulo de segurança para o alerta de RTOS Azure funciona como um indicador de potencial compromisso, e deve ser investigado e remediado. Um módulo de segurança para a recomendação de Azure RTOS identifica a postura de segurança fraca para ser remediada e atualizada. 

Neste artigo, encontrará uma lista de alertas e recomendações incorporados que são desencadeados com base nas gamas padrão, e personalizáveis com os seus próprios valores, com base no comportamento esperado ou de base. 

Para obter mais informações sobre como funciona a personalização de alerta no Centro de Segurança Azure para o serviço IoT, consulte [alertas personalizáveis.](concept-customizable-security-alerts.md) Os alertas e recomendações específicos disponíveis para personalização ao utilizar o Módulo de Segurança para Azure RTOS são detalhados nas seguintes tabelas. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Módulo de Segurança para Azure RTOS suportado alertas de segurança

### <a name="device-related-security-alerts"></a>Alertas de segurança relacionados com dispositivos

|Atividade de alerta de segurança relacionada com dispositivo  |Nome do alerta  |
|---------|---------|
|Endereço IP| Comunicação com um endereço IP suspeito detetado|
|Impressão digital do certificado do dispositivo X.509|Desajuste de impressão digital do certificado do dispositivo X.509|
|Certificado X.509| Certificado X.509 expirado|
|Ficha SAS| Ficha SAS expirada|
|Ficha SAS| Assinatura token INVálida SAS|

### <a name="iot-hub-related-security-alerts"></a>Alertas de segurança relacionados com o IoT Hub

|Atividade de alerta de segurança IoT Hub  |Nome do alerta  |
|---------|---------|
|Adicione um certificado    |  Detetada tentativa falhada de adicionar um certificado a um Hub IoT       |
|Adição ou edição de uma definição de diagnóstico    | Detetou uma tentativa de adicionar ou editar uma definição de diagnóstico de um Hub IoT      |
|Apagar um certificado    |  Detetada tentativa falhada de apagar um certificado de um Hub IoT       |
|Excluir uma definição de diagnóstico    |  Detetada tentativa de apagar uma definição de diagnóstico de um hub IoT      |
|Certificado eliminado    | Supressão detetada de um certificado de um hub IoT        |
|Novo certificado     |  Detetado adição de novo certificado a um Hub IoT       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Módulo de Segurança para Azure RTOS suportado alertas personalizáveis

### <a name="device-related-customizable-alerts"></a>Alertas personalizáveis relacionados com dispositivo

|Atividade relacionada com dispositivo |Nome do alerta  |
|---------|---------|
|Ligações ativas|O número de ligações ativas não está na gama permitida|
|Mensagens de nuvem para dispositivo no protocolo **MQTT**|Número de mensagens de nuvem para dispositivo no protocolo **MQTT** não está na gama permitida|
|Ligação de saída| Ligação de saída a um IP que não é permitido|

### <a name="hub-related-customizable-alerts"></a>Alertas personalizáveis relacionados com o hub 

|Atividade relacionada com o hub  |Nome do alerta  |
|---------|---------|
|Purgas de fila de comando     |  Número de purgas de fila de comando fora do alcance permitido       |
|Mensagens de nuvem para dispositivo no protocolo **MQTT**    |  Número de mensagens cloud para dispositivo no protocolo **MQTT** fora do alcance permitido       |
|Dispositivo para cloud mensagens no protocolo **MQTT**    | Número de dispositivo para mensagens em nuvem no protocolo **MQTT** fora do alcance permitido        |
|O método direto invoca     |  Número de invocaçãos diretas fora do intervalo permitido       |
|Nuvem rejeitada para mensagens de dispositivo no protocolo **MQTT**     |   Número de mensagens rejeitadas para dispositivos no protocolo **MQTT** fora do intervalo permitido      |
|Atualizações para módulos duplos     |  Número de atualizações para módulos duplos fora da gama permitida       |
|Operações não autorizadas    |  Número de operações não autorizadas fora do alcance permitido       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Módulo de Segurança para Azure RTOS apoiou recomendações

### <a name="device-related-recommendations"></a>Recomendações relacionadas com dispositivos

|Atividade relacionada com dispositivos  |Nome da recomendação |
|---------|---------|
|Credenciais de autenticação    |  Credenciais de autenticação idênticas utilizadas por vários dispositivos       |

### <a name="hub-related-recommendations"></a>Recomendações relacionadas com o hub

|Atividade relacionada com o IoT Hub  |Nome da recomendação |
|---------|---------|
|Política de filtro IP   |  A política de filtro IP predefinido deve ser definida para **negar**  |
|Regra do filtro IP| A regra do filtro IP inclui uma grande gama de IP|
|Registos de diagnósticos|Sugestão para permitir registos de diagnóstico no IoT Hub|

### <a name="all-azure-security-center-for-iot-alerts-and-recommendations"></a>Todo o Centro de Segurança Azure para alertas e recomendações de IoT

Para obter uma lista completa de todos os alertas e recomendações relacionados com o serviço IoT, consulte [alertas](concept-security-alerts.md)de segurança IoT, [recomendações de](concept-recommendations.md)segurança IoT .

## <a name="next-steps"></a>Próximos passos

- [Quickstart: Módulo de segurança para Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Configure e personalize módulo de segurança para Azure RTOS](how-to-azure-rtos-security-module.md)
- Consulte o [Módulo de Segurança para Azure RTOS API](azure-rtos-security-module-api.md)