---
title: Defender-IoT-micro-agente para Azure RTOS API
description: API de referência para o Defender-IoT-micro-agente para Azure RTOS.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779397"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-micro-agente para Azure RTOS API (pré-visualização)

Esta API destina-se a ser utilizada apenas com o defender-ioT-micro-agente para o Azure RTOS. Para obter recursos adicionais, consulte o [recurso Defender-IoT-micro-agente para o recurso Azure RTOS GitHub](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Ativar defender-ioT-micro-agente para Azure RTOS

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Esta rotina permite o subsistema Azure IoT Defender-IoT-micro-agente. Uma máquina interna do Estado gere a recolha de eventos de segurança e envia-os para o Azure IoT Hub. Apenas uma NX_AZURE_IOT_SECURITY_MODULE instância é necessária e necessária para gerir a recolha de dados.

### <a name="parameters"></a>Parâmetros

| Nome | Descrição |
|---------|---------|
| nx_azure_iot_ptr [in]    | Um ponteiro para um `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Valores de retorno

|Valores de retorno  |Descrição |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Módulo de Segurança Azure IoT habilitado com sucesso.     |
|NX_AZURE_IOT_FAILURE   |  Falhou em ativar o Módulo de Segurança Azure IoT devido a um erro interno.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  O módulo de segurança requer uma #NX_AZURE_IOT de ocorrência válida.      |

### <a name="allowed-from"></a>Permitido a partir de

Fios

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Desativar Azure IoT Defender-IoT-micro-agente

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Esta rotina desativa o subsistema Azure IoT Defender-IoT-micro-agente.

### <a name="parameters"></a>Parâmetros

| Nome | Descrição |
|---------|---------|
| nx_azure_iot_ptr [in]    | Um ponteiro para `NX_AZURE_IOT` . Se a instância de singleton desativada. |

### <a name="return-values"></a>Valores de retorno

|Valores de retorno  |Descrição |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Bem sucedido quando o Módulo de Segurança Azure IoT for desativado com sucesso.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  A exemplo do Azure IoT Hub é diferente da instância composta singleton.       |
|NX_AZURE_IOT_FAILURE    |  Falhou em desativar o Módulo de Segurança Azure IoT devido a um erro interno.       |

### <a name="allowed-from"></a>Permitido a partir de

Fios


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como começar com a Azure RTOS Defender-IoT-micro-agent, consulte os seguintes artigos:

- Reveja o Defender para IoT RTOS Defender-IoT-micro-agente [.](iot-security-azure-rtos.md)
