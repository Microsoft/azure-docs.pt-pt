---
title: Propriedades dos gémeos do módulo de agente e hub - Azure IoT Edge
description: Reveja as propriedades específicas e os respetivos valores duplos de módulo edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4684daf2a1095a40c478170be37edcae788868ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379363"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente IoT Edge e dos gémeos do módulo IoT Edge

O agente IoT Edge e o hub IoT Edge são dois módulos que compõem o tempo de funcionação do IoT Edge. Para obter mais informações sobre as responsabilidades de cada módulo de tempo de funcionação, consulte Compreender o tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo fornece as propriedades pretendidas e propriedades comunicadas de duplos de módulo de tempo de execução. Para obter mais informações sobre como implementar módulos em dispositivos IoT Edge, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

Um módulo twin inclui:

* **Propriedades desejadas.** O backend da solução pode definir as propriedades desejadas, e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são usadas juntamente com propriedades reportadas para sincronizar a configuração ou condições do módulo.

* **Propriedades reportadas.** O módulo pode definir propriedades reportadas, e o backend da solução pode lê-las e consultas. As propriedades reportadas são usadas juntamente com as propriedades desejadas para sincronizar a configuração ou as condições do módulo.

## <a name="edgeagent-desired-properties"></a>Propriedades de EdgeAgent pretendido

