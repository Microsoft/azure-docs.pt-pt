---
title: Saiba como o tempo de execução gere dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Saiba como o tempo de funcionação do IoT Edge gere módulos, segurança, comunicação e reportagens nos seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358667"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura

O tempo de execução do IoT Edge é uma coleção de programas que transformam um dispositivo num dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para executar na borda e comunicar os resultados.

O tempo de funcionamento do IoT Edge é responsável pelas seguintes funções nos dispositivos IoT Edge:

* Instale e atualize as cargas de trabalho no dispositivo.
* Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
* Certifique-se de que os [módulos IoT Edge](iot-edge-modules.md) estão sempre em funcionamento.
* Informe a saúde do módulo na nuvem para monitorização remota.
* Gerencie a comunicação entre dispositivos a jusante e dispositivos IoT Edge.
* Gerencie a comunicação entre módulos no dispositivo IoT Edge.
* Gerencie a comunicação entre o dispositivo IoT Edge e a nuvem.

![Tempo de execução comunica informações e o estado de funcionamento do módulo para o IoT Hub](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do runtime do IoT Edge enquadram-se em duas categorias: gestão de comunicação e o módulo. Estas duas funções são executadas por dois componentes que fazem parte do tempo de execução do IoT Edge. O *hub IoT Edge* é responsável pela comunicação, enquanto o agente *IoT Edge* implanta e monitoriza os módulos.

Tanto o hub IoT Edge como o agente IoT Edge são módulos, tal como qualquer outro módulo em execução num dispositivo IoT Edge. Às vezes são referidos como os módulos de tempo de *execução.*

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub IoT Edge é um dos dois módulos que compõem o tempo de funcionação do Azure IoT Edge. Ele atua como um proxy local para o IoT Hub ao expor os pontos de extremidade de protocolo mesmo como o IoT Hub. Esta consistência significa que os clientes (se dispositivos ou módulos) pode ligar-se para o runtime do IoT Edge como se fossem ao IoT Hub.

>[!NOTE]
> O hub IoT Edge suporta clientes que se conectam usando MQTT ou AMQP. Não suporta clientes que utilizam HTTP.

O hub IoT Edge não é uma versão completa do IoT Hub a funcionar localmente. O hub ioT Edge delega silenciosamente algumas tarefas no IoT Hub. Por exemplo, o hub IoT Edge reencaminha pedidos de autenticação para o IoT Hub quando um dispositivo tenta ligar-se pela primeira vez. Após a primeira ligação ser estabelecida, as informações de segurança são guardadas localmente pelo centro IoT Edge. Futuras ligações a partir desse dispositivo são permitidas sem ter que autenticar novamente a nuvem.

Para reduzir a largura de banda que a sua solução IoT Edge utiliza, o hub IoT Edge otimiza quantas ligações reais são feitas à nuvem. O hub IoT Edge pega em ligações lógicas a partir de módulos ou dispositivos a jusante e combina-as para uma única ligação física à nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes acha que eles têm sua própria ligação para a cloud, apesar de tudo o que está a ser enviados pela mesma conexão.

![O hub IoT Edge é uma porta de entrada entre dispositivos físicos e IoT Hub](./media/iot-edge-runtime/Gateway.png)

O centro de IoT Edge pode determinar se está ligado ao IoT Hub. Se a ligação for perdida, o hub IoT Edge guarda mensagens ou atualizações gémeas localmente. Depois de uma conexão for restabelecida, ele sincroniza todos os dados. A localização utilizada para esta cache temporária é determinada por uma propriedade do módulo twin do hub IoT Edge. O tamanho da cache não está limitado e irá aumentar, desde que o dispositivo tem capacidade de armazenamento. Para mais informações, consulte [as capacidades offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação de módulo

O hub IoT Edge facilita a comunicação do módulo. A utilização do hub IoT Edge como corretor de mensagens mantém os módulos independentes uns dos outros. Módulos só precisam de especificar as entradas em que aceite mensagens e as saídas para que eles escrevem mensagens. Um desenvolvedor de soluções pode coser estas inputs e saídas em conjunto para que os módulos processem dados na ordem específica para essa solução.

![IoT Edge Hub facilita comunicação módulo-a-módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados para o hub IoT Edge, um módulo chama o método SendEventAsync. O primeiro argumento especifica em qual saída deve enviar a mensagem. O pseudocódigo seguinte envia uma mensagem no **output1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Para receber uma mensagem, registe um retorno de chamada que processa as mensagens recebidas numa entrada específica. O pseudocódigo seguinte regista o processador de mensagens de função a utilizar para o processamento de todas as mensagens recebidas na **entrada1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e os seus métodos [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)de comunicação, consulte a referência API para a sua língua SDK preferida: [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)ou [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub IoT Edge transmite mensagens entre módulos. As regras de encaminhamento são definidas na nuvem e empurradas para baixo para o hub IoT Edge no seu módulo twin. A mesma sintaxe para as rotas do IoT Hub é usada para definir rotas entre módulos no Azure IoT Edge. Para mais informações, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

![As rotas entre módulos passam pelo hub IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar módulos, garantindo que continuam em execução e comunicar o estado dos módulos de volta para o IoT Hub. Estes dados de configuração são escritos como uma propriedade do módulo de módulo do agente IoT Edge twin.

O [daemon de segurança IoT Edge](iot-edge-security-manager.md) inicia o agente IoT Edge no arranque do dispositivo. O agente obtém seu módulo duplo a partir do IoT Hub e inspeciona o manifesto de implantação. O manifesto de implantação é um ficheiro JSON que declara os módulos que têm de ser iniciado.

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é utilizado pelo agente IoT Edge para controlar o ciclo de vida do módulo. Algumas das propriedades mais interessantes são:

* **definições.image** – A imagem do recipiente que o agente IoT Edge utiliza para iniciar o módulo. O agente IoT Edge deve ser configurado com credenciais para o registo do contentor se a imagem estiver protegida por uma palavra-passe. As credenciais para o registo do contentor podem ser configuradas remotamente utilizando o manifesto de implantação ou no próprio dispositivo IoT Edge, atualizando o ficheiro `config.yaml` na pasta do programa IoT Edge.
* **definições.createOptions** – Uma cadeia que é passada diretamente para o daemon do contentor Moby ao iniciar um recipiente de módulo. A adição de opções nesta propriedade permite configurações avançadas como reencaminhamento de portas ou volumes de montagem no recipiente de um módulo.  
* **estado** – O estado em que o agente IoT Edge coloca o módulo. Normalmente, este valor está definido para *funcionar* como a maioria das pessoas quer que o agente IoT Edge inicie imediatamente todos os módulos do dispositivo. No entanto, pode especificar o estado inicial de um módulo a ser parado e esperar por um futuro tempo para dizer ao agente IoT Edge para iniciar um módulo. O agente IoT Edge relata o estado de cada módulo de volta à nuvem nas propriedades relatadas. Uma diferença entre a propriedade pretendida e a propriedade comunicada é um indicador de um dispositivo com comportamento inadequado. Os Estados suportados são:

  * A transferir
  * A executar
  * Estado de funcionamento incorreto
  * Falhou
  * Parada

* **reiniciar Política** – Como o agente IoT Edge reinicia um módulo. Os valores possíveis incluem:
  
  * `never` – O agente IoT Edge nunca reinicia o módulo.
  * `on-failure` - Se o módulo se despenhar, o agente IoT Edge reinicia-o. Se o módulo desligar-se de forma limpa, o agente IoT Edge não o reinicia.
  * `on-unhealthy` - Se o módulo se despenhar ou for considerado insalubre, o agente IoT Edge reinicia-o.
  * `always` - Se o módulo se despenhar, for considerado insalubre, ou desligar de alguma forma, o agente IoT Edge reinicia-o.

* **imagemPullPolicy** - Quer o agente IoT Edge tente puxar a imagem mais recente para um módulo automaticamente ou não. Se não especificar um valor, o predefinido está *no Create*. Os valores possíveis incluem:

  * `on-create` - Ao iniciar um módulo ou atualizar um módulo com base num novo manifesto de implantação, o agente IoT Edge tentará retirar a imagem do módulo do registo do contentor.
  * `never` - O agente IoT Edge nunca tentará retirar a imagem do módulo do registo do contentor. Com esta configuração, então é responsável por colocar a imagem do módulo no dispositivo e gerir quaisquer atualizações de imagem.

O agente do IoT Edge envia a resposta de runtime para o IoT Hub. Aqui está uma lista de possíveis respostas:
  
* 200 - OK
* 400 - a configuração de implementação está incorreto ou é inválido.
* 417 - O dispositivo não tem um conjunto de configuração de implementação.
* 412 - a versão de esquema na configuração da implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não envia relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para mais informações, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar uma imagem do módulo antes de iniciá-los.

Para mais informações sobre o quadro de segurança Azure IoT Edge, leia sobre o gestor de [segurança IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Passos seguintes

[Compreender os módulos Azure IoT Edge](iot-edge-modules.md)
