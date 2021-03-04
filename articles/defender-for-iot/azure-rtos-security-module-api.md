---
title: API do Módulo de Segurança para Azure RTOS
description: API de referência para o Módulo de Segurança para Azure RTOS.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120067"
---
# <a name="security-module-for-azure-rtos-api"></a>API do Módulo de Segurança para Azure RTOS 

Esta API destina-se a ser utilizada apenas com o Módulo de Segurança para Azure RTOS. Para obter recursos adicionais, consulte o [Módulo de Segurança para o recurso Azure RTOS GitHub](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Ativar módulo de segurança para Azure RTOS

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Esta rotina permite o subsistema do Módulo de Segurança Azure IoT. Uma máquina interna do Estado gere a recolha de eventos de segurança e envia-os para o Azure IoT Hub. Apenas uma NX_AZURE_IOT_SECURITY_MODULE instância é necessária e necessária para gerir a recolha de dados.

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

## <a name="disable-azure-iot-security-module"></a>Desativar módulo de segurança Azure IoT

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Esta rotina desativa o subsistema do Módulo de Segurança Azure IoT.

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

Para saber mais sobre como começar com o Módulo de Segurança Azure RTOS, consulte os seguintes artigos:

- Reveja a [visão geral](iot-security-azure-rtos.md)do módulo de segurança IoT RTOS .