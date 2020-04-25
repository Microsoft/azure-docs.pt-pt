---
title: Implantação automática para grupos de dispositivos - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Utilize implementações automáticas em Azure IoT Edge para gerir grupos de dispositivos com base em etiquetas partilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7540c5a82220eef61b8f1cf470697315496cd6bf
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127610"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Compreender as implementações automáticas IoT Edge para dispositivos individuais ou em escala

As implementações automáticas e a implementação em camadas ajudam-no a gerir e configurar módulos em grande número de dispositivos IoT Edge.

O Azure IoT Edge fornece duas formas de configurar os módulos para funcionar em dispositivos IoT Edge. O primeiro método é implementar módulos numa base por dispositivo. Cria-se um manifesto de implantação e aplica-o num determinado dispositivo pelo nome. O segundo método consiste em implantar automaticamente módulos em qualquer dispositivo registado que satisfaça um conjunto de condições definidas. Cria-se um manifesto de implantação e, em seguida, define a que dispositivos se aplica com base em [etiquetas](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) no dispositivo twin.

Este artigo centra-se na configuração e monitorização de frotas de dispositivos, colectivamente referidas como *implantações automáticas IoT Edge*.Os passos básicos de implantação são os seguintes:

1. Um operador define uma implementação que descreve um conjunto de módulos e os dispositivos-alvo.Cada implantação tem um manifesto de implantação que reflete esta informação.
2. O serviço IoT Hub comunica com todos os dispositivos direcionados para configurá-los com os módulos declarados.
3. O serviço IoT Hub recupera o estado dos dispositivos IoT Edge e disponibiliza-os ao operador.Por exemplo, um operador pode ver quando um dispositivo Edge não está configurado com sucesso ou se um módulo falha durante o tempo de funcionação.
4. A qualquer momento, novos dispositivos IoT Edge que satisfaçam as condições de segmentação estão configurados para a implementação.

