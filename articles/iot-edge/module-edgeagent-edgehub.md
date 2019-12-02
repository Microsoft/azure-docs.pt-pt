---
title: Propriedades do módulo agente e Hub gêmeos-Azure IoT Edge
description: Examine as propriedades específicas e seus valores para o módulo edgeAgent e edgeHub gêmeos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 731c51894126a6de75c9fc25e4e7bdb3dfa4dd03
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665802"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente de IoT Edge e do módulo de Hub de IoT Edge gêmeos

O agente de IoT Edge e o Hub de IoT Edge são dois módulos que compõem o tempo de execução do IoT Edge. Para obter mais informações sobre quais funções cada módulo executa, consulte [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md). 

Este artigo fornece as propriedades desejadas e as propriedades relatadas do módulo de tempo de execução gêmeos. Para obter mais informações sobre como implantar módulos em dispositivos IoT Edge, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Um módulo de r inclui: 

* **Propriedades desejadas**. O back-end da solução pode definir as propriedades desejadas e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são usadas junto com as propriedades relatadas para sincronizar a configuração ou as condições do módulo.

* **Propriedades relatadas**. O módulo pode definir as propriedades relatadas e o back-end da solução pode lê-las e consultá-las. As propriedades relatadas são usadas junto com as propriedades desejadas para sincronizar a configuração ou as condições do módulo. 

## <a name="edgeagent-desired-properties"></a>Propriedades desejadas do EdgeAgent

