---
title: Saiba como o tempo de execução gerencia dispositivos-Azure IoT Edge | Microsoft Docs
description: Saiba como o tempo de execução do IoT Edge gerencia módulos, segurança, comunicação e relatórios em seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 94e33c855327e70f486746bcd781491823324dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490430"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Entenda o tempo de execução de Azure IoT Edge e sua arquitetura

O tempo de execução do IoT Edge é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para serem executados na borda e comuniquem os resultados. 

O tempo de execução de IoT Edge é responsável pelas seguintes funções em dispositivos IoT Edge:

* Instalar e atualizar cargas de trabalho no dispositivo.
* Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
* Verifique se os [módulos IOT Edge](iot-edge-modules.md) estão sempre em execução.
* Relate a integridade do módulo para a nuvem para monitoramento remoto.
* Gerencie a comunicação entre dispositivos downstream e dispositivos IoT Edge.
* Gerenciar a comunicação entre módulos no dispositivo IoT Edge.
* Gerencie a comunicação entre o dispositivo IoT Edge e a nuvem.

![O tempo de execução comunica as informações e a integridade do módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do tempo de execução de IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. Essas duas funções são executadas por dois componentes que fazem parte do tempo de execução de IoT Edge. O *Hub de IOT Edge* é responsável pela comunicação, enquanto o *agente de IOT Edge* implanta e monitora os módulos. 

O Hub de IoT Edge e o agente de IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. Às vezes, eles são chamados de *módulos de tempo de execução*. 

## <a name="iot-edge-hub"></a>Hub de IoT Edge

O Hub de IoT Edge é um dos dois módulos que compõem o tempo de execução de Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (se dispositivos ou módulos) podem se conectar ao tempo de execução do IoT Edge da mesma forma que fariam com o Hub IoT. 

>[!NOTE]
> IoT Edge Hub dá suporte a clientes que se conectam usando MQTT ou AMQP. Ele não dá suporte a clientes que usam HTTP. 

O Hub de IoT Edge não é uma versão completa do Hub IoT em execução localmente. Há algumas coisas que o Hub de IoT Edge Delega silenciosamente ao Hub IoT. Por exemplo, IoT Edge Hub encaminha solicitações de autenticação para o Hub IoT quando um dispositivo tenta se conectar pela primeira vez. Depois que a primeira conexão é estabelecida, as informações de segurança são armazenadas em cache localmente pelo Hub IoT Edge. As conexões subsequentes desse dispositivo são permitidas sem a necessidade de autenticação na nuvem. 

Para reduzir a largura de banda que sua solução de IoT Edge usa, o Hub de IoT Edge otimiza quantas conexões reais são feitas na nuvem. IoT Edge Hub usa conexões lógicas de clientes como módulos ou dispositivos downstream e os combina para uma única conexão física com a nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes acham que têm sua própria conexão com a nuvem, mesmo que todos estejam sendo enviados pela mesma conexão. 

![O Hub de IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/Gateway.png)

IoT Edge Hub pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, IoT Edge Hub salvará mensagens ou atualizará as atualizações localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. O local usado para esse cache temporário é determinado por uma propriedade do módulo de IoT Edge do Hub. O tamanho do cache não é limitado e aumentará desde que o dispositivo tenha capacidade de armazenamento. Para obter mais informações, consulte [recursos offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação de módulo

IoT Edge Hub facilita a comunicação entre módulos e módulos. O uso do hub de IoT Edge como um agente de mensagem mantém os módulos independentes entre si. Os módulos só precisam especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Um desenvolvedor de soluções pode reunir essas entradas e saídas para que os módulos processem dados na ordem específica para essa solução. 

![IoT Edge Hub facilita a comunicação entre módulos e módulos](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados para o Hub de IoT Edge, um módulo chama o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. O pseudocódigo a seguir envia uma mensagem em **Saída1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. O pseudocódigo a seguir registra a função messageProcessor a ser usada para processar todas as mensagens recebidas em **entrada1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)de SDK preferencial:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)ou [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor da solução é responsável por especificar as regras que determinam como IoT Edge Hub passa mensagens entre módulos. As regras de roteamento são definidas na nuvem e enviadas por push para IoT Edge Hub em seu módulo. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).   

![Rotas entre módulos passam pelo hub de IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente de IoT Edge

O agente de IoT Edge é o outro módulo que compõe o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar os módulos, garantindo que eles continuem a ser executados e relatando o status dos módulos de volta ao Hub IoT. Esses dados de configuração são gravados como uma propriedade do módulo do agente de IoT Edge. 

O [daemon de segurança do IOT Edge](iot-edge-security-manager.md) inicia o agente do IOT Edge na inicialização do dispositivo. O agente recupera seu módulo/d do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados. 

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente de IoT Edge para controlar o ciclo de vida do módulo. Algumas das propriedades mais interessantes são: 

* **Settings. Image** – a imagem de contêiner que o agente de IOT Edge usa para iniciar o módulo. O agente de IoT Edge deve ser configurado com credenciais para o registro de contêiner se a imagem estiver protegida por uma senha. As credenciais para o registro de contêiner podem ser configuradas remotamente usando o manifesto de implantação ou no próprio dispositivo IoT Edge atualizando o arquivo `config.yaml` na pasta do programa IoT Edge.
* **Settings.** – uma cadeia de caracteres que é passada diretamente para o daemon de contêiner Moby ao iniciar o contêiner de um módulo. A adição de opções nessa propriedade permite configurações avançadas como encaminhamento de porta ou montagem de volumes no contêiner de um módulo.  
* **status** – o estado no qual o agente de IOT Edge coloca o módulo. Normalmente, esse valor é definido como *em execução* , pois a maioria das pessoas deseja que o agente de IOT Edge inicie imediatamente todos os módulos no dispositivo. No entanto, você pode especificar o estado inicial de um módulo a ser interrompido e aguardar um tempo futuro para informar ao agente de IoT Edge para iniciar um módulo. O agente de IoT Edge relata o status de cada módulo de volta para a nuvem nas propriedades relatadas. Uma diferença entre a propriedade desejada e a propriedade relatada é um indicador de um dispositivo de comportamento inadequado. Os status com suporte são:
   * Baixar
   * A executar
   * Não íntegro
   * Com Falhas
   * Parada
* **restartPolicy** – como o agente de IOT Edge reinicia um módulo. Os valores possíveis incluem:
   * `never` – o agente de IoT Edge nunca reinicia o módulo.
   * `on-failure`-se o módulo falhar, o agente de IoT Edge o reiniciará. Se o módulo for desligado corretamente, o agente de IoT Edge não o reiniciará.
   * `on-unhealthy`-se o módulo falhar ou for considerado não íntegro, o agente de IoT Edge o reiniciará.
   * `always`-se o módulo falhar, for considerado não íntegro ou for desligado de alguma forma, o agente de IoT Edge o reiniciará. 

O agente de IoT Edge envia resposta de tempo de execução ao Hub IoT. Aqui está uma lista de possíveis respostas:
  * 200-OK
  * 400-a configuração de implantação está malformada ou inválida.
  * 417-o dispositivo não tem um conjunto de configuração de implantação.
  * 412-a versão do esquema na configuração de implantação é inválida.
  * 406-o dispositivo IoT Edge está offline ou não está enviando relatórios de status.
  * 500-ocorreu um erro no tempo de execução de IoT Edge.

Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).   

### <a name="security"></a>Segurança

O agente de IoT Edge desempenha uma função crítica na segurança de um dispositivo de IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo. 

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Passos seguintes

[Entender Azure IoT Edge módulos](iot-edge-modules.md)
