---
title: Gestão de dispositivos automática à escala com o IoT Hub do Azure | Documentos da Microsoft
description: Utilize a gestão de automático de dispositivos do IoT Hub do Azure para atribuir uma configuração de vários dispositivos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 598bf82e375f472b2f723c3462ba7ba7b4d25fbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333658"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-portal"></a>Gestão automática de dispositivos de IoT em escala no portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Gerenciamento automático de dispositivos no IoT Hub do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de frotas de dispositivo grandes. Com a gestão de dispositivos automático, pode visar um conjunto de dispositivos com base nas respetivas propriedades, definir uma configuração desejada e, em seguida, permitir que o IoT Hub atualizar os dispositivos quando eles vão para o âmbito. Esta atualização é feita usando uma _configuração do dispositivo automática_, que permite que resumir conclusão e a conformidade, a mesclagem de identificador e a conflitos e implementar configurações numa abordagem faseada.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Funciona a gestão de dispositivos automático ao atualizar um conjunto de dispositivos duplos com as propriedades pretendidas e um resumo com base no dispositivo duplo de relatórios de propriedades comunicadas.  Ele introduz uma nova classe e o documento JSON chamado um *configuração* que possui três partes:

* O **condição de destino** define o âmbito dos dispositivos duplos para ser atualizado. A condição de destino é especificada como uma consulta nas etiquetas do dispositivo duplo e/ou propriedades comunicadas.

* O **conteúdo de destino** define as propriedades pretendidas para ser adicionados ou atualizados nos gémeos de dispositivo de destino. O conteúdo inclui um caminho para a secção de propriedades pretendidas para serem alterados.

* O **métricas** definem as contagens de resumidas de vários Estados de configuração, tais como **êxito**, **em curso**, e **erro**. Métricas personalizadas são especificadas como consultas no dispositivo duplo de propriedades comunicadas.  As métricas do sistema são as métricas de predefinição que medem o estado de atualização de duplo, como o número de dispositivos duplos, que são visados e o número de duplos foram atualizadas com êxito. 

## <a name="implement-device-twins-to-configure-devices"></a>Implementar dispositivos duplos para configurar dispositivos

Configurações de dispositivos automático requerem a utilização de dispositivos duplos para sincronizar o estado entre a cloud e dispositivos.  Consulte a [compreender e utilizar dispositivos duplos no IoT Hub](iot-hub-devguide-device-twins.md) para obter orientações sobre como utilizar dispositivos duplos.

## <a name="identify-devices-using-tags"></a>Identificar os dispositivos utilizando etiquetas

Antes de criar uma configuração, tem de especificar quais os dispositivos que quer afetar. O IoT Hub do Azure identifica dispositivos utilizando as etiquetas no dispositivo duplo. Cada dispositivo pode ter várias etiquetas e pode defini-las qualquer forma que faça sentido para a sua solução. Por exemplo, se gerir dispositivos em diferentes locais, adicione as seguintes etiquetas para um dispositivo duplo:

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

2. Selecione **configuração do dispositivo IoT**.

3. Selecione **Adicionar configuração**.

Existem cinco passos para criar uma configuração. As seções a seguir, percorra cada um deles. 

### <a name="name-and-label"></a>Nome e Etiqueta

1. Dê um nome exclusivo que é até 128 minúsculas da configuração. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione etiquetas para ajudar a controlar as configurações. As etiquetas são **Name**, **valor** pares que descrevem a configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **seguinte** para mover para o passo seguinte. 

### <a name="specify-settings"></a>Especificar Definições

Esta secção especifica o conteúdo de destino sejam definidas no gémeos de dispositivo de destino. Existem duas entradas para cada conjunto de definições. A primeira é o caminho de twin do dispositivo, o que é o caminho para a secção JSON nas propriedades pretendida do duplo que serão definidas.  O segundo é o conteúdo JSON a ser inserido nesta secção. Por exemplo, defina o caminho do dispositivo duplo e o conteúdo para o seguinte:

