---
title: Implantação automática para grupos de dispositivos - Azure IoT Edge | Microsoft Docs
description: Utilize implementações automáticas em Azure IoT Edge para gerir grupos de dispositivos com base em tags partilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3989ec4ca2b5c9d7385841604678791b20c1d102
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489987"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Compreender as implementações automáticas IoT Edge para dispositivos individuais ou à escala

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

As implementações automáticas e a implementação em camadas ajudam-no a gerir e a configurar módulos em um grande número de dispositivos IoT Edge.

O Azure IoT Edge fornece duas formas de configurar os módulos para funcionar em dispositivos IoT Edge. O primeiro método é implantar módulos por dispositivo. Cria-se um manifesto de implantação e aplica-se-o a um determinado dispositivo pelo nome. O segundo método é implantar automaticamente os módulos em qualquer dispositivo registado que satisfaça um conjunto de condições definidas. Cria-se um manifesto de implantação e, em seguida, define quais os dispositivos a que se aplica com base em [etiquetas](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) no dispositivo twin.

Este artigo centra-se na configuração e monitorização de frotas de dispositivos, colectivamente referidas como *implantações automáticas IoT Edge*. As etapas básicas de implantação são as seguintes:

1. Um operador define uma implementação que descreve um conjunto de módulos e dispositivos-alvo. Cada implantação tem um manifesto de implantação que reflete esta informação.
2. O serviço IoT Hub comunica com todos os dispositivos direcionados para os configurar com os módulos declarados.
3. O serviço IoT Hub recupera o estado dos dispositivos IoT Edge e coloca-os à disposição do operador.  Por exemplo, um operador pode ver quando um dispositivo Edge não está configurado com sucesso ou se um módulo falha durante o tempo de funcionaamento.
4. A qualquer momento, novos dispositivos IoT Edge que satisfaçam as condições de destino são configurados para a implementação.

