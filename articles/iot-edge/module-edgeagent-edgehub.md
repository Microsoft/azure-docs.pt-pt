---
title: Propriedades dos gémeos do módulo de agente e hub - Azure IoT Edge
description: Reveja as propriedades específicas e os seus valores para os gémeos edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546194"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente IoT Edge e dos gémeos do módulo IoT Edge

O agente IoT Edge e o hub IoT Edge são dois módulos que compõem o tempo de funcionação do IoT Edge. Para obter mais informações sobre as responsabilidades de cada módulo de tempo de funcionação, consulte Compreender o tempo de funcionação do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo fornece as propriedades desejadas e propriedades reportadas dos gémeos do módulo runtime. Para obter mais informações sobre como implementar módulos em dispositivos IoT Edge, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

Um módulo twin inclui:

* **Propriedades desejadas.** O backend da solução pode definir as propriedades desejadas, e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são usadas juntamente com propriedades reportadas para sincronizar a configuração ou condições do módulo.

* **Propriedades reportadas.** O módulo pode definir propriedades reportadas, e o backend da solução pode lê-las e consultas. As propriedades reportadas são usadas juntamente com as propriedades desejadas para sincronizar a configuração ou as condições do módulo.

## <a name="edgeagent-desired-properties"></a>Propriedades desejadas por EdgeAgent

