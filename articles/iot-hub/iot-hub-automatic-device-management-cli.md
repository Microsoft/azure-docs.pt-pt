---
title: Gestão automática de dispositivos em escala com Azure IoT Hub (CLI) | Microsoft Docs
description: Utilize configurações automáticas do Azure IoT Hub para gerir vários dispositivos ou módulos IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545417"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gestão automática de módulos e dispositivos IoT com a CLI do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos. Com a gestão automática do dispositivo, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando eles entram no âmbito. Esta atualização é feita utilizando uma _configuração automática do dispositivo_ ou _configuração automática_ do módulo , que permite resumir a conclusão e conformidade, lidar com a fusão e conflitos e lançar configurações numa abordagem faseada.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A gestão automática do dispositivo funciona atualizando um conjunto de gémeos de dispositivos ou gémeos módulos com propriedades desejadas e reportando um resumo que é baseado em propriedades geminadas reportadas.  Introduz uma nova classe e documento JSON chamado *configuração* que tem três partes:

* A **condição alvo** define o âmbito de aplicação dos gémeos do dispositivo ou gémeos módulos a serem atualizados. A condição do alvo é especificada como uma consulta sobre etiquetas gémeas do dispositivo e/ou propriedades reportadas.

* O **conteúdo do alvo** define as propriedades desejadas a serem adicionadas ou atualizadas nos gémeos do dispositivo ou gémeos do módulo. O conteúdo inclui um caminho para a secção de propriedades desejadas a ser alterada.

* As **métricas** definem as contagens sumárias de vários estados de configuração, tais como **Sucesso,** **Progresso** e **Erro.** As métricas personalizadas são especificadas como consultas sobre propriedades geminadas reportadas.  As métricas do sistema são as métricas padrão que medem o estado de atualização dupla, como o número de gémeos que são alvo e o número de gémeos que foram atualizados com sucesso.

As configurações automáticas são executadas pela primeira vez pouco depois de a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

## <a name="cli-prerequisites"></a>Requisitos pré-requisitos do CLI

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure. 

* [Azure CLI](/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az –-version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 

* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementar gémeos

As configurações automáticas do dispositivo requerem a utilização de gémeos do dispositivo para sincronizar o estado entre a nuvem e os dispositivos.  Para obter mais informações, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub.](iot-hub-devguide-device-twins.md)

As configurações automáticas do módulo requerem a utilização de gémeos módulos para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, consulte [Compreender e utilizar gémeos módulos no IoT Hub.](iot-hub-devguide-module-twins.md)

## <a name="use-tags-to-target-twins"></a>Use etiquetas para atingir gémeos alvo

Antes de criar uma configuração, tem de especificar quais os dispositivos ou módulos que pretende afetar. O Azure IoT Hub identifica os dispositivos e usa etiquetas no dispositivo twin, e identifica módulos usando tags no módulo twin. Cada dispositivo ou módulo pode ter várias tags, e pode defini-las de qualquer forma que faça sentido para a sua solução. Por exemplo, se gerir dispositivos em diferentes locais, adicione as seguintes etiquetas a um dispositivo gémeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir o conteúdo e métricas-alvo

O conteúdo alvo e as consultas métricas são especificados como documentos JSON que descrevem as propriedades duplas ou módulos do dispositivo para definir e reportar propriedades para medir.  Para criar uma configuração automática utilizando o Azure CLI, guarde o conteúdo e métricas-alvo localmente como .txt ficheiros. Utilize os caminhos de ficheiro numa secção posterior quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está uma amostra básica de conteúdo alvo para uma configuração automática do dispositivo:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

As configurações automáticas do módulo comportam-se da mesma forma, mas o seu alvo `moduleContent` em vez de `deviceContent` .

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Aqui estão exemplos de consultas métricas:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

As consultas métricas para módulos também são semelhantes às consultas para dispositivos, mas seleciona para `moduleId` `devices.modules` . Por exemplo: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Criar uma configuração

Configura os dispositivos-alvo criando uma configuração que consiste no conteúdo e métricas do alvo. 