Este artigo descreve cada componente envolvido na configuração e monitorização de uma implantação. Para uma passagem pela criação e atualização de uma implementação, consulte [os módulos IoT Edge em escala](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Implementação

Uma implementação automática IoT Edge atribui imagens do módulo IoT Edge para funcionar como instâncias num conjunto direcionado de dispositivos IoT Edge. Funciona configurando um manifesto de implantação IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização correspondentes. Uma implementação pode ser atribuída a um único dispositivo (com base no ID do dispositivo) ou a um grupo de dispositivos (com base em etiquetas). Uma vez que um dispositivo IoT Edge recebe um manifesto de implantação, ele descarrega e instala as imagens do contentor a partir dos respetivos repositórios de contentores, e configura-as em conformidade. Uma vez criada uma implantação, um operador pode monitorizar o estado de implantação para ver se os dispositivos direcionados estão corretamente configurados.

Apenas os dispositivos IoT Edge podem ser configurados com uma implantação. Os seguintes pré-requisitos devem estar no dispositivo antes de poder receber a implantação:

* O sistema operativo base
* Um sistema de gestão de contentores, como Moby ou Docker
* Provisionamento do tempo de execução IoT Edge

### <a name="deployment-manifest"></a>Manifesto de implementação

Um manifesto de implantação é um documento JSON que descreve os módulos a configurar nos dispositivos IoT Edge direcionados. Contém os metadados de configuração para todos os módulos, incluindo os módulos de sistema necessários (especificamente o agente IoT Edge e o hub IoT Edge).  

Os metadados de configuração de cada módulo incluem:

* Versão
* Tipo
* Estado (por exemplo, correr ou parar)
* Política de reinício
* Registo de imagem e contentores
* Rotas para a entrada e saída de dados

Se a imagem do módulo for armazenada num registo de contentores privados, o agente IoT Edge detém as credenciais de registo.

### <a name="target-condition"></a>Condição do alvo

A condição alvo é continuamente avaliada ao longo da vida útil da implantação. Quaisquer novos dispositivos que satisfaçam os requisitos estão incluídos, e quaisquer dispositivos existentes que já não o façam são removidos. A implantação é reativada se o serviço detetar qualquer alteração da condição do alvo.

Por exemplo, tem uma implantação com uma condição de alvo tags.ambiente = 'prod'. Quando se inicia a implantação, há 10 dispositivos de produção. Os módulos são instalados com sucesso nestes 10 dispositivos. O estado do agente IoT Edge mostra 10 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 0 respostas pendentes. Agora adiciona mais cinco dispositivos com tags.ambiente = 'prod'. O serviço deteta a alteração e o estado do agente IoT Edge passa a ser de 15 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 5 respostas pendentes enquanto se implanta nos cinco novos dispositivos.

Utilize qualquer condição booleana em etiquetas gémeas do dispositivo, propriedades reportadas por twin do dispositivo ou dispositivoId para selecionar os dispositivos-alvo. Se quiser utilizar o estado com etiquetas, tem de adicionar "tags": {} secção no dispositivo twin abaixo do mesmo nível que as propriedades. [Saiba mais sobre tags no dispositivo twin](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condições-alvo:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' E tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'
* propriedades.reported.devicemodel = '4000x'

Considere estes constrangimentos quando constrói uma condição alvo:

* No dispositivo twin, só é possível construir uma condição de alvo utilizando tags, propriedades relatadas ou deviceId.
* Cotações duplas não são permitidas em nenhuma parte da condição alvo. Use aspas individuais.
* As cotações individuais representam os valores da condição alvo. Portanto, deve escapar à citação única com outra única citação se for parte do nome do dispositivo. Por exemplo, para direcionar um dispositivo chamado `operator'sDevice` , escreva `deviceId='operator''sDevice'` .
* Números, letras e caracteres a seguir são permitidos em valores de condição alvo: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implantação deve ser aplicada a um dispositivo direcionado em relação a outras implementações. Uma prioridade de implantação é um número inteiro positivo, com números maiores denotando maior prioridade. Se um dispositivo IoT Edge for alvo de mais de uma implantação, a implementação com a maior prioridade aplica-se.  As implantações com prioridades mais baixas não são aplicadas, nem são fundidas.  Se um dispositivo for direcionado com duas ou mais implementações com igual prioridade, aplica-se a implantação mais recente (determinada pela estamp de tempo de criação).

### <a name="labels"></a>Etiquetas

As etiquetas são os pares de chaves/valor de corda que pode utilizar para filtrar e implantações de grupo. Uma implantação pode ter várias etiquetas. As etiquetas são opcionais e não afetam a configuração real dos dispositivos IoT Edge.

### <a name="metrics"></a>Métricas

Por predefinição, todas as implementações reportam quatro métricas:

* **O alvo** mostra os dispositivos IoT Edge que correspondem à condição de alvo de implementação.
* **Aplicado** mostra os dispositivos IoT Edge direcionados que não são alvo de outra implementação de maior prioridade.
* **Reportagem De Sucesso** mostra os dispositivos IoT Edge que relataram que os módulos foram implementados com sucesso.
* **A Falha de Reporte** mostra os dispositivos IoT Edge que relataram que um ou mais módulos não foram implementados com sucesso. Para investigar mais aprofundadamente o erro, ligue-se remotamente a esses dispositivos e veja os ficheiros de registo.

Além disso, pode definir as suas próprias métricas personalizadas para ajudar a monitorizar e gerir a implementação.

As métricas fornecem contagens sumárias dos vários estados que os dispositivos podem reportar como resultado da aplicação de uma configuração de implementação. As métricas podem consultar [propriedades reportadas pelo módulo edgeHub,](module-edgeagent-edgehub.md#edgehub-reported-properties)como *o lastDesiredStatus* ou *o lastConnectTime*. Por exemplo:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Adicionar as suas próprias métricas é opcional, e não afeta a configuração real dos dispositivos IoT Edge.

## <a name="layered-deployment"></a>Implantação em camadas

As implementações em camadas são implementações automáticas que podem ser combinadas para reduzir o número de implementações únicas que precisam de ser criadas. As implementações em camadas são úteis em cenários onde os mesmos módulos são reutilizados em diferentes combinações em muitas implementações automáticas.

As implementações em camadas têm os mesmos componentes básicos que qualquer implantação automática. Visam dispositivos baseados em etiquetas nos gémeos do dispositivo e fornecem a mesma funcionalidade em torno de etiquetas, métricas e relatórios de estado. As implementações em camadas também têm prioridades atribuídas a eles, mas em vez de usar a prioridade para determinar que implementação é aplicada a um dispositivo, a prioridade determina como várias implementações são classificadas num dispositivo. Por exemplo, se duas implementações em camadas tiverem um módulo ou uma rota com o mesmo nome, a implementação em camadas com a maior prioridade será aplicada enquanto a prioridade inferior é substituída.

Os módulos de tempo de execução do sistema, edgeAgent e edgeHub, não estão configurados como parte de uma implementação em camadas. Qualquer dispositivo IoT Edge alvo de uma implementação em camadas precisa de uma implementação automática padrão aplicada primeiro. A implantação automática fornece a base sobre a qual podem ser adicionadas implementações em camadas.

Um dispositivo IoT Edge pode aplicar uma e apenas uma implementação automática padrão, mas pode aplicar várias implementações automáticas em camadas. Quaisquer implementações em camadas dirigidas a um dispositivo devem ter uma prioridade maior do que a colocação automática para esse dispositivo.

Por exemplo, considere o seguinte cenário de uma empresa que gere edifícios. Desenvolveram módulos IoT Edge para recolher dados de câmaras de segurança, sensores de movimento e elevadores. No entanto, nem todos os seus edifícios podem usar os três módulos. Com as implementações automáticas padrão, a empresa precisa de criar implementações individuais para todas as combinações de módulos de que os seus edifícios precisam.

![As implementações automáticas padrão precisam acomodar todas as combinações de módulos](./media/module-deployment-monitoring/standard-deployment.png)

No entanto, uma vez que a empresa muda para implementações automáticas em camadas, descobrem que podem criar as mesmas combinações de módulos para os seus edifícios com menos implementações para gerir. Cada módulo tem a sua própria implementação em camadas, e as etiquetas do dispositivo identificam quais os módulos adicionados a cada edifício.

![Implementações automáticas em camadas simplificam cenários onde os mesmos módulos são combinados de diferentes maneiras](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configuração de twin módulo

Quando trabalha com implementações em camadas, pode, intencionalmente ou não, ter duas implementações com o mesmo módulo direcionado para um dispositivo. Nesses casos, pode decidir se a maior prioridade deve substituir o módulo gémeo ou apender-lhe. Por exemplo, pode ter uma implementação que aplica o mesmo módulo a 100 dispositivos diferentes. No entanto, 10 destes dispositivos encontram-se em instalações seguras e necessitam de configuração adicional para se comunicarem através de servidores proxy. Pode utilizar uma implementação em camadas para adicionar propriedades gémeas do módulo que permitem que esses 10 dispositivos se comuniquem de forma segura sem sobrepor as informações gémeas do módulo existente a partir da implantação da base.

Pode anexar as propriedades desejadas pelo módulo twin no manifesto de implantação. Quando numa implementação padrão adicionaria propriedades nas **propriedades.secção desejada** do módulo twin, numa implementação em camadas pode declarar um novo subconjunto de propriedades desejadas.

Por exemplo, numa implementação padrão pode adicionar o módulo de sensor de temperatura simulado com as seguintes propriedades desejadas que lhe dizem para enviar dados em intervalos de 5 segundos:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Numa implementação em camadas que visa alguns ou todos os mesmos dispositivos, pode adicionar uma propriedade que diz ao sensor simulado para enviar 1000 mensagens e depois parar. Você não quer substituir as propriedades existentes, então você cria uma nova secção dentro das propriedades desejadas chamadas `layeredProperties` , que contém a nova propriedade:

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

Se definir o `properties.desired` campo do módulo twin numa implementação em camadas, substituirá as propriedades desejadas para esse módulo em quaisquer implementações de menor prioridade.

## <a name="phased-rollout"></a>Rollout faseado

Um lançamento faseado é um processo global pelo qual um operador implementa alterações para um conjunto alargado de dispositivos IoT Edge. O objetivo é fazer mudanças gradualmente para reduzir o risco de fazer mudanças de quebra em larga escala. As implementações automáticas ajudam a gerir os lançamentos faseados através de uma frota de dispositivos IoT Edge.

Um lançamento faseado é executado nas seguintes fases e etapas:

1. Estabeleça um ambiente de teste dos dispositivos IoT Edge, provisionando-os e definindo uma etiqueta gémea do dispositivo como `tag.environment='test'` . O ambiente de teste deve refletir o ambiente de produção que a implantação irá eventualmente visar.
2. Crie uma implementação que inclua os módulos e configurações pretendidos. A condição de destino deve direcionar o ambiente do dispositivo IoT Edge.
3. Validar a nova configuração do módulo no ambiente de teste.
4. Atualize a implementação para incluir um subconjunto de dispositivos IoT Edge de produção adicionando uma nova etiqueta à condição de destino. Além disso, certifique-se de que a prioridade para a implantação é maior do que outras implementações atualmente direcionadas para esses dispositivos
5. Verifique se a implementação foi bem sucedida nos dispositivos IoT direcionados visualizando o estado de implantação.
6. Atualize a implementação para direcionar todos os dispositivos IoT Edge de produção restantes.

## <a name="rollback"></a>Reversão

As implementações podem ser reviradas se receber erros ou configurações erradas. Uma vez que uma implementação define a configuração absoluta do módulo para um dispositivo IoT Edge, uma implementação adicional também deve ser direcionada para o mesmo dispositivo com uma prioridade inferior, mesmo que o objetivo seja remover todos os módulos.  

A eliminação de uma implantação não remove os módulos de dispositivos direcionados. Deve haver outra implementação que defina uma nova configuração para os dispositivos, mesmo que seja uma implementação vazia.

Executar reversãos na seguinte sequência:

1. Confirme que uma segunda implantação também é direcionada para o mesmo conjunto de dispositivos. Se o objetivo do revés for remover todos os módulos, a segunda implementação não deve incluir quaisquer módulos.
2. Modifique ou remova a expressão da condição de destino da implementação que pretende reverter para que os dispositivos deixem de cumprir a condição de destino.
3. Verifique se o revés foi bem sucedido ao visualizar o estado de implantação.
   * A colocação de reversão de enrolar não deve continuar a apresentar o estado dos dispositivos que foram revirados.
   * A segunda implementação deve agora incluir o estado de implantação dos dispositivos que foram revirados.

## <a name="next-steps"></a>Passos seguintes

* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos de Implementação e Monitorização IoT Edge à escala](how-to-deploy-at-scale.md).
* Saiba mais sobre outros conceitos IoT Edge como o [tempo de execução IoT Edge](iot-edge-runtime.md) e os [módulos IoT Edge](iot-edge-modules.md).
