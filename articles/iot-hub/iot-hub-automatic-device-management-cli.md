---
title: Gerenciamento automático de dispositivos em escala com o Hub IoT do Azure (CLI) | Microsoft Docs
description: Usar configurações automáticas do Hub IoT do Azure para gerenciar vários dispositivos ou módulos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 9a7e2d9874f049000dadcb3e46cccb2202b53698
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429292"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gerenciamento automático de módulos e dispositivos IoT usando o CLI do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivos no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas do gerenciamento de frotas de dispositivos grandes. Com o gerenciamento automático de dispositivos, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e, em seguida, permitir que o Hub IoT atualize os dispositivos quando eles entrarem no escopo. Essa atualização é feita usando uma _configuração automática de dispositivo_ ou _configuração automática de módulo_, que permite resumir a conclusão e a conformidade, lidar com a mesclagem e os conflitos e distribuir configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático de dispositivo funciona atualizando um conjunto de dispositivos gêmeos ou gêmeos de módulo com as propriedades desejadas e relatando um resumo baseado em Propriedades relatadas de entrelaçamento.  Ele introduz uma nova classe e um documento JSON denominado uma *configuração* que tem três partes:

* A **condição de destino** define o escopo do dispositivo gêmeos ou gêmeos do módulo a ser atualizado. A condição de destino é especificada como uma consulta em marcas de dispositivo e/ou Propriedades relatadas.

* O **conteúdo de destino** define as propriedades desejadas a serem adicionadas ou atualizadas no dispositivo de destino gêmeos ou no módulo gêmeos. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterada.

* As **métricas** definem as contagens de Resumo de vários Estados de configuração, como **êxito**, **em andamento**e **erro**. As métricas personalizadas são especificadas como consultas em Propriedades relatadas de entrelaçamento.  As métricas do sistema são as métricas padrão que medem o status de atualização de entrelaçamento, como o número de gêmeos que são direcionadas e o número de gêmeos que foram atualizados com êxito.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure. 
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –-version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. 
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-twins"></a>Implementar gêmeos

As configurações automáticas de dispositivo exigem o uso do dispositivo gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Para obter mais informações, veja [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).

