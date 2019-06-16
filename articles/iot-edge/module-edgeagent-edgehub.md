---
title: EdgeAgent e EdgeHub pretendido referência de propriedades - Azure IoT Edge | Documentos da Microsoft
description: Reveja as propriedades específicas e os respetivos valores duplos de módulo edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b6eb0c5b0d52bba3d34c9853a73b1f3e07b112a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61322741"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente do IoT Edge e aos duplos de módulo do IoT Edge hub

O agente do IoT Edge e hub do IoT Edge são dois módulos que compõem o runtime do IoT Edge. Para obter mais informações sobre quais deveres executa cada módulo, consulte [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md). 

Este artigo fornece as propriedades pretendidas e propriedades comunicadas de duplos de módulo de tempo de execução. Para obter mais informações sobre como implementar módulos em dispositivos IoT Edge, veja [implementação e monitorização](module-deployment-monitoring.md).

## <a name="edgeagent-desired-properties"></a>Propriedades de EdgeAgent pretendido

O módulo duplo do agente do IoT Edge é chamado `$edgeAgent` e coordena as comunicações entre o agente do Edge de IoT em execução num dispositivo e o IoT Hub. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. 

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| Runtime.Type | Tem de ser "docker" | Sim |
| runtime.settings.minDockerVersion | Definido como a versão mínima do Docker, necessária para esse manifesto de implantação | Sim |
| runtime.settings.loggingOptions | Um JSON em cadeias de contentor que contém as opções de registo do contentor de agente do IoT Edge. [Opções de registo do docker](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| runtime.settings.registryCredentials<br>. .username {registryId} | O nome de utilizador do registo de contentor. No Azure Container Registry, o nome de utilizador é normalmente o nome do registo.<br><br> As credenciais do registo são necessárias para imagens qualquer módulo que não são públicas. | Não |
| runtime.settings.registryCredentials<br>. .password {registryId} | A palavra-passe para o registo de contentor. | Não |
| runtime.settings.registryCredentials<br>. .address {registryId} | O endereço do registo de contentor. No Azure Container Registry, o endereço é normalmente *, como, de {nome do registo}* . | Não |  
| systemModules.edgeAgent.type | Tem de ser "docker" | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente do IoT Edge. Atualmente, o agente do IoT Edge não é possível se Atualize automaticamente. | Sim |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON em cadeias de contentor que contém as opções para a criação do contentor de agente do IoT Edge. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | O ID da implementação que implementadas neste módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado através de uma implementação. Não faça parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem de ser "docker" | Sim |
| systemModules.edgeHub.status | Tem de ser "em execução" | Sim |
| systemModules.edgeHub.restartPolicy | Tem de ser "sempre" | Sim |
| systemModules.edgeHub.settings.image | O URI da imagem do hub IoT Edge. | Sim |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON em cadeias de contentor que contém as opções para a criação do contentor de hub do IoT Edge. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | O ID da implementação que implementadas neste módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado através de uma implementação. Não faça parte de um manifesto de implantação. |
| módulos. .version {moduleId} | Uma cadeia definido pelo utilizador, que representa a versão deste módulo. | Sim |
| módulos. .type {moduleId} | Tem de ser "docker" | Sim |
| módulos. .status {moduleId} | {"a executar" \| "parada"} | Sim |
| modules.{moduleId}.restartPolicy | {"nunca" \| "no-falhou" \| "no-mau estado de funcionamento" \| "sempre"} | Sim |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | Sim |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de contentor que contém as opções para a criação do contentor de módulo. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| modules.{moduleId}.configuration.id | O ID da implementação que implementadas neste módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado através de uma implementação. Não faça parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades comunicadas de EdgeAgent

O agente do IoT Edge comunicado propriedades incluem três partes principais de informações:

1. O estado da aplicação de propriedades pretendidas visto o último;
2. O estado dos módulos atualmente em execução no dispositivo, conforme comunicado pelo agente do IoT Edge; e
3. Uma cópia de propriedades pretendidas, atualmente em execução no dispositivo.

Esta última informação é útil no caso das propriedades pretendidas mais recente não são aplicadas com êxito pelo tempo de execução e o dispositivo ainda está em execução um manifesto de implantação anterior.

> [!NOTE]
> As propriedades reportadas do agente do IoT Edge são úteis como eles podem ser consultados com o [linguagem de consulta do IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) para investigar o status das Implantações em escala. Para obter mais informações sobre como utilizar as propriedades de agente do IoT Edge para obter o estado, consulte [implementações de compreender o IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades pretendidas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este número inteiro refere-se para a última versão das propriedades pretendidas, processados pelo agente do IoT Edge. |
| lastDesiredStatus.code | Este é o código de estado que faça referência a última propriedades pretendidas vistas pelo agente do IoT Edge. Valores permitidos: `200` Êxito, `400` configuração inválida, `412` versão de esquema inválida `417` as propriedades pretendidas são vazias, `500` com falhas |
| lastDesiredStatus.description | Descrição de texto do Estado |
| deviceHealth | `healthy` Se o estado de tempo de execução de todos os módulos `running` ou `stopped`, `unhealthy` caso contrário, |
| configurationHealth.{deploymentId}.health | `healthy` Se o estado de tempo de execução de todos os módulos definido pela implantação {deploymentId} `running` ou `stopped`, `unhealthy` caso contrário, |
| runtime.platform.OS | O sistema operacional em execução no dispositivo de geração de relatórios |
| runtime.platform.architecture | A arquitetura da CPU de relatórios no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O estado do agente do IoT Edge: {"a executar" \| "mau estado de funcionamento"} |
| systemModules.edgeAgent.statusDescription | Descrição de texto do Estado do agente do IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Estado do hub do IoT Edge: {"em execução" \| "parada" \| "failed" \| "término" \| "mau estado de funcionamento"} |
| systemModules.edgeHub.statusDescription | Descrição de texto do Estado do hub do IoT Edge se mau estado de funcionamento. |
| systemModules.edgeHub.exitCode | Se saiu, o código de saída comunicado pelo contentor de hub do IoT Edge |
| systemModules.edgeHub.startTimeUtc | Tempo quando hub do IoT Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Tempo quando hub do IoT Edge saiu pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Tempo quando hub do IoT Edge foi reiniciados pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| modules.{moduleId}.runtimeStatus | Estado do módulo: {"em execução" \| "parada" \| "failed" \| "término" \| "mau estado de funcionamento"} |
| modules.{moduleId}.statusDescription | Descrição de texto do Estado do módulo se mau estado de funcionamento. |
| modules.{moduleId}.exitCode | Se saiu, o código de saída comunicado pelo contentor de módulo |
| modules.{moduleId}.startTimeUtc | Tempo quando o módulo foi iniciado pela última vez |
| modules.{moduleId}.lastExitTimeUtc | Tempo quando o módulo saiu pela última vez |
| modules.{moduleId}.lastRestartTimeUtc | Tempo quando o módulo foi reiniciados pela última vez |
| modules.{moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="edgehub-desired-properties"></a>Propriedades de EdgeHub pretendido

O módulo duplo de hub do IoT Edge é chamado `$edgeHub` e coordena as comunicações entre o hub do IoT Edge em execução num dispositivo e o IoT Hub. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. 

| Propriedade | Descrição | Necessário no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| routes.{routeName} | Uma cadeia de caracteres que representa uma rota de hub do IoT Edge. | O `routes` elemento pode estar presente, mas está vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o IoT Edge hub mantém as mensagens no caso de desligado pontos finais de encaminhamento, por exemplo, desligado do IoT Hub ou módulo local | Sim |

## <a name="edgehub-reported-properties"></a>Propriedades comunicadas de EdgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este número inteiro refere-se para a última versão das propriedades pretendidas, processados pelo hub IoT Edge. |
| lastDesiredStatus.code | Este é o código de estado que faça referência a última propriedades pretendidas vistas pelo hub IoT Edge. Valores permitidos: `200` Êxito, `400` configuração inválida, `500` com falhas |
| lastDesiredStatus.description | Descrição de texto do Estado |
| clientes. .status {moduleId ou dispositivo} | O estado de conectividade desse dispositivo ou o módulo. Possíveis valores {"conectado" \| "desconectado"}. Apenas as identidades do módulo podem estar no estado desligado. Dispositivos Downstream estabelecer ligação ao hub IoT Edge são apresentados apenas quando estiver ligado. |
| clientes. .lastConnectTime {moduleId ou dispositivo} | Última vez no dispositivo ou o módulo ligado |
| clientes. .lastDisconnectTime {moduleId ou dispositivo} | Última vez que o dispositivo ou o módulo desligado |

## <a name="next-steps"></a>Passos Seguintes

Para saber como utilizar estas propriedades para criar manifestos de implantação, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
