---
title: Implementação automática de grupos de dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Utilizar implementações automática no Azure IoT Edge para gerir grupos de dispositivos com base em etiquetas partilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 376ee74732daf526b31129fa8c93cbaa32350eae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107819"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Compreender as implementações automáticas do IoT Edge para dispositivos individuais ou em escala

Dispositivos do IoT Edge do Azure siga um [ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md) que é semelhante a outros tipos de dispositivos IoT:

1. Aprovisionar novos dispositivos de IoT Edge, um dispositivo com um sistema operacional de geração de imagens e instalar o [runtime do IoT Edge](iot-edge-runtime.md).
2. Configurar os dispositivos a executar [módulos do IoT Edge](iot-edge-modules.md)e, em seguida, monitorizar a integridade dos mesmos. 
3. Por fim, extinga dispositivos quando eles são substituídos ou tornar-se obsoletas.  

O Azure IoT Edge fornece duas formas de configurar os módulos para executar em dispositivos IoT Edge: um para desenvolvimento e de iterações rápidas num único dispositivo (usaram esse método no Azure IoT Edge [tutoriais](tutorial-deploy-function.md)), e outro para gerenciar grandes fleets de Dispositivos IoT Edge. Ambas as abordagens estão disponíveis no portal do Azure e por meio de programação. Para filtrar grupos ou um grande número de dispositivos, pode especificar quais os dispositivos que deseja implantar seus módulos ao uso [etiquetas](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) no dispositivo duplo. Os seguintes passos falam sobre uma implementação de um grupo de dispositivos de estado de Washington identificados por meio da propriedade de etiquetas. 

Este artigo se concentra na configuração e monitorização fases para frotas de dispositivos, coletivamente designado como implementações automáticas do IoT Edge. Os passos de implementação geral são os seguintes: 

1. Um operador define uma implementação que descreve um conjunto de módulos, bem como os dispositivos de destino. Cada implementação tem um manifesto de implantação que reflita essas informações. 
2. Comunica o serviço IoT Hub com todos os dispositivos direcionados para configurá-las com o número de módulos desejados. 
3. O serviço IoT Hub obtém o estado dos dispositivos IoT Edge e disponibiliza-os para o operador.  Por exemplo, um operador pode ver quando um dispositivo do Edge não está configurado com êxito ou se um módulo falha durante o tempo de execução. 
4. Em qualquer altura, os novos dispositivos de IoT Edge que satisfazem as condições de direcionamento estão configurados para a implementação. Por exemplo, uma implementação direcionada para todos os dispositivos do IoT Edge no Estado norte-americano de Washington automaticamente configura um novo dispositivo IoT Edge, uma vez que ele é aprovisionado e adicionado ao grupo de dispositivos de estado de Washington. 
 
