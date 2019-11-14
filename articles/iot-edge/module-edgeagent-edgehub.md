---
title: EdgeAgent e EdgeHub pretendido referência de propriedades - Azure IoT Edge | Documentos da Microsoft
description: Reveja as propriedades específicas e os respetivos valores duplos de módulo edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: caee247dfb1f7068e935be9c293a28870cfb98ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069316"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente de IoT Edge e do módulo de Hub de IoT Edge gêmeos

O agente de IoT Edge e o Hub de IoT Edge são dois módulos que compõem o tempo de execução do IoT Edge. Para obter mais informações sobre quais deveres executa cada módulo, consulte [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md). 

Este artigo fornece as propriedades pretendidas e propriedades comunicadas de duplos de módulo de tempo de execução. Para obter mais informações sobre como implantar módulos em dispositivos IoT Edge, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Um módulo de r inclui: 

* **Propriedades desejadas**. O back-end da solução pode definir as propriedades desejadas e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são usadas junto com as propriedades relatadas para sincronizar a configuração ou as condições do módulo.

* **Propriedades relatadas**. O módulo pode definir as propriedades relatadas e o back-end da solução pode lê-las e consultá-las. As propriedades relatadas são usadas junto com as propriedades desejadas para sincronizar a configuração ou as condições do módulo. 

## <a name="edgeagent-desired-properties"></a>Propriedades de EdgeAgent pretendido