Utilize o seguinte comando para criar uma configuração:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - O nome da configuração que será criada no hub IoT. Dê à sua configuração um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` .

* --**etiquetas** - Adicione etiquetas para ajudar a rastrear a sua configuração. As etiquetas são Nome, pares de valor que descrevem a sua implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --**conteúdo** - Inline JSON ou caminho de arquivo para o conteúdo alvo a definir como propriedades duplas desejadas. 

* --**nome do hub** - Nome do hub IoT no qual a configuração será criada. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]`

* --**estado-alvo** - Introduza uma condição de destino para determinar quais os dispositivos ou módulos que serão alvo com esta configuração. Para a configuração automática do dispositivo, a condição baseia-se em etiquetas gémeas do dispositivo ou no dispositivo que as propriedades desejadas e devem corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. Para a configuração automática do módulo, a condição baseia-se em etiquetas gémeas do módulo ou em propriedades duplas do módulo. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioridade** - Um número inteiro positivo. No caso de duas ou mais configurações serem direcionadas para o mesmo dispositivo ou módulo, aplicar-se-á a configuração com o valor numérico mais elevado de Prioridade.

* --**métricas** - Filepath para as consultas métricas. As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode reportar após a aplicação do conteúdo de configuração. Por exemplo, pode criar uma métrica para alterações pendentes de configurações, uma métrica de erros e uma métrica para alterações de definições bem sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorize uma configuração

Utilize o seguinte comando para exibir o conteúdo de uma configuração:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - O nome da configuração que existe no hub IoT.

* --**nome do hub** - Nome do hub IoT no qual a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]`

Inspecione a configuração na janela de comando. A propriedade **métrica** lista uma contagem para cada métrica que é avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de gémeos do dispositivo ou gémeos módulos no IoT Hub que correspondem à condição de alvo.

* **appliedCount** - Uma métrica do sistema especifica o número de dispositivos ou módulos que tiveram o conteúdo alvo aplicado.

* **A sua métrica personalizada** - Quaisquer métricas que definiu são métricas do utilizador.

Pode apresentar uma lista de IDs do dispositivo, IDs de módulos ou objetos para cada uma das métricas utilizando o seguinte comando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - O nome da implantação que existe no hub IoT.

* --**id métrica** - O nome da métrica para a qual pretende ver a lista de IDs do dispositivo ou IDs de módulos, por exemplo `appliedCount` .

* --**nome do hub** - Nome do hub IoT no qual a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]` .

* --**tipo métrico** - Tipo métrico pode ser `system` ou `user` .  As métricas do sistema são `targetedCount` `appliedCount` e. Todas as outras métricas são métricas do utilizador.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando modifica uma configuração, as alterações replicam-se imediatamente em todos os dispositivos direcionados. 

Se atualizar a condição do alvo, ocorrem as seguintes atualizações:

* Se um gémeo não cumpriu a antiga condição de alvo, mas cumpre a nova condição alvo e esta configuração é a maior prioridade para esse gémeo, então esta configuração é aplicada. 

* Se um gémeo atualmente em execução esta configuração deixar de cumprir a condição alvo, as definições da configuração serão removidas e o gémeo será modificado pela próxima configuração de maior prioridade. 

* Se um gémeo atualmente em execução esta configuração deixar de cumprir a condição de destino e não cumprir a condição alvo de quaisquer outras configurações, então as definições da configuração serão removidas e nenhuma outra alteração será feita no twin. 

Utilize o seguinte comando para atualizar uma configuração:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - O nome da configuração que existe no hub IoT.

* --**nome do hub** - Nome do hub IoT no qual a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]` .

* --**set** - Atualizar uma propriedade na configuração. Pode atualizar as seguintes propriedades:

    * targetCondição - por exemplo `targetCondition=tags.location.state='Oregon'`

    * rótulos 

    * prioridade

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando elimina uma configuração, qualquer dispositivo gémeos ou gémeos módulos assume a sua próxima configuração de maior prioridade. Se os gémeos não cumprirem a condição alvo de qualquer outra configuração, não são aplicadas outras definições. 

Utilize o seguinte comando para eliminar uma configuração:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - O nome da configuração que existe no hub IoT.

* --**nome do hub** - Nome do hub IoT no qual a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]` .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar e monitorizar dispositivos IoT em escala. Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Monitorize o seu hub IoT](monitor-iot-hub.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)

Para explorar utilizando o serviço de provisionamento de dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/index.yml)