---
title: Gestão automática de dispositivos em escala com Hub Azure IoT (CLI) [ Microsoft Docs
description: Utilize configurações automáticas Azure IoT Hub para gerir vários dispositivos ou módulos IoT
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 76728b99f1e9309294079d4df88e563054e780e7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767562"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Dispositivo automático IoT e gestão de módulos utilizando o Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos. Com a gestão automática do dispositivo, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando estes entrarem em alcance. Esta atualização é feita utilizando uma _configuração automática_ do dispositivo ou _configuração automática_do módulo, que permite resumir a conclusão e conformidade, lidar com a fusão e conflitos, e lançar configurações numa abordagem faseada.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A gestão automática do dispositivo funciona atualizando um conjunto de gémeos de dispositivoou gémeos módulos com propriedades desejadas e reportando um resumo baseado em propriedades reportadas de gémeos.  Introduz uma nova classe e documento JSON chamado *Configuração* que tem três partes:

* A **condição-alvo** define o âmbito dos gémeos do dispositivo ou gémeos módulos a serem atualizados. A condição-alvo é especificada como uma consulta nas etiquetas gémeas do dispositivo e/ou propriedades reportadas.

* O **conteúdo-alvo** define as propriedades desejadas a serem adicionadas ou atualizadas no dispositivo-alvo gémeos ou gémeos módulos. O conteúdo inclui um caminho para a secção das propriedades desejadas a ser alterada.

* As **métricas** definem as contagens sumárias de vários estados de configuração, tais como **Sucesso,** **Progresso**, e **Erro**. As métricas personalizadas são especificadas como consultas em propriedades reportadas gémeas.  As métricas do sistema são as métricas padrão que medem o estado da atualização gémea, como o número de gémeos que são alvo e o número de gémeos que foram atualizados com sucesso.

As configurações automáticas funcionam pela primeira vez pouco depois da configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas métricas são executadas sempre que a configuração automática corre.

## <a name="cli-prerequisites"></a>Pré-requisitos do CLI

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az –-version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 

* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementar gémeos

As configurações automáticas do dispositivo requerem a utilização de gémeos dispositivos para sincronizar o estado entre a nuvem e os dispositivos.  Para mais informações, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

As configurações automáticas do módulo requerem a utilização de gémeos módulos para sincronizar o estado entre a nuvem e os módulos. Para mais informações, consulte [Compreender e utilizar gémeos módulos no IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Use etiquetas para atingir gémeos

Antes de criar uma configuração, deve especificar quais os dispositivos ou módulos que pretende afetar. O Azure IoT Hub identifica dispositivos e utiliza etiquetas no dispositivo twin, e identifica módulos usando tags no módulo twin. Cada dispositivo ou módulos pode ter várias tags, e pode defini-las de qualquer forma que faça sentido para a sua solução. Por exemplo, se gerir dispositivos em diferentes locais, adicione as seguintes etiquetas a um dispositivo twin:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir o conteúdo e métricas do alvo

O conteúdo-alvo e as consultas métricas são especificados como documentos JSON que descrevem o dispositivo twin ou módulo twin propriedades desejadas para definir e reportar propriedades para medir.  Para criar uma configuração automática utilizando o Azure CLI, guarde o conteúdo e métricas do alvo localmente como ficheiros .txt. Utiliza os caminhos dos ficheiros numa secção posterior quando executa o comando para aplicar a configuração no seu dispositivo.

Aqui está uma amostra básica de conteúdo de destino para uma configuração automática do dispositivo:

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

As configurações automáticas do módulo comportam-se de forma muito semelhante, mas o seu alvo `moduleContent` em vez de `deviceContent`.

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

As consultas métricas para os módulos também são semelhantes `moduleId` `devices.modules`às consultas para dispositivos, mas seleciona para . Por exemplo: 

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

* --**config-id** - O nome da configuração que será criada no centro IoT. Dê à sua configuração um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

* --**etiquetas** - Adicione etiquetas para ajudar a rastrear a sua configuração. As etiquetas são Nome, pares de valor que descrevem a sua implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --**conteúdo** - Inline JSON ou caminho de ficheiro para o conteúdo do alvo a definir como propriedades duplas desejadas. 

* --**hub-name** - Nome do hub IoT no qual a configuração será criada. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando`az account set -s [subscription name]`

* --**condição do alvo** - Introduza uma condição-alvo para determinar quais os dispositivos ou módulos que serão alvo com esta configuração.Para a configuração automática do dispositivo, a condição baseia-se em etiquetas duplas do dispositivo ou propriedades gémeas do dispositivo e deve corresponder ao formato de expressão.Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`.Para a configuração automática do módulo, a condição baseia-se em etiquetas duplas de módulos ou propriedades duplas de módulos.. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioridade** - Um inteiro positivo. No caso de duas ou mais configurações serem direcionadas para o mesmo dispositivo ou módulo, aplicar-se-á a configuração com o valor numérico mais elevado para a Priority.

* --**métricas** - Filepath para as consultas métricas. As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode reportar após a aplicação do conteúdo de configuração. Por exemplo, pode criar uma métrica para alterações de definições pendentes, uma métrica para erros e uma métrica para alterações de configurações bem sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorize uma configuração

Utilize o seguinte comando para visualizar o conteúdo de uma configuração:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - O nome da configuração que existe no centro ioT.

* --**nome** do hub - Nome do hub IoT em que a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando`az account set -s [subscription name]`

Inspecione a configuração na janela de comando.A **metrics** propriedade métrica lista uma contagem para cada métrica que é avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de gémeos de dispositivo ou gémeos módulo sintetizadores no IoT Hub que correspondem à condição de alvo.

* **aplicadaCount** - Uma métrica do sistema especifica o número de dispositivos ou módulos que tiveram o conteúdo alvo aplicado.

* **A sua métrica personalizada** - Quaisquer métricas que tenha definido são métricas de utilizador.

Pode apresentar uma lista de IDs, IDs de módulos ou objetos para cada uma das métricas utilizando o seguinte comando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - O nome da implantação que existe no centro ioT.

* --**metric-id** - O nome da métrica para a qual pretende ver a lista de `appliedCount`IDs ou iDs de módulos, por exemplo.

* --**nome** do hub - Nome do centro IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição `az account set -s [subscription name]`desejada com o comando .

* --**tipo métrico** - O `system` tipo `user`métrico pode ser ou .  As métricas `targetedCount` `appliedCount`do sistema são e. Todas as outras métricas são métricas de utilizador.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando modifica uma configuração, as alterações replicam-se imediatamente para todos os dispositivos visados. 

Se atualizar a condição-alvo, ocorrem as seguintes atualizações:

* Se um gémeo não cumpriu a condição de alvo antiga, mas cumpre a nova condição-alvo e esta configuração é a maior prioridade para esse gémeo, então esta configuração é aplicada. 

* Se um gémeo atualmente executar esta configuração deixar de cumprir a condição de destino, as definições da configuração serão removidas e o gémeo será modificado pela próxima configuração de prioridade máxima. 

* Se um gémeo atualmente executar esta configuração já não satisfaz a condição-alvo e não satisfaz o estado-alvo de quaisquer outras configurações, então as definições da configuração serão removidas e não serão feitas outras alterações no twin. 

Utilize o seguinte comando para atualizar uma configuração:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - O nome da configuração que existe no centro ioT.

* --**nome** do hub - Nome do hub IoT em que a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição `az account set -s [subscription name]`desejada com o comando .

* --**conjunto** - Atualizar uma propriedade na configuração. Pode atualizar as seguintes propriedades:

    * targetCondition - por exemplo`targetCondition=tags.location.state='Oregon'`

    * rótulos 

    * prioridade

## <a name="delete-a-configuration"></a>Eliminar uma configuração

Ao eliminar uma configuração, quaisquer gémeos ou gémeos de módulos assumem a sua próxima configuração de prioridade máxima. Se os gémeos não cumprirem a condição-alvo de qualquer outra configuração, então não são aplicadas outras definições. 

Utilize o seguinte comando para eliminar uma configuração:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - O nome da configuração que existe no centro ioT.

* --**nome** do hub - Nome do hub IoT em que a configuração existe. O centro deve estar na subscrição atual. Mude para a subscrição `az account set -s [subscription name]`desejada com o comando .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar e monitorizar dispositivos IoT em escala. Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar a utilização do Serviço de Provisionamento de Dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
