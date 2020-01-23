---
title: Implementação automática de grupos de dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Utilizar implementações automática no Azure IoT Edge para gerir grupos de dispositivos com base em etiquetas partilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406830add1891a058e9b43fccb8435aa4d339ed0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548684"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Compreender as implementações automáticas do IoT Edge para dispositivos individuais ou em escala

As implantações automáticas e a implantação em camadas ajudam você a gerenciar e configurar módulos em um grande número de dispositivos IoT Edge.

O Azure IoT Edge fornece duas maneiras de configurar os módulos para serem executados em dispositivos IoT Edge. O primeiro método é implantar módulos em uma base por dispositivo. Você cria um manifesto de implantação e, em seguida, o aplica a um dispositivo específico por nome. O segundo método é implantar módulos automaticamente em qualquer dispositivo registrado que atenda a um conjunto de condições definidas. Você cria um manifesto de implantação e, em seguida, define a quais dispositivos ele se aplica com base nas [marcas](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) no dispositivo.

Este artigo se concentra na configuração e no monitoramento de frotas de dispositivos, coletivamente chamados de *IOT Edge implantações automáticas*. As etapas básicas de implantação são as seguintes:

1. Um operador define uma implantação que descreve um conjunto de módulos e os dispositivos de destino. Cada implantação tem um manifesto de implantação que reflete essas informações.
2. O serviço do Hub IoT se comunica com todos os dispositivos de destino para configurá-los com os módulos declarados.
3. O serviço IoT Hub obtém o estado dos dispositivos IoT Edge e disponibiliza-os para o operador.  Por exemplo, um operador pode ver quando um dispositivo de borda não está configurado com êxito ou se um módulo falha durante o tempo de execução.
4. Em qualquer altura, os novos dispositivos de IoT Edge que satisfazem as condições de direcionamento estão configurados para a implementação.

