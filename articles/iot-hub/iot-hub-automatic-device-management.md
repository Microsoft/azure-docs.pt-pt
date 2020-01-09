---
title: Gerenciamento automático de dispositivos em escala com o Hub IoT do Azure | Microsoft Docs
description: Usar configurações automáticas do Hub IoT do Azure para gerenciar vários dispositivos e módulos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429344"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gerenciamento automático de módulos e dispositivos IoT usando o portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivos no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas do gerenciamento de frotas de dispositivos grandes. Com o gerenciamento automático de dispositivos, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e, em seguida, permitir que o Hub IoT atualize os dispositivos quando eles entrarem no escopo. Essa atualização é feita usando uma _configuração automática de dispositivo_ ou _configuração automática de módulo_, que permite resumir a conclusão e a conformidade, lidar com a mesclagem e os conflitos e distribuir configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático de dispositivo funciona atualizando um conjunto de dispositivos gêmeos ou gêmeos de módulo com as propriedades desejadas e relatando um resumo baseado em Propriedades relatadas de entrelaçamento.  Ele introduz uma nova classe e um documento JSON denominado uma *configuração* que tem três partes:

* A **condição de destino** define o escopo do dispositivo gêmeos ou gêmeos do módulo a ser atualizado. A condição de destino é especificada como uma consulta em marcas de cima e/ou Propriedades relatadas.

* O **conteúdo de destino** define as propriedades desejadas a serem adicionadas ou atualizadas no dispositivo de destino gêmeos ou no módulo gêmeos. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterada.

* As **métricas** definem as contagens de Resumo de vários Estados de configuração, como **êxito**, **em andamento**e **erro**. As métricas personalizadas são especificadas como consultas em Propriedades relatadas de entrelaçamento.  As métricas do sistema são as métricas padrão que medem o status de atualização de entrelaçamento, como o número de gêmeos que são direcionadas e o número de gêmeos que foram atualizados com êxito.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

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

## <a name="create-a-configuration"></a>Criar uma configuração

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IOT**.

