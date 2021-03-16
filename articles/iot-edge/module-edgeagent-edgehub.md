---
title: Propriedades do agente e do módulo hub gémeos - Azure IoT Edge
description: Reveja as propriedades específicas e os seus valores para os gémeos módulos edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951111b217b7ace3f12676edf6febfa7266094df
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489953"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente IoT Edge e gémeos módulos de hub IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O agente IoT Edge e o hub IoT Edge são dois módulos que compõem o tempo de execução IoT Edge. Para obter mais informações sobre as responsabilidades de cada módulo de tempo de execução, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo fornece as propriedades desejadas e as propriedades reportadas dos gémeos módulos de tempo de execução. Para obter mais informações sobre como implementar módulos em dispositivos IoT Edge, consulte [Saiba como implantar módulos e estabeleça rotas no IoT Edge](module-composition.md).

Um módulo gémeo inclui:

* **Propriedades desejadas**. O backend da solução pode definir as propriedades desejadas, e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são utilizadas juntamente com propriedades reportadas para sincronizar a configuração ou condições do módulo.

* **Propriedades reportadas**. O módulo pode definir propriedades reportadas, e o backend da solução pode lê-las e questioná-las. As propriedades reportadas são utilizadas juntamente com as propriedades desejadas para sincronizar a configuração ou as condições do módulo.

## <a name="edgeagent-desired-properties"></a>EdgeAgent propriedades desejadas