![Definir o caminho do dispositivo duplo e o conteúdo](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Também pode definir as configurações individuais especificando o caminho de todo o caminho do dispositivo duplo e o valor no conteúdo sem Retos. Por exemplo, definir o caminho do dispositivo duplo `properties.desired.chiller-water.temperature` e defina o conteúdo como `66`.

Se o mesmo caminho de Twin do dispositivo de destino de duas ou mais configurações, aplicará o conteúdo a partir da configuração de prioridade mais alta (a prioridade é definida na etapa 4).

Se quiser remover uma propriedade, especifique o valor da propriedade para `null`.

Pode adicionar definições adicionais, selecionando **Adicionar definição do dispositivo duplo**.

### <a name="specify-metrics-optional"></a>Especificar as métricas (opcionais)

Métricas fornecem contagens de resumidas dos diversos Estados em que um dispositivo pode comunicar de volta depois de aplicar o conteúdo de configuração. Por exemplo, pode criar uma métrica para definições alterações pendentes, uma métrica para erros e uma métrica para alterações de definições concluída com êxito.

1. Introduza um nome para **nome da métrica**.

2. Introduza uma consulta para **critérios de métrica**.  A consulta baseia-se no dispositivo duplo de propriedades comunicadas.  A métrica representa o número de linhas retornadas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Pode incluir uma cláusula foi aplicada a configuração, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

### <a name="target-devices"></a>Dispositivos de Destino

Utilize a propriedade de etiquetas de seus dispositivos duplos para os dispositivos específicos que devem receber esta configuração de destino.  Também pode visar dispositivos por dispositivo duplo de propriedades comunicadas.

Uma vez que várias configurações podem direcionar o mesmo dispositivo, deve dar a cada configuração de um número de prioridade. Se já houver um conflito, a configuração com a prioridade mais alta wins. 

1. Introduza um número inteiro positivo para a configuração **prioridade**. O maior valor numérico é considerado a prioridade mais alta. Se duas configurações de tem o mesmo número de prioridade, aquele que foi criado mais recentemente wins. 

2. Introduza um **condição de destino** para determinar quais os dispositivos que serão visados para esta configuração. A condição baseia-se nas etiquetas de twin do dispositivo ou o dispositivo duplo propriedades comunicadas e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. Pode especificar `*` para todos os dispositivos de destino.

3. Selecione **seguinte** para avançar para a etapa final.

### <a name="review-configuration"></a>Rever Configuração

Reveja as suas informações de configuração, em seguida, selecione **submeter**.

## <a name="monitor-a-configuration"></a>Uma configuração de monitor

Para ver os detalhes de uma configuração e monitorizar os dispositivos a executá-lo, utilize os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IoT**.

3. Inspecione a lista de configuração. Para cada configuração, pode ver os seguintes detalhes:

   * **ID** -o nome da configuração.

   * **Condição de destino** -a consulta usada para definir os dispositivos visados.

   * **Prioridade** -o número de prioridade atribuído à configuração.

   * **Hora de criação** -o carimbo de hora de quando a configuração foi criada. Este timestamp é utilizado para dividir os empates quando duas configurações têm a mesma prioridade. 

   * **As métricas do sistema** -métricas que são calculadas ao IoT Hub e não podem ser personalizadas pelos desenvolvedores. Direcionada Especifica o número de dispositivos duplos que correspondem à condição de destino. Aplica-se especificado o número de dispositivos duplos, que foram modificados pela configuração, que pode incluir modificações parciais no caso de uma configuração de separado de elevada prioridade também fez alterações. 

   * **Métricas personalizadas** -propriedades comunicadas de métricas que foram especificadas pelo desenvolvedor como consultas no dispositivo duplo.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que pretende monitorizar.  

5. Inspecione os detalhes de configuração. Pode utilizar os separadores para ver detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição de destino** -os dispositivos que correspondem à condição de destino. 

   * **Métricas** -uma lista de métricas do sistema e métricas personalizadas.  Pode ver uma lista de dispositivos que são contabilizados para cada uma, selecione a métrica na lista pendente e, em seguida, selecionando **ver dispositivos**.

   * **Definições do dispositivo duplo** -as definições de twin de dispositivos que estão definidas pela configuração. 

   * **Etiquetas de configuração** -pares chave-valor usados para descrever uma configuração.  Etiquetas não tem nenhum impacto na funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um dispositivo duplo não cumpre a condição de destino antigo, mas que atenda à nova condição de destino e esta configuração é a prioridade mais alta para esse dispositivo duplo, esta configuração é aplicada para o dispositivo duplo. 

* Se um dispositivo duplo já não cumpre a condição de destino, serão possível remover as definições da configuração e o dispositivo duplo será modificado pela configuração de prioridade mais alta seguinte. 

* Se um dispositivo duplo atualmente em execução esta configuração já não cumpre a condição de destino e não cumpre a condição de destino de quaisquer outras configurações, em seguida, as definições da configuração serão removidas e nenhuma outra alteração serão feitas no duplo. 

Para modificar uma configuração, utilize os seguintes passos: 

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IoT**. 

3. Selecione a configuração que pretende modificar. 

4. Efetue as atualizações para os seguintes campos: 

   * Condição de destino 
   * Etiquetas 
   * Prioridade 
   * Métricas

4. Selecione **Guardar**.

5. Siga os passos em [monitorizar uma configuração](#monitor-a-configuration) para ver as alterações a implementar. 

## <a name="delete-a-configuration"></a>Eliminar uma configuração

Quando elimina uma configuração, quaisquer dispositivos duplos assumem a seguinte configuração de prioridade mais alta. Se os dispositivos duplos não cumprem a condição de destino de qualquer outra configuração, não existem outras definições são aplicadas. 

1. Na [portal do Azure](https://portal.azure.com), aceda ao seu hub IoT. 

2. Selecione **configuração do dispositivo IoT**. 

3. Utilize a caixa de verificação para selecionar a configuração que pretende eliminar. 

4. Selecione **Eliminar**.

5. Uma linha de comandos irá pedir-lhe para confirmar.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a configurar e monitorizar dispositivos de IoT em escala. Siga estas ligações para saber mais sobre como gerir o IoT Hub do Azure:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar, utilizando o serviço de aprovisionamento de dispositivos do IoT Hub para ativar o aprovisionamento sem toque e just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