3. Selecione **Adicionar configuração de dispositivo** ou **Adicionar configuração de módulo**.

   ![Adicionar configuração de dispositivo ou configuração de módulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Há cinco etapas para criar uma configuração. As seções a seguir, percorra cada um deles. 

### <a name="name-and-label"></a>Nome e rótulo

1. Dê à sua configuração um nome exclusivo que tenha até 128 letras minúsculas. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione rótulos para ajudar a acompanhar suas configurações. Os rótulos são pares **nome**, **valor** que descrevem sua configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Avançar** para ir para a próxima etapa. 

### <a name="specify-settings"></a>Especificar configurações

Esta seção define o conteúdo a ser definido no dispositivo de destino ou no módulo gêmeos. Há duas entradas para cada conjunto de configurações. A primeira é o caminho de configuração, que é o caminho para a seção JSON dentro das propriedades desejadas de entrelaçamento que serão definidas.  O segundo é o conteúdo JSON a ser inserido nessa seção. 

Por exemplo, você pode definir o caminho de entrelaçamento para `properties.desired.chiller-water` e, em seguida, fornecer o seguinte conteúdo JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Definir o caminho e o conteúdo do entrelaçamento](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Você também pode definir configurações individuais especificando todo o caminho de conjunto de entrelaçamento e fornecendo o valor sem colchetes. Por exemplo, com o `properties.desired.chiller-water.temperature`de caminho de conjunto de entrelaçamento, defina o conteúdo como `66`. Em seguida, crie uma nova configuração de entrelaçamento para a propriedade de pressão. 

Se duas ou mais configurações tiverem como destino o mesmo caminho de entrelaçamento, o conteúdo da configuração de prioridade mais alta será aplicado (a prioridade é definida na etapa 4).

Se você quiser remover uma propriedade existente, especifique o valor da propriedade para `null`.

Você pode adicionar configurações adicionais selecionando **Adicionar dispositivo de configuração** ou **Adicionar configuração do módulo**.

### <a name="specify-metrics-optional"></a>Especificar métricas (opcional)

As métricas fornecem contagens de resumo dos vários Estados que um dispositivo ou módulo pode reportar depois de aplicar o conteúdo de configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas.

Cada configuração pode ter até cinco métricas personalizadas. 

1. Insira um nome para o **nome da métrica**.

2. Insira uma consulta para **critérios de métrica**.  A consulta baseia-se nas propriedades relatadas do dispositivo.  A métrica representa o número de linhas retornadas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Você pode incluir uma cláusula na qual a configuração foi aplicada, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se você estiver criando uma métrica para relatar os módulos configurados, selecione `moduleId` de `devices.modules`. Por exemplo:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivos de destino

Use a propriedade Tags de seu gêmeos para direcionar os dispositivos ou módulos específicos que devem receber essa configuração. Você também pode direcionar as propriedades relatadas de entrelaçar.

As configurações automáticas de dispositivo só podem direcionar marcas de dispositivo de destino e as configurações automáticas de módulo só podem ser direcionadas a marcas de módulo. 

Como várias configurações podem ter como destino o mesmo dispositivo ou módulo, cada configuração precisa de um número de prioridade. Se houver algum conflito, a configuração com a maior prioridade vence. 

1. Insira um número inteiro positivo para a **prioridade**de configuração. O valor numérico mais alto é considerado a prioridade mais alta. Se duas configurações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganha. 

2. Insira uma **condição de destino** para determinar quais dispositivos ou módulos serão direcionados a essa configuração. A condição se baseia em marcas de entrelaçamento ou em Propriedades relatadas de entrelaçamento e deve corresponder ao formato de expressão. 

   Para configuração automática do dispositivo, você pode especificar apenas a marca ou a propriedade relatada para destino. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. Você pode especificar `*` para direcionar todos os dispositivos. 
   
   Para configuração automática de módulo, use uma consulta para especificar marcas ou Propriedades relatadas dos módulos registrados para o Hub IoT. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`. O curinga não pode ser usado para direcionar todos os módulos. 

3. Selecione **seguinte** para avançar para a etapa final.

### <a name="review-configuration"></a>Examinar configuração

Examine as informações de configuração e, em seguida, selecione **Enviar**.

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Para exibir os detalhes de uma configuração e monitorar os dispositivos que o executam, use as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IOT**.

3. Inspecione a lista de configurações. Para cada configuração, você pode exibir os seguintes detalhes:

   * **ID** -o nome da configuração.

   * **Condição de destino** -a consulta usada para definir dispositivos ou módulos de destino.

   * **Priority** -o número de prioridade atribuído à configuração.

   * **Hora de criação** – o carimbo de data/hora de quando a configuração foi criada. Esse carimbo de data/hora é usado para quebrar ligações quando duas configurações têm a mesma prioridade. 

   * **Métricas do sistema** -métricas que são calculadas pelo Hub IOT e não podem ser personalizadas por desenvolvedores. Target especifica o número de gêmeos de dispositivo que correspondem à condição de destino. Aplica-se o número especificado de dispositivos gêmeos que foram modificados pela configuração, que podem incluir modificações parciais no caso de uma configuração separada e de prioridade mais alta também ter sido alterada. 

   * **Métricas personalizadas** -métricas que foram especificadas pelo desenvolvedor como consultas em Propriedades relatadas de entrelaçamento.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que você deseja monitorar.  

5. Inspecione os detalhes de configuração. Você pode usar guias para exibir detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição de destino** -os dispositivos ou módulos que correspondem à condição de destino. 

   * **Métricas** -uma lista de métricas de sistema e métricas personalizadas.  Você pode exibir uma lista de dispositivos ou módulos que são contados para cada métrica selecionando a métrica na lista suspensa e, em seguida, selecionando **exibir dispositivos** ou **Exibir módulos**.

   * Configurações de conjunto de **dispositivos** ou configurações de **módulo** de configuração de conjunto – as configurações de alto que são definidas pelo Configuration. 

   * **Rótulos de configuração** – pares chave-valor usados para descrever uma configuração.  Os rótulos não têm impacto sobre a funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos ou módulos de destino. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um Altova não atender à condição de destino antiga, mas atender à nova condição de destino e essa configuração for a prioridade mais alta para esse pressione, essa configuração será aplicada. 

* Se um "n" em execução nesta configuração não atender mais à condição de destino, as configurações da configuração serão removidas e o número de teleatualização será modificado pela próxima configuração de prioridade mais alta. 

* Se um "n" em execução nesta configuração não atender mais à condição de destino e não cumprir a condição de destino de nenhuma das outras configurações, as configurações da configuração serão removidas e nenhuma outra alteração será feita em. 

Para modificar uma configuração, use as seguintes etapas: 

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IOT**. 

3. Selecione a configuração que você deseja modificar. 

4. Efetue as atualizações para os seguintes campos: 

   * Condição de destino 
   * Etiquetas 
   * Prioridade 
   * Métricas

4. Selecione **Guardar**.

5. Siga as etapas em [monitorar uma configuração](#monitor-a-configuration) para observar a distribuição das alterações. 

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeos assume sua próxima configuração de prioridade mais alta. Se o dispositivo gêmeos não atender à condição de destino de nenhuma outra configuração, nenhuma outra definição será aplicada. 

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IOT**. 

3. Use a caixa de seleção para selecionar a configuração que você deseja excluir. 

4. Selecione **Eliminar**.

5. Um prompt pedirá que você confirme.

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
