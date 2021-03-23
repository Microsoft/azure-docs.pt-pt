---
title: Investigar recomendação de referência do CIS
description: Realizar investigações básicas e avançadas com base em recomendações de base do SISTEMA.
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 01ca6e1fecddff9800872a3e5495a5cac578a74f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782032"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Investigar a recomendação de base do SISTEMA (com base no referencial cis) 

Realizar investigações básicas e avançadas com base em recomendações de base do SISTEMA.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Investigação básica de recomendação de segurança de base do OS  

Pode investigar recomendações de base de OS navegando para o seu portal Azure Defender para IoT, sob o **IoT Hub**. Para mais informações, consulte como [investigar recomendações](quickstart-investigate-security-recommendations.md)de segurança.

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Investigação avançada de recomendação de segurança de base do SO  

Esta secção descreve como compreender melhor os resultados dos testes de base do OS e consultar eventos em Azure Log Analytics.  

A investigação avançada de recomendação de segurança de base osso só é suportada através da análise de registo. Ligue o Defender por IoT a um espaço de trabalho do Log Analytics antes de continuar. Para obter mais informações sobre recomendações avançadas de segurança de base do SISTEMA, consulte como [configurar o Azure Defender para a solução baseada em agentes IoT](how-to-configure-agent-based-solution.md).

Para consultar os seus eventos de segurança IoT no Log Analytics para obter alertas:

1. Navegue para a página **Alertas.**

1. **Selecione Investigar recomendações no espaço de trabalho Log Analytics**.

Para consultar os seus eventos de segurança IoT no Log Analytics para obter recomendações:

1. Navegue para a página **recomendações.**

1. **Selecione Investigar recomendações no espaço de trabalho Log Analytics**.

1. Selecione **Mostrar Sistema de Operação (OS) detalhes** da página de visualização rápida de detalhes da **Recomendação** para ver os detalhes de um dispositivo específico.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Consulte os detalhes de um dispositivo específico."::: 

Para consultar os seus eventos de segurança IoT no espaço de trabalho Log Analytics diretamente:

1. Navegue para a página **'Registos'.**

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Selecione os troncos do painel do lado esquerdo.":::

1. **Selecione Investigue os alertas** ou selecione os **alertas na** opção Log Analytics a partir de qualquer recomendação de segurança ou alerta.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Consultas úteis para investigar os recursos de base do SISTEMA: 

> [!Note]
> Certifique-se de que substitui `<device-id>` com o(s) nome(s) que deu o seu dispositivo em cada uma das seguintes consultas. 


### <a name="retrieve-the-latest-information"></a>Recuperar as últimas informações

- **Falha na frota do dispositivo**: Executar a seguinte consulta para obter as informações mais recentes sobre verificações que falharam em toda a frota do dispositivo: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Falha específica** do dispositivo - Executar a seguinte consulta para recuperar as informações mais recentes sobre verificações que falharam num dispositivo específico:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Erro específico do dispositivo** - Executar esta consulta para obter as informações mais recentes sobre verificações que tenham um erro num dispositivo específico: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Lista de dispositivos de atualização para a frota de dispositivos que falhou uma verificação específica** - Execute esta consulta para recuperar a lista atualizada de dispositivos (em toda a frota do dispositivo) que falharam uma verificação específica:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Passos seguintes

[Investigue recomendações de segurança.](quickstart-investigate-security-recommendations.md)
 