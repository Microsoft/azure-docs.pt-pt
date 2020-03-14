---
title: Gestão automática de dispositivos em escala com o Hub Azure IoT  Microsoft Docs
description: Utilize configurações automáticas Azure IoT Hub para gerir vários dispositivos e módulos IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271307"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Dispositivo ioT automático e gestão de módulos utilizando o portal Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos. Com a gestão automática do dispositivo, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando estes entrarem em alcance. Esta atualização é feita utilizando uma _configuração automática_ do dispositivo ou _configuração automática_do módulo, que permite resumir a conclusão e conformidade, lidar com a fusão e conflitos, e lançar configurações numa abordagem faseada.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A gestão automática do dispositivo funciona atualizando um conjunto de gémeos de dispositivoou gémeos módulos com propriedades desejadas e reportando um resumo baseado em propriedades reportadas de gémeos.  Introduz uma nova classe e documento JSON chamado *Configuração* que tem três partes:

* A **condição-alvo** define o âmbito dos gémeos do dispositivo ou gémeos módulos a serem atualizados. A condição-alvo é especificada como uma consulta em etiquetas duplas e/ou propriedades reportadas.

* O **conteúdo-alvo** define as propriedades desejadas a serem adicionadas ou atualizadas no dispositivo-alvo gémeos ou gémeos módulos. O conteúdo inclui um caminho para a secção das propriedades desejadas a ser alterada.

* As **métricas** definem as contagens sumárias de vários estados de configuração, tais como **Sucesso,** **Progresso**, e **Erro**. As métricas personalizadas são especificadas como consultas em propriedades reportadas gémeas.  As métricas do sistema são as métricas padrão que medem o estado da atualização gémea, como o número de gémeos que são alvo e o número de gémeos que foram atualizados com sucesso.

As configurações automáticas funcionam pela primeira vez pouco depois da configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas métricas são executadas sempre que a configuração automática corre.

## <a name="implement-twins"></a>Implementar gémeos

As configurações automáticas do dispositivo requerem a utilização de gémeos dispositivos para sincronizar o estado entre a nuvem e os dispositivos.  Para obter mais informações, veja [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).

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

## <a name="create-a-configuration"></a>Criar uma configuração