As configurações automáticas de módulo exigem o uso do módulo gêmeos para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, consulte [entender e usar o módulo gêmeos no Hub IOT](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Usar marcas para gêmeos de destino

Antes de criar uma configuração, você deve especificar quais dispositivos ou módulos você deseja afetar. O Hub IoT do Azure identifica dispositivos e usando marcas no dispositivo e identifica os módulos usando marcas no módulo. Cada dispositivo ou módulo pode ter várias marcas e você pode defini-las de qualquer maneira que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, adicione as seguintes marcas a um dispositivo.

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir o conteúdo e as métricas de destino

As consultas de conteúdo e métrica de destino são especificadas como documentos JSON que descrevem as propriedades desejadas do dispositivo ou do módulo "..." para definir e relatar Propriedades a serem medidas.  Para criar uma configuração automática usando CLI do Azure, salve o conteúdo e as métricas de destino localmente como arquivos. txt. Você usará os caminhos de arquivo em uma seção posterior quando executar o comando para aplicar a configuração ao seu dispositivo.

Aqui está um exemplo de conteúdo de destino básico para uma configuração de dispositivo automática:

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

As configurações automáticas de módulo se comportam de forma semelhante, mas você tem como destino `moduleContent` em vez de `deviceContent`

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

Aqui estão exemplos de consultas de métrica:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

As consultas de métricas para módulos também são semelhantes às consultas de dispositivos, mas você seleciona para `moduleId` de `devices.modules`. Por exemplo: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Criar uma configuração

Você configura os dispositivos de destino criando uma configuração que consiste no conteúdo e nas métricas de destino. 

Use o seguinte comando para criar uma configuração:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** -o nome da configuração que será criada no Hub IOT. Dê à sua configuração um nome exclusivo que tenha até 128 letras minúsculas. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.

* **Rótulos** de --– adicione rótulos para ajudar a acompanhar sua configuração. As etiquetas são pares de valor que descrevem a implementação, nome de. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --o caminho de arquivo ou JSON embutido em **conteúdo** para o conteúdo de destino a ser definido como propriedades desejadas. 

* --**Hub-** nome-nome do Hub IOT no qual a configuração será criada. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

* --**target-Condition** -Insira uma condição de destino para determinar quais dispositivos ou módulos serão direcionados a essa configuração. Para configuração automática de dispositivo, a condição é baseada nas marcas de dispositivo ou nas propriedades de dispositivo de dispositivos de entrelaçamento e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`. Para a configuração automática de módulo, a condição é baseada nas marcas de módulo ou propriedades de módulo de entrelaçamento. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* **prioridade** de ---um inteiro positivo. Caso duas ou mais configurações sejam destinadas ao mesmo dispositivo ou módulo, a configuração com o maior valor numérico para prioridade será aplicada.

* **métricas** de ---filePath para as consultas de métrica. As métricas fornecem contagens de resumo dos vários Estados que um dispositivo ou módulo pode reportar depois de aplicar o conteúdo de configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Use o seguinte comando para exibir o conteúdo de uma configuração:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** -o nome da configuração existente no Hub IOT.

* --**Hub-** nome-nome do Hub IOT no qual a configuração existe. O hub tem de ser na subscrição atual. Mude para a subscrição pretendida com o comando `az account set -s [subscription name]`

Inspecione a configuração na janela de comando. A propriedade de **métricas** lista uma contagem de cada métrica que é avaliada por cada Hub:

* **targetedCount** -uma métrica do sistema que especifica o número de dispositivos gêmeos ou do módulo gêmeos no Hub IOT que correspondem à condição de destino.

* **appliedCount** -uma métrica do sistema especifica o número de dispositivos ou módulos que tiveram o conteúdo de destino aplicado.

* **Sua métrica personalizada** -quaisquer métricas que você definiu são métricas de usuário.

Você pode mostrar uma lista de IDs de dispositivo, IDs de módulo ou objetos para cada uma das métricas usando o seguinte comando:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** -o nome da implantação que existe no Hub IOT.

* --**Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo ou IDs de módulo, por exemplo `appliedCount`.

* --**Hub-** nome-nome do Hub IOT no qual a implantação existe. O hub tem de ser na subscrição atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --tipo de métrica de **tipo** de métrica pode ser `system` ou `user`.  As métricas do sistema são `targetedCount` e `appliedCount`. Todas as outras métricas são métricas de usuário.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos de destino. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um Altova não atender à condição de destino antiga, mas atender à nova condição de destino e essa configuração for a prioridade mais alta para esse pressione, essa configuração será aplicada. 

* Se um "n" em execução nesta configuração não atender mais à condição de destino, as configurações da configuração serão removidas e o número de teleatualização será modificado pela próxima configuração de prioridade mais alta. 

* Se um "n" em execução nesta configuração não atender mais à condição de destino e não cumprir a condição de destino de nenhuma das outras configurações, as configurações da configuração serão removidas e nenhuma outra alteração será feita em. 

Use o seguinte comando para atualizar uma configuração:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** -o nome da configuração existente no Hub IOT.

* --**Hub-** nome-nome do Hub IOT no qual a configuração existe. O hub tem de ser na subscrição atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**set** – atualizar uma propriedade na configuração. Pode atualizar as seguintes propriedades:

    * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`

    * etiquetas 

    * prioridade

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeos ou módulo gêmeos assume sua próxima configuração de prioridade mais alta. Se gêmeos não atender à condição de destino de nenhuma outra configuração, nenhuma outra definição será aplicada. 

Use o seguinte comando para excluir uma configuração:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** -o nome da configuração existente no Hub IOT.

* --**Hub-** nome-nome do Hub IOT no qual a configuração existe. O hub tem de ser na subscrição atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a configurar e monitorar dispositivos IoT em escala. Siga estes links para saber mais sobre como gerenciar o Hub IoT do Azure:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar o uso do serviço de provisionamento de dispositivos no Hub IoT para habilitar o provisionamento sem toque e Just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