O módulo twin para o agente `$edgeAgent` IoT Edge é chamado e coordena as comunicações entre o agente IoT Edge em execução num dispositivo e ioT Hub. As propriedades desejadas são definidas ao aplicar um manifesto de implantação num dispositivo específico como parte de um único dispositivo ou de uma implantação em escala.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | Sim |
| tempo de execução.tipo | Tem que ser "estivador" | Sim |
| tempo de execução.definições.minDockerVersion | Definido para a versão mínima do Docker exigida por este manifesto de implantação | Sim |
| tempo de execução.definições.loggingOptions | Um JSON stringizado contendo as opções de exploração de madeira para o recipiente do agente IoT Edge. [Opções de exploração de madeira de estivador](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| tempo de execução.definições.registryCredenciais<br>. {registryId}.username | O nome de utilizador do registo do contentor. Para o Registo de Contentores Azure, o nome de utilizador é geralmente o nome do registo.<br><br> As credenciais de registo são necessárias para quaisquer imagens de módulos privados. | Não |
| tempo de execução.definições.registryCredenciais<br>. {registryId}.password | A senha do registo do contentor. | Não |
| tempo de execução.definições.registryCredenciais<br>. {registryId}.address | O endereço do registo do contentor. Para o Registo de Contentores Azure, o endereço é geralmente *{registry name}.azurecr.io*. | Não |  
| systemModules.edgeAgent.type | Tem que ser "estivador" | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente IoT Edge. Atualmente, o agente IoT Edge não é capaz de se atualizar sozinho. | Sim |
| systemModules.edgeAgent.settings<br>.criarOpções | Um JSON stringizado contendo as opções para a criação do recipiente de agente IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | A identificação da implementação que implantou este módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem que ser "estivador" | Sim |
| systemModules.edgeHub.status | Tem que ser "correr" | Sim |
| systemModules.edgeHub.restartPolicy | Tem que ser "sempre" | Sim |
| systemModules.edgeHub.settings.image | O URI da imagem do centro ioT Edge. | Sim |
| systemModules.edgeHub.settings<br>.criarOpções | Um JSON stringizado contendo as opções para a criação do recipiente do hub IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | A identificação da implementação que implantou este módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| módulos. {moduleId}.versão | Uma cadeia definida pelo utilizador que representa a versão deste módulo. | Sim |
| módulos. {móduloId}.type | Tem que ser "estivador" | Sim |
| módulos. {móduloId}.status | {"running" \| "stop"} | Sim |
| módulos. {moduleId}.restartPolicy | {"nunca" \| "on-failure" \| "on-inhealthy" \| "always"} | Sim |
| módulos. {moduleId}.imagePullPolicy | {"on-create" \| "nunca"} | Não |
| módulos. {moduleId}.env | Uma lista de variáveis ambientais para passar para o módulo. Toma o formato`"<name>": {"value": "<value>"}` | Não |
| módulos. {moduleId}.definições.image | O URI para a imagem do módulo. | Sim |
| módulos. {moduleId}.settings.createOptions | Um JSON stringizado contendo as opções para a criação do recipiente do módulo. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| módulos. {moduleId}.configuração.id | A identificação da implementação que implantou este módulo. | O IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades reportadas edgeAgent

As propriedades reportadas pelo agente IoT Edge incluem três principais informações:

1. O estatuto da aplicação das propriedades desejadas pela última vez;
2. O estado dos módulos atualmente em funcionamento no dispositivo, conforme relatado pelo agente IoT Edge; e
3. Uma cópia das propriedades desejadas atualmente em funcionamento no dispositivo.

A cópia das propriedades atuais desejadas é útil para dizer se o dispositivo aplicou a mais recente implementação ou se ainda está a executar um manifesto de implementação anterior.

> [!NOTE]
> As propriedades relatadas do agente IoT Edge são úteis, uma vez que podem ser consultadas com a linguagem de [consulta do IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) para investigar o estado das implementações em escala. Para obter mais informações sobre como utilizar as propriedades do agente IoT Edge para o estado, consulte [as implementações de Understand IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

A tabela que se segue não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente IoT Edge. |
| lastDesiredStatus.code | Este código de estado refere-se às últimas propriedades desejadas vistas pelo agente IoT Edge. Valores `200` `400` permitidos: Sucesso, `412` Configuração inválida, `417` versão schema `500` inválida, as propriedades desejadas estão vazias, falhadas |
| lastDesiredStatus.description | Descrição do texto do estado |
| dispositivoSHealth | `healthy`se o estado de tempo de `running` `stopped`execução de todos os módulos for ou, `unhealthy` caso contrário |
| configuraçãoHealth. {deploymentId}.health | `healthy`se o estado de tempo de funcionamento de todos `running` os `stopped` `unhealthy` módulos definidos pela implementação {deploymentId} for ou , de outra forma |
| runtime.platform.OS | Reportar o Sistema operativo em execução no dispositivo |
| runtime.platform.architecture | Reportagem da arquitetura da CPU no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O estado reportado do agente IoT \| Edge: {"running" "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrição do texto do estado reportado do agente IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Estado do hub IoT Edge: \| { \| "running" \| "stop" \| "failed" "backoff" "unhealthy" } |
| systemModules.edgeHub.statusDescription | Descrição do texto do estado do hub IoT Edge se não for saudável. |
| systemModules.edgeHub.exitCode | O código de saída reportado pelo contentor do hub IoT Edge se o contentor sair |
| systemModules.edgeHub.startTimeUtc | Tempo em que o hub IoT Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Tempo em que ioT Edge hub saiu pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Tempo em que o hub ioT Edge foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| módulos. {moduleId}.runtimeStatus | Estado do módulo: { \| "running" \| "stop" \| "failed" "backoff" \| "unhealthy" } |
| módulos. {móduloId}.statusDescription | Descrição do texto do estado do módulo se não for saudável. |
| módulos. {moduleId}.exitCode | O código de saída reportado pelo contentor do módulo se o contentor sair |
| módulos. {moduleId}.startTimeUtc | Hora em que o módulo foi iniciado pela última vez |
| módulos. {moduleId}.lastExitTimeUtc | Hora da última saída do módulo |
| módulos. {moduleId}.lastRestartTimeUtc | Tempo em que o módulo foi reiniciado pela última vez |
| módulos. {moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="edgehub-desired-properties"></a>Propriedades desejadas edgeHub

O módulo twin para o hub `$edgeHub` IoT Edge é chamado e coordena as comunicações entre o hub IoT Edge em execução num dispositivo e IoT Hub. As propriedades desejadas são definidas ao aplicar um manifesto de implantação num dispositivo específico como parte de um único dispositivo ou de uma implantação em escala.

| Propriedade | Descrição | Exigido no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | Sim |
| rotas. {routeName} | Uma corda representando uma rota do centro de IoT Edge. Para mais informações, consulte [Rotas declarar](module-composition.md#declare-routes). | O `routes` elemento pode estar presente, mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o hub IoT Edge mantém as mensagens se desligadas dos pontos finais de encaminhamento, seja o IoT Hub ou um módulo local. O valor pode ser qualquer inteiro positivo. | Sim |

## <a name="edgehub-reported-properties"></a>Propriedades reportadas edgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub IoT Edge. |
| lastDesiredStatus.code | O código de estado referente às propriedades desejadas últimas vistas pelo centro IoT Edge. Valores `200` permitidos: `400` Sucesso, `500` Configuração inválida, Falha |
| lastDesiredStatus.description | Descrição do texto do estado. |
| clientes. {dispositivo ou móduloId}.status | O estado de conectividade deste dispositivo ou módulo. Valores possíveis \| {"connected" "desligado"}. Apenas as identidades dos módulos podem estar em estado dedesconectado. Os dispositivos a jusante que ligam o hub IoT Edge só aparecem quando ligados. |
| clientes. {dispositivo ou móduloId}.lastConnectTime | Da última vez que o dispositivo ou módulo se ligou. |
| clientes. {dispositivo ou móduloId}.lastDisconnectTime | Da última vez que o dispositivo ou módulo se desligou. |

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar estas propriedades para construir manifestos de implantação, veja [compreender como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).
