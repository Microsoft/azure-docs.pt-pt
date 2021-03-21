---
title: Compreenda a atualização do dispositivo para os recursos do Hub Azure IoT | Microsoft Docs
description: Compreender atualização do dispositivo para os recursos do Hub IoT Azure
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663643"
---
# <a name="device-update-resources"></a>Recursos de atualização de dispositivos

Para utilizar a Atualização do Dispositivo para o IoT Hub, é necessário criar uma conta de atualização do dispositivo e um recurso de instância. 

## <a name="device-update-account"></a>Conta de atualização de dispositivos

Uma conta de Atualização de Dispositivos é um recurso que é criado dentro da sua subscrição Azure. Ao nível da conta De Atualização de Dispositivos, pode selecionar a região onde a sua conta de Atualização de Dispositivos será criada. Também pode definir permissões para autorizar utilizadores que tenham acesso à Atualização do Dispositivo.


## <a name="device-update-instance"></a>Instância de atualização do dispositivo
Depois de ter sido criada uma conta, precisa de criar uma instância de Atualização de Dispositivos. Um caso é um recipiente lógico que contém atualizações e implementações associadas a um hub IoT específico. A Atualização do Dispositivo utiliza o hub IoT como diretório de dispositivos e um canal de comunicação com dispositivos. 

Durante a pré-visualização pública, duas contas de atualização do Dispositivo podem ser criadas por subscrição. Além disso, podem ser criadas duas instâncias de atualização de dispositivos por conta.

## <a name="configuring-device-update-linked-iot-hub"></a>Configurar atualização do dispositivo ligado ao hub IoT 

Para que a Atualização do Dispositivo receba notificações de alteração do IoT Hub, a Atualização do Dispositivo integra-se com o "Built-In" Event Hub. Clicar no botão "Configure IoT Hub" dentro do seu caso configura as rotas de mensagens necessárias e a política de acesso necessária para comunicar com dispositivos IoT. 

As seguintes rotas de mensagens são configuradas para a atualização do dispositivo:

|   Nome da rota    | Consulta de encaminhamento  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Escutas para eventos de mudanças digitais de gémeos  |
|  DeviceUpdate.DeviceLifeCycle | opType = 'deleteDeviceIdentity'  | Escutas para dispositivos que foram eliminados |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-id = "urn:azureiot:ModelDiscovery:ModelInformation:1 | Ouve novos tipos de dispositivos |
|  DeviceUpdate.DeviceTwinEvents| (opType = 'updateTwin' OR opType = 'replaceTwin') E IS_DEFINED($body.tags.ADUGroup) | Escuta novos grupos de atualização de dispositivos |

## <a name="next-steps"></a>Passos seguintes

[Criar recursos de atualização de dispositivos](./create-device-update-account.md)