O módulo para o agente de IoT Edge é chamado `$edgeAgent` e coordena as comunicações entre o agente de IoT Edge em execução em um dispositivo e Hub IoT. As propriedades desejadas são definidas ao aplicar um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| SchemaVersion | Deve ser "1,0" | Sim |
| tempo de execução. tipo | Deve ser "Docker" | Sim |
| Runtime. Settings. minDockerVersion | Definir para a versão mínima do Docker exigida por este manifesto de implantação | Sim |
| Runtime. Settings. LoggingOptions | Um JSON em cadeias que contém as opções de log para o contêiner do agente de IoT Edge. [Opções de log do Docker](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| Runtime. Settings. registryCredentials<br>. {registryid}. nome de usuário | O nome de usuário do registro de contêiner. Para o registro de contêiner do Azure, o nome de usuário geralmente é o do registro.<br><br> As credenciais do registro são necessárias para qualquer imagem de módulo que não seja pública. | Não |
| Runtime. Settings. registryCredentials<br>. {registryid}. senha | A senha para o registro de contêiner. | Não |
| Runtime. Settings. registryCredentials<br>. {registryid}. endereço | O endereço do registro de contêiner. Para o registro de contêiner do Azure, o endereço geralmente é *{nome do registro}. azurecr. Io*. | Não |  
| systemModules. edgeAgent. Type | Deve ser "Docker" | Sim |
| systemModules. edgeAgent. Settings. Image | O URI da imagem do agente de IoT Edge. Atualmente, o agente de IoT Edge não é capaz de se atualizar. | Sim |
| systemModules. edgeAgent. Settings<br>. criaroptions | Um JSON em cadeias que contém as opções para a criação do contêiner do agente de IoT Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules. edgeHub. Type | Deve ser "Docker" | Sim |
| systemModules. edgeHub. status | Deve estar "em execução" | Sim |
| systemModules. edgeHub. restartPolicy | Deve ser "Always" | Sim |
| systemModules. edgeHub. Settings. Image | O URI da imagem do hub de IoT Edge. | Sim |
| systemModules. edgeHub. Settings<br>. criaroptions | Um JSON em cadeias que contém as opções para a criação do contêiner de Hub de IoT Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| módulos. {ModuleID}. versão | Uma cadeia de caracteres definida pelo usuário que representa a versão deste módulo. | Sim |
| módulos. {ModuleID}. tipo | Deve ser "Docker" | Sim |
| módulos. {ModuleID}. status | {"Running" \| "parado"} | Sim |
| módulos. {ModuleID}. restartPolicy | {"nunca" \| "em caso de falha" \| "não íntegro" \| "sempre"} | Sim |
| módulos. {ModuleID}. imagePullPolicy | {"on-Create" \| "Never"} | Não |
| módulos. {ModuleID}. Settings. Image | O URI para a imagem do módulo. | Sim |
| módulos. {ModuleID}. Settings. | Um JSON em cadeias que contém as opções para a criação do contêiner de módulo. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| módulos. {ModuleID}. Configuration. ID | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades relatadas do EdgeAgent

As propriedades relatadas do agente de IoT Edge incluem três partes principais de informações:

1. O status do aplicativo das propriedades desejadas da última vista;
2. O status dos módulos em execução no momento no dispositivo, conforme relatado pelo agente de IoT Edge; e
3. Uma cópia das propriedades desejadas atualmente em execução no dispositivo.

Essa última informação, uma cópia das propriedades desejadas atuais, é útil para informar se o dispositivo aplicou as propriedades desejadas mais recentes ou ainda está executando um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente de IoT Edge são úteis, pois podem ser consultadas com a [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) para investigar o status das implantações em escala. Para obter mais informações sobre como usar as propriedades do agente de IoT Edge para status, consulte [entender IOT Edge implantações para dispositivos únicos ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente de IoT Edge. |
| lastDesiredStatus. Code | Esse código de status refere-se às últimas propriedades desejadas vistas pelo agente de IoT Edge. Valores permitidos: `200` êxito, `400` Configuração inválida, `412` versão de esquema inválida, `417` as propriedades desejadas estão vazias, `500` com falha |
| lastDesiredStatus. Description | Descrição de texto do status |
| deviceHealth | `healthy` se o status de tempo de execução de todos os módulos for `running` ou `stopped`, `unhealthy` de outra forma |
| configurationHealth. {DeploymentId}. integridade | `healthy` se o status de tempo de execução de todos os módulos definidos pela implantação {DeploymentId} for `running` ou `stopped`, `unhealthy` de outra forma |
| Runtime. Platform. OS | Relatando o sistema operacional em execução no dispositivo |
| tempo de execução. plataforma. arquitetura | Relatando a arquitetura da CPU no dispositivo |
| systemModules. edgeAgent. runtimeStatus | O status relatado do agente de IoT Edge: {"em execução" \| "não íntegro"} |
| systemModules. edgeAgent. statusDescription | Descrição de texto do status relatado do agente de IoT Edge. |
| systemModules. edgeHub. runtimeStatus | Status do hub de IoT Edge: {"em execução" \| "parado" \| "falha" \| "retirada" \| "não íntegro"} |
| systemModules. edgeHub. statusDescription | Texto descrição do status do hub de IoT Edge se não estiver íntegro. |
| systemModules. edgeHub. exitCode | O código de saída relatado pelo contêiner do Hub IoT Edge se o contêiner for encerrado |
| systemModules. edgeHub. startTimeUtc | Hora em que IoT Edge Hub foi iniciado pela última vez |
| systemModules. edgeHub. lastExitTimeUtc | Hora em que o Hub de IoT Edge saiu pela última vez |
| systemModules. edgeHub. lastRestartTimeUtc | Hora em que IoT Edge Hub foi reiniciado pela última vez |
| systemModules. edgeHub. restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |
| módulos. {ModuleID}. runtimeStatus | Status do módulo: {"em execução" \| "parado" \| "falha" \| "retirada" \| "não íntegro"} |
| módulos. {ModuleID}. statusDescription | Texto descrição do status do módulo se não estiver íntegro. |
| módulos. {ModuleID}. exitCode | O código de saída relatado pelo contêiner do módulo se o contêiner for encerrado |
| módulos. {ModuleID}. startTimeUtc | Hora em que o módulo foi iniciado pela última vez |
| módulos. {ModuleID}. lastExitTimeUtc | Hora em que o módulo foi encerrado pela última vez |
| módulos. {ModuleID}. lastRestartTimeUtc | Hora em que o módulo foi reiniciado pela última vez |
| módulos. {ModuleID}. restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |

## <a name="edgehub-desired-properties"></a>Propriedades desejadas do EdgeHub

O módulo para o Hub de IoT Edge é chamado `$edgeHub` e coordena as comunicações entre o Hub de IoT Edge em execução em um dispositivo e Hub IoT. As propriedades desejadas são definidas ao aplicar um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | Descrição | Necessário no manifesto de implantação |
| -------- | ----------- | -------- |
| SchemaVersion | Deve ser "1,0" | Sim |
| circula. RouteName | Uma cadeia de caracteres que representa uma rota de Hub de IoT Edge. Para obter mais informações, consulte [declarar rotas](module-composition.md#declare-routes). | O elemento `routes` pode estar presente, mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que IoT Edge Hub mantém mensagens se desconectado dos pontos de extremidade de roteamento, seja o Hub IoT ou um módulo local. O valor pode ser qualquer inteiro positivo. | Sim |

## <a name="edgehub-reported-properties"></a>Propriedades relatadas do EdgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub de IoT Edge. |
| lastDesiredStatus. Code | O código de status referente às últimas propriedades desejadas vistas pelo hub de IoT Edge. Valores permitidos: `200` êxito, `400` Configuração inválida, `500` com falha |
| lastDesiredStatus. Description | Texto descrição do status. |
| clientes. {Device ou ModuleID}. status | O status de conectividade deste dispositivo ou módulo. Os valores possíveis {"Connected" \| "Disconnected"}. Somente as identidades de módulo podem estar no estado desconectado. Os dispositivos downstream que se conectam ao Hub IoT Edge aparecem somente quando conectado. |
| clientes. {Device ou ModuleID}. lastConnectTime | Última vez em que o dispositivo ou módulo está conectado. |
| clientes. {Device ou ModuleID}. lastDisconnectTime | Última vez em que o dispositivo ou módulo foi desconectado. |

## <a name="next-steps"></a>Passos seguintes

Para saber como usar essas propriedades para criar manifestos de implantação, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).
