---
title: Gestão automática de dispositivos em escala com Azure IoT Hub Microsoft Docs
description: Utilize configurações automáticas do Azure IoT Hub para gerir vários dispositivos e módulos IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545400"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gestão automática de módulos e dispositivos IoT no portal do Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos. Com a gestão automática do dispositivo, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando eles entram no âmbito. Esta atualização é feita utilizando uma _configuração automática do dispositivo_ ou _configuração automática_ do módulo , que permite resumir a conclusão e conformidade, lidar com a fusão e conflitos e lançar configurações numa abordagem faseada.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A gestão automática do dispositivo funciona atualizando um conjunto de gémeos de dispositivos ou gémeos módulos com propriedades desejadas e reportando um resumo que é baseado em propriedades geminadas reportadas.  Introduz uma nova classe e documento JSON chamado *configuração* que tem três partes:

* A **condição alvo** define o âmbito de aplicação dos gémeos do dispositivo ou gémeos módulos a serem atualizados. A condição do alvo é especificada como uma consulta sobre etiquetas gémeas e/ou propriedades reportadas.

* O **conteúdo do alvo** define as propriedades desejadas a serem adicionadas ou atualizadas nos gémeos do dispositivo ou gémeos do módulo. O conteúdo inclui um caminho para a secção de propriedades desejadas a ser alterada.

* As **métricas** definem as contagens sumárias de vários estados de configuração, tais como **Sucesso,** **Progresso** e **Erro.** As métricas personalizadas são especificadas como consultas sobre propriedades geminadas reportadas.  As métricas do sistema são as métricas padrão que medem o estado de atualização dupla, como o número de gémeos que são alvo e o número de gémeos que foram atualizados com sucesso.

As configurações automáticas são executadas pela primeira vez pouco depois de a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas de métricas são executadas cada vez que a configuração automática é executada.

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

## <a name="create-a-configuration"></a>Criar uma configuração