Este artigo descreve cada componente envolvido na configuração e monitorização de uma implementação. Para uma passagem pela criação e atualização de uma implementação, consulte [os módulos De implantação e monitorização em escala](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Implementação

Uma implementação automática IoT Edge atribui imagens do módulo IoT Edge para ser executada como instâncias num conjunto direcionado de dispositivos IoT Edge. Funciona configurando um manifesto de implantação ioT Edge para incluir uma lista de módulos com os parâmetros de inicialização correspondentes.Uma implementação pode ser atribuída a um único dispositivo (com base no ID do dispositivo) ou a um grupo de dispositivos (com base em etiquetas).Uma vez que um dispositivo IoT Edge recebe um manifesto de implantação, ele descarrega e instala as imagens do recipiente dos respetivos repositórios de contentores, e configura-as em conformidade.Uma vez criada uma implementação, um operador pode monitorizar o estado de implementação para ver se os dispositivos direcionados estão corretamente configurados.

Apenas dispositivos IoT Edge podem ser configurados com uma implementação. Os seguintes pré-requisitos devem estar no dispositivo antes de poder receber a implantação:

* O sistema operativo base
* Um sistema de gestão de contentores, como Moby ou Docker
* Provisionamento do tempo de execução da Borda IoT

### <a name="deployment-manifest"></a>Manifesto de implementação

Um manifesto de implantação é um documento JSON que descreve os módulos a configurar nos dispositivos IoT Edge direcionados. Contém os metadados de configuração para todos os módulos, incluindo os módulos de sistema necessários (especificamente o agente IoT Edge e o hub IoT Edge).  

Os metadados de configuração para cada módulo incluem:

* Versão
* Tipo
* Estado (por exemplo, execução ou paragem)
* Política de reinício
* Registo de imagem e contentores
* Rotas para entrada e saída de dados

Se a imagem do módulo for armazenada num registo de contentores privados, o agente IoT Edge detém as credenciais de registo.

### <a name="target-condition"></a>Condição do alvo

A condição-alvo é continuamente avaliada ao longo da vida útil da implantação. Estão incluídos quaisquer novos dispositivos que satisfaçam os requisitos e que os dispositivos existentes que já não o façam sejam removidos. A implantação é reativada se o serviço detetar qualquer alteração de condição do alvo.

Por exemplo, tem uma implantação com uma etiqueta de condição-alvo.ambiente = 'prod'. Quando inicias a implantação, há 10 dispositivos de produção. Os módulos são instalados com sucesso nestes 10 dispositivos. O estado do agente IoT Edge mostra 10 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 0 respostas pendentes. Agora adicione mais cinco dispositivos com tags.environment = 'prod'. O serviço deteta a alteração e o estado do agente IoT Edge torna-se 15 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 5 respostas pendentes enquanto se implanta nos cinco novos dispositivos.

Utilize qualquer condição Boolean em etiquetas duplas do dispositivo, propriedades reportadas de dispositivos twin ou dispositivoId para selecionar os dispositivos-alvo. Se pretender utilizar a condição com etiquetas, tem de{} adicionar "tags": secção do dispositivo twin sob o mesmo nível que as propriedades. [Saiba mais sobre tags em dispositivo twin](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condições-alvo:

* dispositivoId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' And tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' And tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* propriedades.reported.devicemodel = '4000x'

Considere estes constrangimentos quando construir uma condição-alvo:

* No dispositivo twin, só é possível construir uma condição de destino utilizando tags, propriedades reportadas ou dispositivoId.
* As pascantes duplas não são permitidas em nenhuma parte da condição do alvo. Use citações simples.
* As cotações individuais representam os valores da condição-alvo. Portanto, deve escapar da citação única com outra citação única se fizer parte do nome do dispositivo. Por exemplo, visar um `operator'sDevice`dispositivo `deviceId='operator''sDevice'`chamado , escrever .
* Os números, letras e os seguintes `-:.+%_#*?!(),=@;$`caracteres são permitidos nos valores das condições-alvo: .

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implementação deve ser aplicada a um dispositivo direcionado em relação a outras implementações. Uma prioridade de implantação é um inteiro positivo, com um número maior a denotar prioridade supressão. Se um dispositivo IoT Edge for alvo de mais de uma implementação, aplica-se a implantação com a maior prioridade.Não são aplicadas implantações com prioridades mais baixas, nem se fundem.Se um dispositivo for direcionado com duas ou mais implementações com igual prioridade, aplica-se a implementação mais recentemente criada (determinada pelo carimbo de tempo de criação).

### <a name="labels"></a>Etiquetas

As etiquetas são pares de chaves de corda/valor que pode usar para filtrar e implantações de grupo.Uma implantação pode ter várias etiquetas. As etiquetas são opcionais e não impactam a configuração real dos dispositivos IoT Edge.

### <a name="metrics"></a>Métricas

Por predefinição, todas as implementações reportam quatro métricas:

* **O alvo** mostra os dispositivos IoT Edge que correspondem à condição de alvo de implantação.
* **Aplicado** mostra os dispositivos IoT Edge direcionados que não são alvo de outra implementação de maior prioridade.
* **Reportando o sucesso** mostra os dispositivos IoT Edge que reportaram que os módulos foram implantados com sucesso.
* **Reporte Falha** mostra os dispositivos IoT Edge que reportaram que um ou mais módulos não foram implantados com sucesso. Para investigar mais aprofundadamente o erro, ligue-se remotamente a esses dispositivos e veja os ficheiros de registo.

Além disso, pode definir as suas métricas personalizadas para ajudar a monitorizar e gerir a implementação.

As métricas fornecem contagens sumárias dos vários estados que os dispositivos podem reportar como resultado da aplicação de uma configuração de implementação. As métricas podem consultar [propriedades reportadas pelo módulo EdgeHub,](module-edgeagent-edgehub.md#edgehub-reported-properties)como *o lastDesiredStatus* ou *o último ConnectTime*. Por exemplo:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

A adição das suas próprias métricas é opcional e não afeta a configuração real dos dispositivos IoT Edge.

## <a name="layered-deployment"></a>Implantação em camadas

As implantações em camadas são implantações automáticas que podem ser combinadas para reduzir o número de implementações únicas que precisam de ser criadas. As implementações em camadas são úteis em cenários em que os mesmos módulos são reutilizados em diferentes combinações em muitas implementações automáticas.

As implantações em camadas têm os mesmos componentes básicos que qualquer implantação automática. Visam dispositivos baseados em tags nos gémeos do dispositivo e fornecem a mesma funcionalidade em torno de etiquetas, métricas e relatórios de estado. As implementações em camadas também têm prioridades atribuídas a eles, mas em vez de usar a prioridade para determinar qual a implementação aplicada a um dispositivo, a prioridade determina como várias implementações são classificadas num dispositivo. Por exemplo, se duas implementações em camadas tiverem um módulo ou uma rota com o mesmo nome, a implantação em camadas com a prioridade mais elevada será aplicada enquanto a menor prioridade é substituída.

Os módulos de tempo de funcionamento do sistema, edgeAgent e edgeHub, não estão configurados como parte de uma implementação em camadas. Qualquer dispositivo IoT Edge visado por uma implementação em camadas precisa de uma implementação automática padrão aplicada primeiro. A implantação automática fornece a base sobre a qual podem ser adicionadas implantações em camadas.

Um dispositivo IoT Edge pode aplicar uma e apenas uma implementação automática padrão, mas pode aplicar várias implementações automáticas em camadas. Quaisquer implementações em camadas direcionadas para um dispositivo devem ter uma prioridade maior do que a implementação automática para esse dispositivo.

Por exemplo, considere o seguinte cenário de uma empresa que gere edifícios. Desenvolveram módulos IoT Edge para recolher dados de câmaras de segurança, sensores de movimento e elevadores. No entanto, nem todos os seus edifícios podem usar os três módulos. Com implementações automáticas padrão, a empresa precisa de criar implementações individuais para todas as combinações de módulos de que os seus edifícios precisam.

![As implementações automáticas padrão precisam de acomodar todas as combinações de módulos](./media/module-deployment-monitoring/standard-deployment.png)

No entanto, uma vez que a empresa muda para implementações automáticas em camadas, eles descobrem que podem criar as mesmas combinações de módulos para os seus edifícios com menos implementações para gerir. Cada módulo tem a sua própria implantação em camadas, e as etiquetas do dispositivo identificam quais os módulos que são adicionados a cada edifício.

![As implementações automáticas em camadas simplificam cenários em que os mesmos módulos são combinados de diferentes maneiras](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configuração de módulo gémeo

Quando trabalha com implementações em camadas, pode, intencionalmente ou não, ter duas implementações com o mesmo módulo direcionado para um dispositivo. Nesses casos, pode decidir se a implementação prioritária mais elevada deve substituir o módulo gémeo ou anexar-lhe. Por exemplo, pode ter uma implementação que aplica o mesmo módulo a 100 dispositivos diferentes. No entanto, 10 desses dispositivos estão em instalações seguras e precisam de configuração adicional para comunicar através de servidores proxy. Pode utilizar uma implementação em camadas para adicionar propriedades duplas de módulos que permitem que esses 10 dispositivos se comuniquem de forma segura sem sobrepor as informações gémeas do módulo existente a partir da implantação da base.

Pode anexar as propriedades desejadas pelo módulo twin no manifesto de implantação. Sempre que, numa implementação padrão, adicione propriedades nas **propriedades.secção desejada** do módulo twin, numa implementação em camadas pode declarar um novo subconjunto de propriedades desejadas.

Por exemplo, numa implementação padrão, pode adicionar o módulo de sensor de temperatura simulado com as seguintes propriedades desejadas que lhe dizem para enviar dados em intervalos de 5 segundos:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Numa implementação em camadas que visa alguns ou todos os mesmos dispositivos, pode adicionar uma propriedade que diga ao sensor simulado para enviar 1000 mensagens e depois parar. Você não quer sobrepor as propriedades existentes, então você cria uma `layeredProperties`nova secção dentro das propriedades desejadas chamadas , que contém a nova propriedade:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Um dispositivo que tenha ambas as implementações aplicadas refletirá as seguintes propriedades no módulo twin para o sensor de temperatura simulado:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Se definir o `properties.desired` campo do módulo twin numa implantação em camadas, ele irá substituir as propriedades desejadas para esse módulo em quaisquer implementações prioritárias mais baixas.

## <a name="phased-rollout"></a>Rollout faseado

Um lançamento faseado é um processo global pelo qual um operador implementa alterações para um conjunto alargado de dispositivos IoT Edge. O objetivo é fazer mudanças gradualmente para reduzir o risco de fazer mudanças de rutura em larga escala. As implementações automáticas ajudam a gerir os lançamentos faseados através de uma frota de dispositivos IoT Edge.

Uma implantação faseada é executada nas seguintes fases e etapas:

1. Estabeleça um ambiente de ensaio dos dispositivos IoT Edge, `tag.environment='test'`aprovisionando-os e estabelecendo uma etiqueta dupla de dispositivos como .O ambiente de ensaio deve espelhar o ambiente de produção que a implantação irá eventualmente visar.
2. Crie uma implementação, incluindo os módulos e configurações desejados. A condição de alvo deve visar o ambiente do dispositivo IoT Edge.
3. Valide a configuração do novo módulo no ambiente de teste.
4. Atualize a implementação para incluir um subconjunto de dispositivos IoT Edge de produção adicionando uma nova etiqueta à condição de alvo. Além disso, certifique-se de que a prioridade para a implementação é maior do que outras implementações atualmente direcionadas para esses dispositivos
5. Verifique se a implementação foi bem sucedida nos dispositivos IoT direcionados, visualizando o estado de implementação.
6. Atualize a implementação para direcionar todos os dispositivos restantes de produção IoT Edge.

## <a name="rollback"></a>Recuo

As implementações podem ser relançadas se receber erros ou configurações erradas.Como uma implementação define a configuração absoluta do módulo para um dispositivo IoT Edge, uma implementação adicional também deve ser direcionada para o mesmo dispositivo com uma prioridade mais baixa, mesmo que o objetivo seja remover todos os módulos.  

Eliminar uma implementação não remove os módulos dos dispositivos direcionados. Deve haver outra implementação que defina uma nova configuração para os dispositivos, mesmo que seja uma implementação vazia.

Execute recuos na seguinte sequência:

1. Confirme que uma segunda implementação também é direcionada para o mesmo conjunto de dispositivos. Se o objetivo da reversão for remover todos os módulos, a segunda implementação não deve incluir quaisquer módulos.
2. Modifique ou remova a expressão da condição alvo da implementação que pretende reverter para que os dispositivos deixem de cumprir a condição de alvo.
3. Verifique se a reversão foi bem sucedida visualizando o estado de implantação.
   * A implantação em enrolado não deve mais mostrar o estado dos dispositivos que foram revirados para trás.
   * A segunda implementação deve agora incluir o estado de implantação dos dispositivos que foram revirados para trás.

## <a name="next-steps"></a>Passos seguintes

* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos Deploy e monitor ioT Edge em escala](how-to-deploy-at-scale.md).
* Saiba mais sobre outros conceitos IoT Edge como o tempo de [execução IoT Edge](iot-edge-runtime.md) e os [módulos IoT Edge](iot-edge-modules.md).