O módulo twin para o agente IoT Edge chama-se `$edgeAgent` e coordena as comunicações entre o agente IoT Edge em execução num dispositivo e ioT Hub. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| Runtime.Type | Tem de ser "docker" | Sim |
| runtime.settings.minDockerVersion | Definido como a versão mínima do Docker, necessária para esse manifesto de implantação | Sim |
| runtime.settings.loggingOptions | Um JSON stringizado contendo as opções de exploração de madeira para o recipiente do agente IoT Edge. [Opções de exploração de madeira de estivador](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| runtime.settings.registryCredentials<br>. .username {registryId} | O nome de utilizador do registo de contentor. No Azure Container Registry, o nome de utilizador é normalmente o nome do registo.<br><br> As credenciais de registo são necessárias para quaisquer imagens de módulos privados. | Não |
| runtime.settings.registryCredentials<br>. .password {registryId} | A palavra-passe para o registo de contentor. | Não |
| runtime.settings.registryCredentials<br>. .address {registryId} | O endereço do registo de contentor. Para o Registo de Contentores Azure, o endereço é geralmente *{registry name}.azurecr.io*. | Não |  
| systemModules.edgeAgent.type | Tem de ser "docker" | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente IoT Edge. Atualmente, o agente IoT Edge não é capaz de se atualizar sozinho. | Sim |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON stringizado contendo as opções para a criação do recipiente de agente IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | O ID da implementação que implementadas neste módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem de ser "docker" | Sim |
| systemModules.edgeHub.status | Tem de ser "em execução" | Sim |
| systemModules.edgeHub.restartPolicy | Tem de ser "sempre" | Sim |
| systemModules.edgeHub.settings.image | O URI da imagem do centro ioT Edge. | Sim |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON stringizado contendo as opções para a criação do recipiente do hub IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | O ID da implementação que implementadas neste módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |
| módulos. .version {moduleId} | Uma cadeia definido pelo utilizador, que representa a versão deste módulo. | Sim |
| módulos. .type {moduleId} | Tem de ser "docker" | Sim |
| módulos. .status {moduleId} | {"running" \| "parou"} | Sim |
| modules.{moduleId}.restartPolicy | {"nunca" \| "on-failure" \| "on-unhealthy" \| "always"} | Sim |
| módulos. {moduleId}.imagePullPolicy | {"on-create" \| "nunca"} | Não |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | Sim |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de contentor que contém as opções para a criação do contentor de módulo. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| modules.{moduleId}.configuration.id | O ID da implementação que implementadas neste módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades comunicadas de EdgeAgent

As propriedades reportadas pelo agente IoT Edge incluem três principais informações:

1. O estado da aplicação de propriedades pretendidas visto o último;
2. O estado dos módulos atualmente em funcionamento no dispositivo, conforme relatado pelo agente IoT Edge; e
3. Uma cópia de propriedades pretendidas, atualmente em execução no dispositivo.

A cópia das propriedades atuais desejadas é útil para dizer se o dispositivo aplicou a mais recente implementação ou se ainda está a executar um manifesto de implementação anterior.

> [!NOTE]
> As propriedades relatadas do agente IoT Edge são úteis, uma vez que podem ser consultadas com a linguagem de [consulta do IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) para investigar o estado das implementações em escala. Para obter mais informações sobre como utilizar as propriedades do agente IoT Edge para o estado, consulte [as implementações de Understand IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades pretendidas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente IoT Edge. |
| lastDesiredStatus.code | Este código de estado refere-se às últimas propriedades desejadas vistas pelo agente IoT Edge. Valores permitidos: `200` Sucesso, `400` Configuração Inválida, versão de esquema inválido `412`, `417` as propriedades desejadas estão vazias, `500` Failed |
| lastDesiredStatus.description | Descrição de texto do Estado |
| deviceHealth | `healthy` se o estado de execução de todos os módulos for `running` ou `stopped`, `unhealthy` de outra forma |
| configurationHealth.{deploymentId}.health | `healthy` se o estado de tempo de funcionamento de todos os módulos definidos pela implementação {deploymentId} for `running` ou `stopped`, `unhealthy` de outra forma |
| runtime.platform.OS | O sistema operacional em execução no dispositivo de geração de relatórios |
| runtime.platform.architecture | A arquitetura da CPU de relatórios no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O estado reportado do agente IoT Edge: {"running" \| "insalubre"} |
| systemModules.edgeAgent.statusDescription | Descrição do texto do estado reportado do agente IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Estado do hub IoT Edge: { "running" \| "parou" \| "falhou" \| "backoff" \| "insalubre" } |
| systemModules.edgeHub.statusDescription | Descrição do texto do estado do hub IoT Edge se não for saudável. |
| systemModules.edgeHub.exitCode | O código de saída reportado pelo contentor do hub IoT Edge se o contentor sair |
| systemModules.edgeHub.startTimeUtc | Tempo em que o hub IoT Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Tempo em que ioT Edge hub saiu pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Tempo em que o hub ioT Edge foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| modules.{moduleId}.runtimeStatus | Estado do módulo: { "running" \| "parou" \| "falhou" \| "backoff" \| "insalubre" } |
| modules.{moduleId}.statusDescription | Descrição do texto do estado do módulo se não for saudável. |
| modules.{moduleId}.exitCode | O código de saída reportado pelo contentor do módulo se o contentor sair |
| modules.{moduleId}.startTimeUtc | Tempo quando o módulo foi iniciado pela última vez |
| modules.{moduleId}.lastExitTimeUtc | Tempo quando o módulo saiu pela última vez |
| modules.{moduleId}.lastRestartTimeUtc | Tempo quando o módulo foi reiniciados pela última vez |
| modules.{moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="edgehub-desired-properties"></a>Propriedades de EdgeHub pretendido

O módulo twin para o hub IoT Edge chama-se `$edgeHub` e coordena as comunicações entre o hub IoT Edge em execução num dispositivo e ioT Hub. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala.

| Propriedade | Descrição | Necessário no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| routes.{routeName} | Uma corda representando uma rota do centro de IoT Edge. Para mais informações, consulte [Rotas declarar](module-composition.md#declare-routes). | O elemento `routes` pode estar presente, mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o hub IoT Edge mantém as mensagens se desligadas dos pontos finais de encaminhamento, seja o IoT Hub ou um módulo local. O valor pode ser qualquer inteiro positivo. | Sim |

## <a name="edgehub-reported-properties"></a>Propriedades comunicadas de EdgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub IoT Edge. |
| lastDesiredStatus.code | O código de estado referente às propriedades desejadas últimas vistas pelo centro IoT Edge. Valores permitidos: sucesso `200`, configuração `400` inválida, `500` Falhado |
| lastDesiredStatus.description | Descrição do texto do estado. |
| clientes. .status {moduleId ou dispositivo} | O estado de conectividade desse dispositivo ou o módulo. Valores possíveis {"connected" \| "desligado"}. Apenas as identidades do módulo podem estar no estado desligado. Os dispositivos a jusante que ligam o hub IoT Edge só aparecem quando ligados. |
| clientes. .lastConnectTime {moduleId ou dispositivo} | Da última vez que o dispositivo ou módulo se ligou. |
| clientes. .lastDisconnectTime {moduleId ou dispositivo} | Da última vez que o dispositivo ou módulo se desligou. |

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar estas propriedades para construir manifestos de implantação, veja [compreender como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).