1. No [portal Azure,](https://portal.azure.com)vá ao seu hub IoT. 

2. Selecione **a configuração do dispositivo IoT** .

3. **Selecione Adicionar a configuração do dispositivo** ou adicionar a **configuração do módulo** .

   ![Adicionar configuração de dispositivo ou configuração de módulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Há cinco passos para criar uma configuração. As seguintes secções atravessam cada uma delas. 

### <a name="name-and-label"></a>Nome e Etiqueta

1. Dê à sua configuração um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` .

2. Adicione etiquetas para ajudar a rastrear as suas configurações. As etiquetas são **Nome,** **Pares de valor** que descrevem a sua configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Seguinte** para passar para o próximo passo. 

### <a name="specify-settings"></a>Especificar Definições

Esta secção define o conteúdo a definir em gémeos de dispositivo ou módulos direcionados. Existem duas entradas para cada conjunto de definições. O primeiro é o caminho gémeo, que é o caminho para a secção JSON dentro das propriedades duplas desejadas que serão definidas.  A segunda é o conteúdo JSON a ser inserido nessa secção. 

Por exemplo, pode definir o caminho gémeo para `properties.desired.chiller-water` e, em seguida, fornecer o seguinte conteúdo JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Definir o caminho e o conteúdo gémeos](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Também pode definir as definições individuais especificando todo o caminho gémeo e fornecendo o valor sem parênteses. Por exemplo, com o caminho `properties.desired.chiller-water.temperature` gémeo, definir o conteúdo para `66` . Em seguida, crie uma nova configuração twin para a propriedade de pressão. 

Se duas ou mais configurações direcionarem o mesmo caminho duplo, aplicar-se-á o conteúdo da configuração de maior prioridade (a prioridade é definida no passo 4).

Se desejar remover um imóvel existente, especifique o valor da propriedade para `null` .

Pode adicionar definições adicionais selecionando a **definição de twin do dispositivo de adicionar** ou adicionar a **definição de twin do módulo** .

### <a name="specify-metrics-optional"></a>Especificar métricas (opcional)

As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode reportar após a aplicação do conteúdo de configuração. Por exemplo, pode criar uma métrica para alterações pendentes de configurações, uma métrica de erros e uma métrica para alterações de definições bem sucedidas.

Cada configuração pode ter até cinco métricas personalizadas. 

1. Insira um nome para **Nome Métrico** .

2. Introduza uma consulta para **critérios métricos** .  A consulta baseia-se em propriedades reportadas por twin do dispositivo.  A métrica representa o número de linhas devolvidas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Pode incluir uma cláusula que a configuração foi aplicada, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se estiver a construir uma métrica para reportar em módulos configurados, selecione `moduleId` a partir de `devices.modules` . Por exemplo:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivos de Destino

Utilize a propriedade tags dos seus gémeos para direcionar os dispositivos ou módulos específicos que devem receber esta configuração. Também pode visar propriedades geminais reportadas.

As configurações automáticas do dispositivo só podem visar as duas tags do dispositivo, e as configurações automáticas do módulo só podem visar as duas tags do módulo. 

Uma vez que várias configurações podem visar o mesmo dispositivo ou módulo, cada configuração precisa de um número prioritário. Se houver algum conflito, a configuração com a maior prioridade ganha. 

1. Introduza um número inteiro positivo para a configuração **Prioridade** . O valor numérico mais elevado é considerado a maior prioridade. Se duas configurações têm o mesmo número de prioridade, a que foi criada mais recentemente ganha. 

2. Introduza uma **condição de Destino** para determinar quais os dispositivos ou módulos que serão alvo com esta configuração. A condição baseia-se em etiquetas gémeas ou propriedades geminais reportadas e deve corresponder ao formato de expressão. 

   Para a configuração automática do dispositivo, pode especificar apenas a etiqueta ou a propriedade reportada para o alvo. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. Pode especificar `*` para direcionar todos os dispositivos. 
   
   Para a configuração automática do módulo, utilize uma consulta para especificar tags ou propriedades reportadas dos módulos registados no hub IoT. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`. O wildcard não pode ser usado para direcionar todos os módulos. 

3. Selecione **Seguinte** para passar ao passo final.

### <a name="review-configuration"></a>Configuração de revisão

Reveja as informações de configuração e, em seguida, **selecione Enviar por isso** .

## <a name="monitor-a-configuration"></a>Monitorize uma configuração

Para ver os detalhes de uma configuração e monitorizar os dispositivos que o executam, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)vá ao seu hub IoT. 

2. Selecione **a configuração do dispositivo IoT** .

3. Inspecione a lista de configurações. Para cada configuração, pode ver os seguintes detalhes:

   * **ID** - o nome da configuração.

   * **Condição do alvo** - a consulta utilizada para definir dispositivos ou módulos direcionados.

   * **Prioridade** - o número prioritário atribuído à configuração.

   * **Tempo de criação** - a data de tempo a partir do momento em que a configuração foi criada. Esta estampta de tempo é usada para quebrar laços quando duas configurações têm a mesma prioridade. 

   * **Métricas do sistema** - métricas que são calculadas pelo IoT Hub e não podem ser personalizadas pelos desenvolvedores. O alvo especifica o número de gémeos do dispositivo que correspondem à condição do alvo. Aplica-se especificado o número de gémeos do dispositivo que foram modificados pela configuração, o que pode incluir modificações parciais no caso de uma configuração separada e de maior prioridade também ter feito alterações. 

   * **Métricas personalizadas** - métricas que foram especificadas pelo desenvolvedor como consultas contra propriedades geminadas reportadas.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que pretende monitorizar.  

5. Inspecione os detalhes da configuração. Pode utilizar separadores para visualizar detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição do alvo** - os dispositivos ou módulos que correspondem à condição alvo. 

   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  Pode ver uma lista de dispositivos ou módulos que são contados para cada métrica selecionando a métrica no drop-down e, em seguida, selecionando Dispositivos de **Visualização** ou **Módulos de Visualização** .

   * **Configurações gémeas do dispositivo** ou **configurações gémeas** do módulo - as definições duplas que são definidas pela configuração. 

   * **Etiquetas de configuração** - pares de valor-chave usados para descrever uma configuração.  As etiquetas não têm impacto na funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando modifica uma configuração, as alterações replicam-se imediatamente em todos os dispositivos ou módulos direcionados. 

Se atualizar a condição do alvo, ocorrem as seguintes atualizações:

* Se um gémeo não cumpriu a antiga condição de alvo, mas cumpre a nova condição alvo e esta configuração é a maior prioridade para esse gémeo, então esta configuração é aplicada. 

* Se um gémeo atualmente em execução esta configuração deixar de cumprir a condição alvo, as definições da configuração serão removidas e o gémeo será modificado pela próxima configuração de maior prioridade. 

* Se um gémeo atualmente em execução esta configuração deixar de cumprir a condição de destino e não cumprir a condição alvo de quaisquer outras configurações, então as definições da configuração serão removidas e nenhuma outra alteração será feita no twin. 

Para modificar uma configuração, utilize os seguintes passos: 

1. No [portal Azure,](https://portal.azure.com)vá ao seu hub IoT. 

2. Selecione **a configuração do dispositivo IoT** . 

3. Selecione a configuração que pretende modificar. 

4. Faça atualizações para os seguintes campos: 

   * Condição do alvo 
   * Etiquetas 
   * Prioridade 
   * Métricas

4. Selecione **Guardar** .

5. Siga os passos no [Monitor uma configuração](#monitor-a-configuration) para ver as alterações desenrolar-se. 

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando elimina uma configuração, qualquer dispositivo que os gémeos assumam a sua próxima configuração de maior prioridade. Se os gémeos do dispositivo não cumprirem a condição alvo de qualquer outra configuração, não são aplicadas outras definições. 

1. No [portal Azure,](https://portal.azure.com)vá ao seu hub IoT. 

2. Selecione **a configuração do dispositivo IoT** . 

3. Utilize a caixa de verificação para selecionar a configuração que pretende eliminar. 

4. Selecione **Eliminar** .

5. Um aviso vai pedir-lhe para confirmar.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar e monitorizar dispositivos IoT em escala. Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir as identidades do dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Monitorize o seu hub IoT](monitor-iot-hub.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)

Para explorar utilizando o serviço de provisionamento de dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/index.yml)