O módulo para o agente de IoT Edge é chamado `$edgeAgent` e coordena as comunicações entre o agente de IoT Edge em execução em um dispositivo e Hub IoT. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. 

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| Runtime.Type | Tem de ser "docker" | Sim |
| runtime.settings.minDockerVersion | Definido como a versão mínima do Docker, necessária para esse manifesto de implantação | Sim |
| runtime.settings.loggingOptions | Um JSON em cadeias que contém as opções de log para o contêiner do agente de IoT Edge. [Opções de registo do docker](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| runtime.settings.registryCredentials<br>. .username {registryId} | O nome de utilizador do registo de contentor. No Azure Container Registry, o nome de utilizador é normalmente o nome do registo.<br><br> As credenciais do registo são necessárias para imagens qualquer módulo que não são públicas. | Não |
| runtime.settings.registryCredentials<br>. .password {registryId} | A palavra-passe para o registo de contentor. | Não |
| runtime.settings.registryCredentials<br>. .address {registryId} | O endereço do registo de contentor. Para o registro de contêiner do Azure, o endereço geralmente é *{nome do registro}. azurecr. Io*. | Não |  
| systemModules.edgeAgent.type | Tem de ser "docker" | Sim |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente de IoT Edge. Atualmente, o agente de IoT Edge não é capaz de se atualizar. | Sim |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON em cadeias que contém as opções para a criação do contêiner do agente de IoT Edge. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | O ID da implementação que implementadas neste módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem de ser "docker" | Sim |
| systemModules.edgeHub.status | Tem de ser "em execução" | Sim |
| systemModules.edgeHub.restartPolicy | Tem de ser "sempre" | Sim |
| systemModules.edgeHub.settings.image | O URI da imagem do hub de IoT Edge. | Sim |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON em cadeias que contém as opções para a criação do contêiner de Hub de IoT Edge. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | O ID da implementação que implementadas neste módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |
| módulos. .version {moduleId} | Uma cadeia definido pelo utilizador, que representa a versão deste módulo. | Sim |
| módulos. .type {moduleId} | Tem de ser "docker" | Sim |
| módulos. .status {moduleId} | {"a executar" \| "parada"} | Sim |
| modules.{moduleId}.restartPolicy | {"nunca" \| "em caso de falha" \| "não íntegro" \| "sempre"} | Sim |
| módulos. {ModuleID}. imagePullPolicy | {"on-Create" \| "Never"} | Não |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | Sim |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de contentor que contém as opções para a criação do contentor de módulo. [Opções de criação de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| modules.{moduleId}.configuration.id | O ID da implementação que implementadas neste módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faça parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades comunicadas de EdgeAgent

As propriedades relatadas do agente de IoT Edge incluem três partes principais de informações:

1. O estado da aplicação de propriedades pretendidas visto o último;
2. O status dos módulos em execução no momento no dispositivo, conforme relatado pelo agente de IoT Edge; e
3. Uma cópia de propriedades pretendidas, atualmente em execução no dispositivo.

Essa última informação, uma cópia das propriedades desejadas atuais, é útil para informar se o dispositivo aplicou as propriedades desejadas mais recentes ou ainda está executando um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente de IoT Edge são úteis, pois podem ser consultadas com a [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) para investigar o status das implantações em escala. Para obter mais informações sobre como usar as propriedades do agente de IoT Edge para status, consulte [entender IOT Edge implantações para dispositivos únicos ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades pretendidas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente de IoT Edge. |
| lastDesiredStatus.code | Esse código de status refere-se às últimas propriedades desejadas vistas pelo agente de IoT Edge. Valores permitidos: `200` sucesso, `400` configuração inválida, `412` versão de esquema inválida `417` as propriedades pretendidas são vazias, `500` com falhas |
| lastDesiredStatus.description | Descrição de texto do Estado |
| deviceHealth | `healthy` Se o estado de tempo de execução de todos os módulos `running` ou `stopped`, `unhealthy` caso contrário, |
| configurationHealth.{deploymentId}.health | `healthy` Se o estado de tempo de execução de todos os módulos definido pela implantação {deploymentId} `running` ou `stopped`, `unhealthy` caso contrário, |
| runtime.platform.OS | O sistema operacional em execução no dispositivo de geração de relatórios |
| runtime.platform.architecture | A arquitetura da CPU de relatórios no dispositivo |
| systemModules.edgeAgent.runtimeStatus | O status relatado do agente de IoT Edge: {"em execução" \| "não íntegro"} |
| systemModules.edgeAgent.statusDescription | Descrição de texto do status relatado do agente de IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Status do hub de IoT Edge: {"em execução" \| "parado" \| "falha" \| "retirada" \| "não íntegro"} |
| systemModules.edgeHub.statusDescription | Texto descrição do status do hub de IoT Edge se não estiver íntegro. |
| systemModules.edgeHub.exitCode | O código de saída relatado pelo contêiner do Hub IoT Edge se o contêiner for encerrado |
| systemModules.edgeHub.startTimeUtc | Hora em que IoT Edge Hub foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Hora em que o Hub de IoT Edge saiu pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Hora em que IoT Edge Hub foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |
| modules.{moduleId}.runtimeStatus | Status do módulo: {"em execução" \| "parado" \| "falha" \| "retirada" \| "não íntegro"} |
| modules.{moduleId}.statusDescription | Texto descrição do status do módulo se não estiver íntegro. |
| modules.{moduleId}.exitCode | O código de saída relatado pelo contêiner do módulo se o contêiner for encerrado |
| modules.{moduleId}.startTimeUtc | Tempo quando o módulo foi iniciado pela última vez |
| modules.{moduleId}.lastExitTimeUtc | Tempo quando o módulo saiu pela última vez |
| modules.{moduleId}.lastRestartTimeUtc | Tempo quando o módulo foi reiniciados pela última vez |
| modules.{moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinício. |

## <a name="edgehub-desired-properties"></a>Propriedades de EdgeHub pretendido

O módulo para o Hub de IoT Edge é chamado `$edgeHub` e coordena as comunicações entre o Hub de IoT Edge em execução em um dispositivo e Hub IoT. As propriedades pretendidas são definidas ao aplicar um manifesto de implantação num dispositivo específico, como parte de uma implementação de único dispositivo ou à escala. 

| Propriedade | Descrição | Necessário no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem de ser "1.0" | Sim |
| routes.{routeName} | Uma cadeia de caracteres que representa uma rota de Hub de IoT Edge. Para obter mais informações, consulte [declarar rotas](module-composition.md#declare-routes). | O `routes` elemento pode estar presente, mas está vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que IoT Edge Hub mantém mensagens se desconectado dos pontos de extremidade de roteamento, seja o Hub IoT ou um módulo local. O valor pode ser qualquer inteiro positivo. | Sim |

## <a name="edgehub-reported-properties"></a>Propriedades comunicadas de EdgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub de IoT Edge. |
| lastDesiredStatus.code | O código de status referente às últimas propriedades desejadas vistas pelo hub de IoT Edge. Valores permitidos: `200` sucesso, `400` configuração inválida, `500` com falhas |
| lastDesiredStatus.description | Texto descrição do status. |
| clientes. .status {moduleId ou dispositivo} | O estado de conectividade desse dispositivo ou o módulo. Possíveis valores {"conectado" \| "desconectado"}. Apenas as identidades do módulo podem estar no estado desligado. Os dispositivos downstream que se conectam ao Hub IoT Edge aparecem somente quando conectado. |
| clientes. .lastConnectTime {moduleId ou dispositivo} | Última vez em que o dispositivo ou módulo está conectado. |
| clientes. .lastDisconnectTime {moduleId ou dispositivo} | Última vez em que o dispositivo ou módulo foi desconectado. |

## <a name="next-steps"></a>Passos seguintes

Para saber como utilizar estas propriedades para criar manifestos de implantação, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