Este artigo descreve cada componente envolvido durante a configuração e monitorização de uma implementação. Para obter instruções para criar e atualizar uma implementação, consulte [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Implementação

Uma implementação automática do IoT Edge atribui o IoT Edge imagens de módulo para ser executado como instâncias num conjunto direcionado de dispositivos do IoT Edge. Ele funciona configurando um manifesto de implantação do IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização correspondente. Uma implementação pode ser atribuída a um único dispositivo (com base no ID de dispositivo) ou a um grupo de dispositivos (com base em etiquetas). Depois de um dispositivo IoT Edge recebe um manifesto de implantação, transfere e instala as imagens de contentor a partir de repositórios do respetivo contentor e configura-as em conformidade. Depois de criar uma implementação, um operador pode monitorizar o estado de implementação para ver se os dispositivos de destino estão corretamente configurados.

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

A condição de destino é avaliada continuamente ao longo da duração da implementação. Novos dispositivos que cumprem os requisitos são incluídos, e todos os dispositivos existentes que deixaram de exigi são removidos. A implementação for reativada se o serviço Deteta qualquer alteração de condição de destino. 

Por exemplo, tem uma implementação de um com um tags.environment de condição de destino = 'prod'. Quando iniciar a implementação, há 10 dispositivos de produção. Os módulos são instalados com êxito nesses 10 dispositivos. O estado do agente do IoT Edge é mostrado como 10 dispositivos total, 10 respostas com êxito, 0 respostas de falhas e 0 respostas pendentes. Agora, adicionar cinco dispositivos mais com tags.environment = 'prod'. O serviço Deteta a alteração e o estado do agente do IoT Edge torna-se 15 dispositivos total, 10 respostas com êxito, respostas de falhas de 0 e 5 respostas pendentes ao tentar implementar a cinco novos dispositivos.

Utilize qualquer condição booleana em etiquetas de gémeos de dispositivo ou deviceId para selecionar os dispositivos de destino. Se pretender utilizar a condição com etiquetas, tem de adicionar "etiquetas":{} secção no dispositivo duplo no mesmo nível que propriedades. [Saiba mais sobre as etiquetas no dispositivo duplo](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condição de destino:

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

Uma prioridade define se uma implementação deve ser aplicada a um dispositivo de destino em relação ao outras implementações. Uma prioridade de implantação é um número inteiro, com os números maiores que indica a prioridade mais alta. Se um dispositivo IoT Edge é direcionado por mais de uma implementação, aplica-se a implementação com a prioridade mais alta.  Implementações com prioridades mais baixas não são aplicadas, nem eles são mesclados.  Se um dispositivo é direcionado com duas ou mais implementações com prioridade igual, aplica-se a implementação mais recentemente criada (determinada pelo carimbo de hora de criação).

### <a name="labels"></a>Etiquetas 

As etiquetas são pares de chave/valor de cadeia de caracteres que pode utilizar para filtrar e grupo de implementações. Uma implementação pode ter várias etiquetas. As etiquetas são opcionais e fazer sem afetar a verdadeira configuração de dispositivos do IoT Edge. 

### <a name="deployment-status"></a>Estado da implementação

Uma implementação pode ser monitorizada para determinar se aplicadas com êxito para qualquer dispositivo IoT Edge direcionado.  Um dispositivo de destino Edge irá aparecer em uma ou mais das seguintes categorias de estado: 

* **Destino** mostra o IoT Edge dispositivos que correspondam a condição de filtragem de implementação.
* **Real** mostra o IoT Edge destino dispositivos que não são visados por outra implementação de prioridade mais alta.
* **Bom estado de funcionamento** mostra o IoT Edge dispositivos que tenham relatado ao serviço que os módulos foram implementados com êxito. 
* **Mau estado de funcionamento** mostra o IoT Edge dispositivos tem relatado ao serviço do que uma ou módulos que não foram implementados com êxito. Para continuar a investigar o erro, ligar remotamente a esses dispositivos e ver os ficheiros de registo.
* **Desconhecido** mostra os dispositivos que não comunicaram qualquer Estado relativas esta implementação de IoT Edge. Para continuar a investigar, ver os ficheiros de registo e informações de serviço.

## <a name="phased-rollout"></a>Implementação faseada 

Uma implementação faseada é um processo geral no qual um operador implementa as alterações a um conjunto essa de dispositivos do IoT Edge. O objetivo é fazer alterações gradualmente para reduzir o risco de efetuar alterações significativas de grande escala.  

Uma implementação faseada é executada nas seguintes fases e passos: 

1. Estabelecer um ambiente de teste de dispositivos do IoT Edge aprovisionamento-los e definindo uma marca de twin do dispositivo, como `tag.environment='test'`. O ambiente de teste deve espelhar o ambiente de produção, eventualmente, serão direcionados para a implementação. 
2. Crie uma implementação, incluindo o número de módulos desejados e configurações. A condição de destino deve visar o teste de ambiente de dispositivos do IoT Edge.   
3. Valide a nova configuração de módulo no ambiente de teste.
4. Atualize a implementação para incluir um subconjunto de dispositivos do IoT Edge de produção, adicionando uma nova etiqueta para a condição de destino. Além disso, certifique-se de que a prioridade para a implementação é maior do que outras implementações atualmente visadas para esses dispositivos 
5. Certifique-se de que a implementação foi concluída com êxito nos dispositivos visados IoT ao visualizar o estado de implementação.
6. Atualize a implementação para todos os restantes dispositivos de IoT Edge de produção de destino.

## <a name="rollback"></a>Reversão

Implementações podem ser revertidas caso receberá erros ou configurações incorretas.  Como uma implementação define a configuração do módulo absoluto para um dispositivo IoT Edge, uma implementação adicional tem também de ser destinada ao mesmo dispositivo com uma prioridade mais baixa, mesmo que o objetivo é remover todos os módulos.  

Execute reversões a seguinte sequência: 

1. Certifique-se que uma segunda implementação também é direcionada para o mesmo conjunto de dispositivos. Se o objetivo a reversão é remover todos os módulos, a segunda implementação não deve incluir quaisquer módulos. 
2. Modificar ou remover a expressão de condição de destino da implementação que pretende implementar, para que os dispositivos já não cumprem a condição de destino.
3. Certifique-se de que a reversão foi concluída com êxito ao visualizar o estado de implementação.
   * A implementação de revertidas deve deixar de Mostrar estado para os dispositivos que foram revertidas.
   * A segunda implementação agora deve incluir o estado de implementação para os dispositivos que foram revertidas.


## <a name="next-steps"></a>Passos Seguintes

* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).
* Saiba mais sobre outros conceitos de IoT Edge, como o [runtime do IoT Edge](iot-edge-runtime.md) e [módulos do IoT Edge](iot-edge-modules.md).