1. No [portal Azure,](https://portal.azure.com)vá ao seu centro ioT. 

2. Selecione a configuração do **dispositivo IoT**.

3. **Selecione configuração do dispositivo de adicionar** ou adicionar **configuração**do módulo .

   ![Adicionar configuração do dispositivo ou do módulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Há cinco passos para criar uma configuração. As seções a seguir, percorra cada um deles. 

### <a name="name-and-label"></a>Nome e Etiqueta

1. Dê à sua configuração um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione etiquetas para ajudar a rastrear as suas configurações. As etiquetas são **Nome**, **pares de valor** que descrevem a sua configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Next** para passar para o próximo passo. 

### <a name="specify-settings"></a>Especificar Definições

Esta secção define o conteúdo a definir em dispositivos ou gémeos de módulos direcionados. Existem duas inputs para cada conjunto de definições. O primeiro é o caminho gémeo, que é o caminho para a secção JSON dentro das propriedades gémeas que serão definidas.  A segunda é o conteúdo jSON a ser inserido nessa secção. 

Por exemplo, pode definir o caminho gémeo para `properties.desired.chiller-water` e, em seguida, fornecer o seguinte conteúdo JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Desloque o caminho e o conteúdo gémeos](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Também pode definir configurações individuais especificando todo o caminho gémeo e fornecendo o valor sem parênteses. Por exemplo, com o caminho gémeo `properties.desired.chiller-water.temperature`, coloque o conteúdo para `66`. Em seguida, crie um novo cenário gémeo para a propriedade de pressão. 

Se duas ou mais configurações visarem o mesmo caminho gémeo, o conteúdo da configuração de prioridade mais alta aplicar-se-á (a prioridade é definida no passo 4).

Se desejar remover um imóvel existente, especifique o valor da propriedade para `null`.

Pode adicionar configurações adicionais selecionando a **definição de twin do dispositivo de adição** ou adicionar a **definição twin do módulo**.

### <a name="specify-metrics-optional"></a>Especificar Métricas (opcional)

As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode reportar após a aplicação do conteúdo de configuração. Por exemplo, pode criar uma métrica para alterações de definições pendentes, uma métrica para erros e uma métrica para alterações de configurações bem sucedidas.

Cada configuração pode ter até cinco métricas personalizadas. 

1. Introduza um nome para **Nome Métrico**.

2. Insira uma consulta para **critérios métricos**.  A consulta baseia-se em propriedades reportadas por gémeos dispositivos.  A métrica representa o número de linhas devolvidas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Pode incluir uma cláusula de que a configuração foi aplicada, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se estiver a construir uma métrica para reportar módulos configurados, selecione `moduleId` a partir de `devices.modules`. Por exemplo:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivos-alvo

Utilize a propriedade de tags dos seus gémeos para direcionar os dispositivos ou módulos específicos que devem receber esta configuração. Também pode visar propriedades reportadas de gémeos.

As configurações automáticas do dispositivo só podem visar duas etiquetas do dispositivo, e as configurações automáticas do módulo só podem visar etiquetas duplas do módulo. 

Uma vez que várias configurações podem visar o mesmo dispositivo ou módulo, cada configuração precisa de um número prioritário. Se houver um conflito, a configuração com a maior prioridade ganha. 

1. Introduza um inteiro positivo para a configuração **Prioridade**. O valor numérico mais elevado é considerado a maior prioridade. Se duas configurações têm o mesmo número prioritário, a que foi criada mais recentemente ganha. 

2. Introduza uma **condição de Destino** para determinar quais os dispositivos ou módulos que serão direcionados com esta configuração. A condição baseia-se em etiquetas duplas ou propriedades reportadas duplas e deve corresponder ao formato de expressão. 

   Para a configuração automática do dispositivo, pode especificar apenas a etiqueta ou a propriedade reportada ao alvo. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. Pode especificar `*` para direcionar todos os dispositivos. 
   
   Para a configuração automática do módulo, utilize uma consulta para especificar etiquetas ou propriedades reportadas dos módulos registados no centro IoT. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`. O wildcard não pode ser usado para direcionar todos os módulos. 

3. Selecione **Next** para passar ao passo final.

### <a name="review-configuration"></a>Configuração de revisão

Reveja as informações de configuração e, em seguida, selecione **Submeter**.

## <a name="monitor-a-configuration"></a>Monitorize uma configuração

Para ver os detalhes de uma configuração e monitorizar os dispositivos que a executam, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)vá ao seu centro ioT. 

2. Selecione a configuração do **dispositivo IoT**.

3. Inspecione a lista de configuração. Para cada configuração, pode ver os seguintes detalhes:

   * **ID** - o nome da configuração.

   * **Condição** do alvo - a consulta usada para definir dispositivos ou módulos direcionados.

   * **Prioridade** - o número prioritário atribuído à configuração.

   * Tempo de **criação** - o carimbo de tempo a partir de quando a configuração foi criada. Este carimbo de tempo é usado para quebrar laços quando duas configurações têm a mesma prioridade. 

   * **Métricas** do sistema - métricas que são calculadas pelo IoT Hub e não podem ser personalizadas pelos desenvolvedores. O alvo especifica o número de gémeos dispositivoque correspondem à condição do alvo. Aplica-se o número de gémeos dispositivoque foram modificados pela configuração, que podem incluir modificações parciais no caso de uma configuração de prioridade mais alta também ter alterado. 

   * **Métricas personalizadas** - métricas que foram especificadas pelo desenvolvedor como consultas contra propriedades reportadas gémeas.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que pretende monitorizar.  

5. Inspecione os detalhes da configuração. Pode utilizar separadores para visualizar detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição do alvo** - os dispositivos ou módulos que correspondem à condição de destino. 

   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  Pode visualizar uma lista de dispositivos ou módulos que são contados para cada métrica selecionando a métrica no drop-down e, em seguida, selecionando Dispositivos de **Visualização** ou **Módulos de Visualização**.

   * **Configurações gémeas** do dispositivo ou **definições gémeas** do módulo - as definições gémeas definidas pela configuração. 

   * **Etiquetas de configuração** - pares de valor-chave usados para descrever uma configuração.  As etiquetas não têm impacto na funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando modifica uma configuração, as alterações replicam-se imediatamente para todos os dispositivos ou módulos direcionados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:

* Se um gémeo não cumpriu a condição de alvo antiga, mas cumpre a nova condição-alvo e esta configuração é a maior prioridade para esse gémeo, então esta configuração é aplicada. 

* Se um gémeo atualmente executar esta configuração deixar de cumprir a condição de destino, as definições da configuração serão removidas e o gémeo será modificado pela próxima configuração de prioridade máxima. 

* Se um gémeo atualmente executar esta configuração já não satisfaz a condição-alvo e não satisfaz o estado-alvo de quaisquer outras configurações, então as definições da configuração serão removidas e não serão feitas outras alterações no twin. 

Para modificar uma configuração, utilize os seguintes passos: 

1. No [portal Azure,](https://portal.azure.com)vá ao seu centro ioT. 

2. Selecione a configuração do **dispositivo IoT**. 

3. Selecione a configuração que pretende modificar. 

4. Efetue as atualizações para os seguintes campos: 

   * Condição de destino 
   * Etiquetas 
   * Priority 
   * Métricas

4. Selecione **Guardar**.

5. Siga os passos no [Monitor uma configuração](#monitor-a-configuration) para ver as alterações a sair. 

## <a name="delete-a-configuration"></a>Eliminar uma configuração

Ao eliminar uma configuração, qualquer dispositivo gémeos assume a sua próxima configuração de prioridade máxima. Se os gémeos do dispositivo não cumprirem o estado-alvo de qualquer outra configuração, então não são aplicadas outras definições. 

1. No [portal Azure,](https://portal.azure.com)vá ao seu centro ioT. 

2. Selecione a configuração do **dispositivo IoT**. 

3. Utilize a caixa de verificação para selecionar a configuração que pretende eliminar. 

4. Selecione **Eliminar**.

5. Um pedido vai pedir-lhe para confirmar.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a configurar e monitorizar dispositivos IoT em escala. Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementação de IA para dispositivos de borda com Borda Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar a utilização do Serviço de Provisionamento de Dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