O módulo twin para o agente IoT Edge é chamado `$edgeAgent` e coordena as comunicações entre o agente IoT Edge em execução num dispositivo e o IoT Hub. As propriedades desejadas são definidas quando se aplica um manifesto de implantação num dispositivo específico como parte de um dispositivo único ou de uma implantação à escala.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| schemaVersão | Ou "1.0" ou "1.1". A versão 1.1 foi introduzida com a versão 1.0.10 do IoT Edge, e é recomendada. | Sim |
| tempo de execução.tipo | Tem que ser "estivador". | Sim |
| runtime.settings.minDockerVersion | Definir para a versão estival mínima exigida por este manifesto de implantação | Sim |
| tempo de execução.definições.loggingOptions | Um JSON com uma JSON com cordas que contém as opções de registo para o contentor do agente IoT Edge. [Opções de registo de estivadores](https://docs.docker.com/engine/admin/logging/overview/) | No |
| tempo de execução.definições.registryCredentais<br>. {registryId}.username | O nome de utilizador do registo do contentor. Para o Registo do Contentor Azure, o nome de utilizador é normalmente o nome de registo.<br><br> As credenciais de registo são necessárias para quaisquer imagens de módulos privados. | No |
| tempo de execução.definições.registryCredentais<br>. {registryId}.password | A senha do registo do contentor. | No |
| tempo de execução.definições.registryCredentais<br>. {registryId}.endereço | O endereço do registo do contentor. Para o Registo do Contentor Azure, o endereço é geralmente *{registry name}.azurecr.io*. | No |  
| systemModules.edgeAgent.type | Tem que ser "estivador". | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente IoT Edge. Atualmente, o agente IoT Edge não é capaz de se atualizar. | Sim |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON cordificado contendo as opções para a criação do contentor de agente IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | A identificação da implantação que implantou este módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem que ser "estivador". | Sim |
| systemModules.edgeHub.status | Tem que estar a "correr" | Sim |
| systemModules.edgeHub.restartPolicy | Tem que ser "sempre" | Sim |
| systemModules.edgeHub.startupOrder | Um valor inteiro para o qual um módulo tem na ordem de arranque. 0 é o primeiro e o inteiro máximo (4294967295) é o último. Se um valor não for fornecido, o padrão é o número máximo.  | No |
| systemModules.edgeHub.settings.image | O URI da imagem do hub IoT Edge. | Sim |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON cordificado contendo as opções para a criação do recipiente do hub IoT Edge. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | A identificação da implantação que implantou este módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| módulos. {moduleId}.versão | Uma cadeia definida pelo utilizador que representa a versão deste módulo. | Sim |
| módulos. {moduleId}.type | Tem que ser "estivador". | Sim |
| módulos. {moduleId}.status | {"running" \| "parado"} | Sim |
| módulos. {moduleId}.restartPolicy | {"nunca" \| "on-failure" \| "on-unhealthy" \| "always"} | Sim |
| módulos. {moduleId}.startupOrder | Um valor inteiro para o qual um módulo tem na ordem de arranque. 0 é o primeiro e o inteiro máximo (4294967295) é o último. Se um valor não for fornecido, o padrão é o número máximo.  | No |
| módulos. {moduleId}.imagePullPolicy | {"on-create" \| "nunca"} | No |
| módulos. {moduleId}.env | Uma lista de variáveis ambientais para passar para o módulo. Toma o formato `"<name>": {"value": "<value>"}` | No |
| módulos. {moduleId}.definições.imagem | O URI para a imagem do módulo. | Sim |
| módulos. {moduleId}.configurações.createOptions | Um JSON cordificado contendo as opções para a criação do recipiente do módulo. [Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| módulos. {moduleId}.configuration.id | A identificação da implantação que implantou este módulo. | IoT Hub define esta propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent reportou propriedades

As propriedades reportadas pelo agente IoT Edge incluem três principais peças de informação:

1. O estado da aplicação dos últimos imóveis pretendidos;
2. O estado dos módulos atualmente em funcionamento no dispositivo, conforme reportado pelo agente IoT Edge; e
3. Uma cópia das propriedades desejadas atualmente em execução no dispositivo.

A cópia das propriedades atuais desejadas é útil para dizer se o dispositivo aplicou a mais recente implementação ou ainda está a executar um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente IoT Edge são úteis, uma vez que podem ser consultadas com a [linguagem de consulta IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) para investigar o estado das implementações em escala. Para obter mais informações sobre como utilizar as propriedades do agente IoT Edge para o estado, consulte [as implementações do Understand IoT Edge para dispositivos individuais ou à escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | Descrição |
| -------- | ----------- |
| últimaVersão deDeseired | Este número inteiro refere-se à última versão das propriedades desejadas processadas pelo agente IoT Edge. |
| últimoDesestatus.code | Este código de estado refere-se às últimas propriedades desejadas vistas pelo agente IoT Edge. Valores permitidos: `200` Sucesso, `400` configuração inválida, `412` versão de esquema inválido, `417` as propriedades desejadas estão vazias, `500` Falhadas |
| lastDesiredStatus.description | Descrição do texto do estado |
| dispositivoSaúde | `healthy` se o estado de funcionação de todos os módulos for `running` `stopped` ou, `unhealthy` de outra forma |
| configuraçãoHealth. {deploymentId}.health | `healthy` se o estado de funcionamento de todos os módulos definidos pela implementação {deploymentId} for `running` `stopped` ou, `unhealthy` de outra forma |
| tempo de execução.platform.OS | Reportando o sistema operativo em execução no dispositivo |
| runtime.platform.architecture | Reportagem da arquitetura do CPU no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O estado reportado do agente IoT Edge: {"running" \| "insalubre"} |
| systemModules.edgeAgent.statusDscription | Descrição de texto do estado reportado do agente IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Estado do hub IoT Edge: { "running" \| "stop" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDscription | Descrição do texto do estado do hub IoT Edge se não for saudável. |
| systemModules.edgeHub.exitCode | O código de saída reportado pelo contentor do hub IoT Edge se o contentor sair |
| systemModules.edgeHub.startTimeUtc | Tempo em que o hub IoT Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Tempo em que o hub IoT Edge saiu pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Tempo em que o hub IoT Edge foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| módulos. {moduleId}.runtimeStatus | Estado do módulo: { "running" \| "stop" \| "failed" \| "backoff" \| "unhealthy" } |
| módulos. {moduleId}.statusDscription | Descrição do texto do estado do módulo se não for saudável. |
| módulos. {moduleId}.exitCode | O código de saída reportado pelo contentor do módulo se o contentor sair |
| módulos. {moduleId}.startTimeUtc | Hora em que o módulo foi iniciado pela última vez |
| módulos. {moduleId}.lastExitTimeUtc | Hora em que o módulo saiu pela última vez |
| módulos. {moduleId}.lastRestartTimeUtc | Tempo em que o módulo foi reiniciado pela última vez |
| módulos. {moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="edgehub-desired-properties"></a>EdgeHub propriedades desejadas

O módulo twin para o hub IoT Edge é chamado `$edgeHub` e coordena as comunicações entre o hub IoT Edge em execução num dispositivo e IoT Hub. As propriedades desejadas são definidas quando se aplica um manifesto de implantação num dispositivo específico como parte de um dispositivo único ou de uma implantação à escala.

| Propriedade | Descrição | Requerido no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersão | Ou "1.0" ou "1.1". A versão 1.1 foi introduzida com a versão 1.0.10 do IoT Edge, e é recomendada. | Sim |
| rotas. {routeName} | Uma corda que representa uma rota do hub IoT Edge. Para mais informações, consulte [as rotas do Declaro.](module-composition.md#declare-routes) | O `routes` elemento pode estar presente, mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que o hub IoT Edge mantém as mensagens se desligado dos pontos finais de encaminhamento, seja o IoT Hub ou um módulo local. O valor pode ser qualquer inteiro positivo. | Sim |

## <a name="edgehub-reported-properties"></a>EdgeHub reportou propriedades

| Propriedade | Descrição |
| -------- | ----------- |
| últimaVersão deDeseired | Este número inteiro refere-se à última versão das propriedades desejadas processadas pelo hub IoT Edge. |
| últimoDesestatus.code | O código de estado refere-se às últimas propriedades desejadas vistas pelo hub IoT Edge. Valores permitidos: `200` Sucesso, `400` Configuração inválida, `500` Falhado |
| lastDesiredStatus.description | Descrição do texto do estado. |
| clientes. {dispositivo ou móduloId}.status | O estado de conectividade deste dispositivo ou módulo. Valores possíveis {"connected" \| "desligado"}. Apenas as identidades do módulo podem estar desligadas. Os dispositivos a jusante que se ligam ao hub IoT Edge só aparecem quando ligados. |
| clientes. {dispositivo ou móduloId}.lastConnectTime | A última vez que o dispositivo ou módulo ligou. |
| clientes. {dispositivo ou móduloId}.lastDisconnectTime | A última vez que o dispositivo ou módulo foi desligado. |

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar estas propriedades para construir manifestos de implantação, consulte [como os módulos IoT Edge podem ser usados, configurados e reutilizados.](module-composition.md)