Este artigo descreve cada componente envolvido durante a configuração e monitorização de uma implementação. Para obter instruções para criar e atualizar uma implementação, consulte [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Implementação

Uma implementação automática do IoT Edge atribui o IoT Edge imagens de módulo para ser executado como instâncias num conjunto direcionado de dispositivos do IoT Edge. Ele funciona configurando um manifesto de implantação do IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização correspondente. Uma implantação pode ser atribuída a um único dispositivo (com base na ID do dispositivo) ou a um grupo de dispositivos (com base nas marcas). Quando um dispositivo IoT Edge recebe um manifesto de implantação, ele baixa e instala as imagens de contêiner dos repositórios do respectivo contêiner e as configura adequadamente. Depois que uma implantação é criada, um operador pode monitorar o status da implantação para ver se os dispositivos de destino estão configurados corretamente.

Apenas os dispositivos do IoT Edge podem ser configurados com uma implementação. Os seguintes pré-requisitos tem de estar no dispositivo antes de pode receber a implementação:

* O sistema operativo base
* Um sistema de gestão do contentor, como o Moby ou o Docker
* Aprovisionamento do runtime do IoT Edge

### <a name="deployment-manifest"></a>Manifesto de implementação

Um manifesto de implantação é um documento JSON que descreve os módulos a ser configurado nos dispositivos visados do IoT Edge. Contém os metadados de configuração para todos os módulos, incluindo os módulos de sistema necessário (especificamente o agente do IoT Edge e hub do IoT Edge).  

Os metadados de configuração para cada módulo incluem:

* Versão
* Tipo
* Estado (por exemplo, em execução ou parado)
* Política de reinício
* Registo de contentor e de imagem
* Rotas para dados de entrada e saída

Se a imagem do módulo é armazenada no registo de contentor privado, o agente do IoT Edge guarda as credenciais de registo.

### <a name="target-condition"></a>Condição de destino

A condição de destino é avaliada continuamente durante o tempo de vida da implantação. Novos dispositivos que cumprem os requisitos são incluídos, e todos os dispositivos existentes que deixaram de exigi são removidos. A implementação for reativada se o serviço Deteta qualquer alteração de condição de destino.

Por exemplo, você tem uma implantação com uma condição de destino Tags. Environment = ' prod '. Quando iniciar a implementação, há 10 dispositivos de produção. Os módulos são instalados com êxito nesses 10 dispositivos. O status do agente de IoT Edge mostra 10 dispositivos no total, 10 respostas bem-sucedidas, 0 respostas de falha e 0 respostas pendentes. Agora, adicionar cinco dispositivos mais com tags.environment = 'prod'. O serviço detecta a alteração e o status do agente de IoT Edge se torna 15 dispositivos no total, 10 respostas bem-sucedidas, 0 respostas de falha e 5 respostas pendentes enquanto ele é implantado nos cinco novos dispositivos.

Utilize qualquer condição booleana em etiquetas de gémeos de dispositivo ou deviceId para selecionar os dispositivos de destino. Se pretender utilizar a condição com etiquetas, tem de adicionar "etiquetas":{} secção no dispositivo duplo no mesmo nível que propriedades. [Saiba mais sobre as etiquetas no dispositivo duplo](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condições de destino:

* deviceId = 'linuxprod1'
* tags.Environment = 'prod'
* tags.Environment = 'prod' e tags.location = 'westus'
* tags.Environment = 'prod' OR tags.location = 'westus'
* tags.Operator = "João" e tags.environment = 'prod' não deviceId = 'linuxprod1'

Aqui estão alguns restringe ao construir uma condição de destino:

* No dispositivo duplo, pode criar apenas uma condição de destino usando marcas ou deviceId.
* Aspas não são permitidas em qualquer parte a condição de destino. Utilize plicas.
* Aspas representam os valores da condição de destino. Por conseguinte, tem de escapar a aspa de segurança com outro aspa se faz parte do nome do dispositivo. Por exemplo, para um dispositivo de destino chamado `operator'sDevice`, escrever `deviceId='operator''sDevice'`.
* Números, letras e os seguintes carateres são permitidos valores de condição de destino: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implementação deve ser aplicada a um dispositivo de destino em relação ao outras implementações. Uma prioridade de implantação é um número inteiro, com os números maiores que indica a prioridade mais alta. Se um dispositivo IoT Edge é direcionado por mais de uma implementação, aplica-se a implementação com a prioridade mais alta.  Implantações com prioridades inferiores não são aplicadas, nem mescladas.  Se um dispositivo for direcionado a duas ou mais implantações com prioridade igual, a implantação criada mais recentemente (determinada pelo carimbo de data/hora de criação) se aplicará.

### <a name="labels"></a>Etiquetas

Os rótulos são pares de chave/valor de cadeia de caracteres que você pode usar para filtrar e agrupar implantações. Uma implantação pode ter vários rótulos. Os rótulos são opcionais e não afetam a configuração real dos dispositivos IoT Edge.

### <a name="metrics"></a>Métricas

Por padrão, todas as implantações se reportam em quatro métricas:

* **Direcionado** mostra os dispositivos IOT Edge que correspondem à condição de direcionamento de implantação.
* **Aplicado** mostra os dispositivos de IOT Edge de destino que não são direcionados por outra implantação de prioridade mais alta.
* **Relatando êxito** mostra os dispositivos IOT Edge que relataram ao serviço que os módulos foram implantados com êxito.
* **Falha de relatório** mostra os dispositivos IOT Edge que relataram ao serviço que um ou mais módulos não foram implantados com êxito. Para continuar a investigar o erro, ligar remotamente a esses dispositivos e ver os ficheiros de registo.

Além disso, você pode definir suas próprias métricas personalizadas para ajudar a monitorar e gerenciar a implantação.

As métricas fornecem contagens de resumo dos vários Estados que os dispositivos podem relatar de volta como resultado da aplicação de uma configuração de implantação. As métricas podem consultar [Propriedades relatadas do módulo edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), como *lastDesiredStatus* ou *lastConnectTime*. Por exemplo:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Adicionar suas próprias métricas é opcional e não afeta a configuração real dos dispositivos IoT Edge.

## <a name="layered-deployment"></a>Implantação em camadas

Implantações em camadas são implantações automáticas que podem ser combinadas para reduzir o número de implantações exclusivas que precisam ser criadas. Implantações em camadas são úteis em cenários em que os mesmos módulos são reutilizados em combinações diferentes em muitas implantações automáticas.

Implantações em camadas têm os mesmos componentes básicos que qualquer implantação automática. Eles direcionam dispositivos com base em marcas no dispositivo gêmeos e fornecem a mesma funcionalidade em relação a rótulos, métricas e relatórios de status. Implantações em camadas também têm prioridades atribuídas a elas, mas em vez de usar a prioridade para determinar qual implantação é aplicada a um dispositivo, a prioridade determina como várias implantações são classificadas em um dispositivo. Por exemplo, se duas implantações em camadas tiverem um módulo ou uma rota com o mesmo nome, a implantação em camadas com a prioridade mais alta será aplicada enquanto a prioridade mais baixa for substituída.

Os módulos de tempo de execução do sistema, edgeAgent e edgeHub, não são configurados como parte de uma implantação em camadas. Qualquer dispositivo IoT Edge de destino de uma implantação em camadas precisa de uma implantação automática padrão aplicada a ele primeiro para fornecer a base sobre a qual as implantações em camadas podem ser adicionadas.

Um dispositivo IoT Edge pode aplicar apenas uma implantação automática padrão, mas pode aplicar várias implantações automáticas em camadas. Todas as implantações em camadas direcionadas a um dispositivo devem ter uma prioridade mais alta do que a implantação automática para esse dispositivo.

Por exemplo, considere o cenário a seguir de uma empresa que gerencia edifícios. Eles desenvolveram IoT Edge módulos para coletar dados de câmeras de segurança, sensores de movimento e elevadors. No entanto, nem todos os seus edifícios podem usar todos os três módulos. Com implantações automáticas padrão, a empresa precisa criar implantações individuais para todas as combinações de módulo de que seus edifícios precisam.

![As implantações automáticas padrão precisam acomodar cada combinação de módulo](./media/module-deployment-monitoring/standard-deployment.png)

No entanto, uma vez que a empresa alterna para implantações automáticas em camadas, eles acham que podem criar as mesmas combinações de módulo para seus prédios com menos implantações para gerenciar. Cada módulo tem sua própria implantação em camadas e as marcas de dispositivo identificam quais módulos são adicionados a cada compilação.

![Implantações automáticas em camadas simplificam cenários em que os mesmos módulos são combinados de maneiras diferentes](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configuração do módulo/.

Ao trabalhar com implantações em camadas, você pode, intencionalmente ou não, ter duas implantações com o mesmo módulo destinado a um dispositivo. Nesses casos, você pode decidir se a implantação de prioridade mais alta deve substituir o módulo ou acrescentar a ele. Por exemplo, você pode ter uma implantação que aplica o mesmo módulo a 100 dispositivos diferentes. No entanto, 10 desses dispositivos estão em instalações seguras e precisam de configuração adicional para se comunicar através de servidores proxy. Você pode usar uma implantação em camadas para adicionar propriedades de módulo 10 que permitem que esses dez dispositivos se comuniquem de forma segura sem substituir as informações do módulo atual de propriedades da implantação de base.

Você pode acrescentar as propriedades desejadas do módulo d no manifesto de implantação. Em uma implantação padrão, você adicionaria Propriedades na seção **Properties. Desired** do módulo... em uma implantação em camadas, você pode declarar um novo subconjunto das propriedades desejadas.

Por exemplo, em uma implantação padrão, você pode adicionar o módulo sensor de temperatura simulado com as seguintes propriedades desejadas que o dizem para enviar dados em intervalos de 5 segundos:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Em uma implantação em camadas direcionando os mesmos dispositivos ou um subconjunto dos mesmos dispositivos, talvez você queira adicionar uma propriedade adicional que informe ao sensor simulado para enviar mensagens 1000 e, em seguida, parar. Você não quer substituir as propriedades existentes, portanto, você cria uma nova seção dentro das propriedades desejadas chamada `layeredProperties`, que contém a nova propriedade:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Um dispositivo que tenha ambas as implantações aplicadas refletirá as seguintes propriedades no módulo "..." para o sensor de temperatura simulado:

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

Se você definir o campo `properties.desired` do módulo em uma implantação em camadas, ele substituirá as propriedades desejadas para esse módulo em todas as implantações de menor prioridade.

## <a name="phased-rollout"></a>Implementação faseada

Uma implementação faseada é um processo geral no qual um operador implementa as alterações a um conjunto essa de dispositivos do IoT Edge. O objetivo é fazer alterações gradualmente para reduzir o risco de efetuar alterações significativas de grande escala. As implantações automáticas ajudam a gerenciar distribuições em fases em uma frota de dispositivos IoT Edge.

Uma implementação faseada é executada nas seguintes fases e passos:

1. Estabelecer um ambiente de teste de dispositivos do IoT Edge aprovisionamento-los e definindo uma marca de twin do dispositivo, como `tag.environment='test'`. O ambiente de teste deve espelhar o ambiente de produção que a implantação eventualmente terá como destino.
2. Crie uma implementação, incluindo o número de módulos desejados e configurações. A condição de destino deve visar o teste de ambiente de dispositivos do IoT Edge.
3. Valide a nova configuração de módulo no ambiente de teste.
4. Atualize a implementação para incluir um subconjunto de dispositivos do IoT Edge de produção, adicionando uma nova etiqueta para a condição de destino. Além disso, certifique-se de que a prioridade para a implementação é maior do que outras implementações atualmente visadas para esses dispositivos
5. Certifique-se de que a implementação foi concluída com êxito nos dispositivos visados IoT ao visualizar o estado de implementação.
6. Atualize a implementação para todos os restantes dispositivos de IoT Edge de produção de destino.

## <a name="rollback"></a>Reverter

Implementações podem ser revertidas caso receberá erros ou configurações incorretas. Como uma implantação define a configuração de módulo absoluta para um dispositivo IoT Edge, uma implantação adicional também deve ser direcionada para o mesmo dispositivo em uma prioridade mais baixa, mesmo se a meta for remover todos os módulos.  

A exclusão de uma implantação não remove os módulos dos dispositivos de destino. Deve haver outra implantação que defina uma nova configuração para os dispositivos, mesmo se for uma implantação vazia.

Execute reversões a seguinte sequência:

1. Certifique-se que uma segunda implementação também é direcionada para o mesmo conjunto de dispositivos. Se o objetivo a reversão é remover todos os módulos, a segunda implementação não deve incluir quaisquer módulos.
2. Modificar ou remover a expressão de condição de destino da implementação que pretende implementar, para que os dispositivos já não cumprem a condição de destino.
3. Certifique-se de que a reversão foi concluída com êxito ao visualizar o estado de implementação.
   * A implementação de revertidas deve deixar de Mostrar estado para os dispositivos que foram revertidas.
   * A segunda implementação agora deve incluir o estado de implementação para os dispositivos que foram revertidas.

## <a name="next-steps"></a>Passos seguintes

* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).
* Saiba mais sobre outros conceitos de IoT Edge, como o [runtime do IoT Edge](iot-edge-runtime.md) e [módulos do IoT Edge](iot-edge-modules.md).
