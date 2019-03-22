---
title: Saiba como o tempo de execução gere dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Saiba como o módulos, segurança, comunicação e relatórios sobre os dispositivos são geridos pelo runtime do Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bb2df9c32d5adc8160da82148e4a66a4ab68d182
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311604"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura

O runtime do IoT Edge é uma coleção de programas que têm de ser instalado num dispositivo para o mesmo ser considerado um dispositivo IoT Edge. Coletivamente, os componentes do runtime do IoT Edge permitem que os dispositivos do IoT Edge receber o código para executar na periferia e comunicam os resultados. 

O runtime do IoT Edge efetua as seguintes funções em dispositivos IoT Edge:

* Instalar e atualizar as cargas de trabalho no dispositivo.
* Manter as normas de segurança do Azure IoT Edge no dispositivo.
* Certifique-se de que [módulos do IoT Edge](iot-edge-modules.md) sempre em execução.
* Comunicar o estado de funcionamento do módulo para a cloud para monitorização remota.
* Facilite a comunicação entre os dispositivos de folha a jusante e dispositivos IoT Edge.
* Facilite a comunicação entre os módulos no dispositivo IoT Edge.
* Facilite a comunicação entre o dispositivo do IoT Edge e a cloud.

![Tempo de execução comunica informações e o estado de funcionamento do módulo para o IoT Hub](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do runtime do IoT Edge enquadram-se em duas categorias: gestão de comunicação e o módulo. Estas duas funções são executadas por dois componentes que compõem o runtime do IoT Edge. O *hub do IoT Edge* é responsável pela comunicação, enquanto o *agente do IoT Edge* implementa e monitoriza os módulos. 

O hub IoT Edge e o agente do IoT Edge são módulos, assim como qualquer outro módulo em execução num dispositivo IoT Edge. 

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub do IoT Edge é um dos dois módulos que constituem o tempo de execução do Azure IoT Edge. Ele atua como um proxy local para o IoT Hub ao expor os pontos de extremidade de protocolo mesmo como o IoT Hub. Esta consistência significa que os clientes (se dispositivos ou módulos) pode ligar-se para o runtime do IoT Edge como se fossem ao IoT Hub. 

>[!NOTE]
> Hub do IoT Edge suporta clientes que se ligam através de MQTT ou AMQP. Não suporta clientes que utilizam HTTP. 

O hub do IoT Edge não é uma versão completa do IoT Hub em execução localmente. Existem algumas coisas que o hub IoT Edge silenciosamente delega para o IoT Hub. Por exemplo, o hub do IoT Edge encaminha pedidos de autenticação para o IoT Hub quando um dispositivo primeiro tenta ligar. Após a primeira é estabelecida a ligação, informações de segurança são armazenados em cache localmente pelo hub do IoT Edge. São permitidas ligações subsequentes do que o dispositivo sem precisarem autenticar para a cloud. 

>[!NOTE]
>O tempo de execução tem de estar ligado sempre que tentar autenticar um dispositivo.

Para reduzir a largura de banda sua solução de IoT Edge utiliza, o hub IoT Edge otimiza o número de conexões reais são efetuadas para a cloud. Hub do IoT Edge usa a lógicas ligações de clientes, como módulos ou dispositivos de folha e combina-os para uma única ligação física para a cloud. Os detalhes desse processo são transparentes para o restante da solução. Os clientes acha que eles têm sua própria ligação para a cloud, apesar de tudo o que está a ser enviados pela mesma conexão. 

![Hub do IoT Edge é um gateway entre dispositivos físicos e o IoT Hub](./media/iot-edge-runtime/Gateway.png)

Hub do IoT Edge pode determinar se está ligado ao IoT Hub. Se a ligação for perdida, o hub do IoT Edge guarda as mensagens ou as atualizações de duplo localmente. Depois de uma conexão for restabelecida, ele sincroniza todos os dados. A localização utilizada para esta cache temporário é determinada por uma propriedade do duplo do módulo do hub IoT Edge. O tamanho da cache não está limitado e irá aumentar, desde que o dispositivo tem capacidade de armazenamento. 

### <a name="module-communication"></a>Comunicação de módulo

Hub do IoT Edge facilita a comunicação de módulo de módulo. Com o IoT Edge hub como um mediador de mensagens mantém módulos independentes entre si. Módulos só precisam de especificar as entradas em que aceite mensagens e as saídas para que eles escrevem mensagens. Um desenvolvedor de soluções, em seguida, stitches essas entradas e saídas em conjunto, para que os módulos de processam os dados na ordem específica para essa solução. 

![Hub do IoT Edge facilita a comunicação de módulo de módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados para o hub IoT Edge, um módulo chama o método de SendEventAsync. O primeiro argumento especifica em qual saída deve enviar a mensagem. O seguinte pseudocódigo envia uma mensagem no output1:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Para receber uma mensagem, registe um retorno de chamada que processa as mensagens recebidas numa entrada específica. O seguinte pseudocódigo registra o messageProcessor de função a ser utilizada para processar todas as mensagens recebidas no input1:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência da API para seu idioma preferencial do SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C e Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), ou [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor de soluções é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre módulos. Regras de encaminhamento são definidas na cloud e enviadas ao hub IoT Edge no seu dispositivo duplo. A mesma sintaxe para as rotas do IoT Hub é usada para definir rotas entre módulos no Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar módulos, garantindo que continuam em execução e comunicar o estado dos módulos de volta para o IoT Hub. Assim como qualquer outro módulo, o agente do IoT Edge utiliza o seu módulo duplo para armazenar estes dados de configuração. 

O [daemon de segurança de IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente obtém seu módulo duplo a partir do IoT Hub e inspeciona o manifesto de implantação. O manifesto de implantação é um ficheiro JSON que declara os módulos que têm de ser iniciado. 

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é utilizado pelo agente do IoT Edge para controlar o ciclo de vida do módulo. Algumas das propriedades mais interessantes são: 

* **Settings.Image** – a imagem de contentor que o agente do IoT Edge utiliza para iniciar o módulo. O agente do IoT Edge deve ser configurado com as credenciais para o registo de contentor, se a imagem estiver protegida por uma palavra-passe. As credenciais para o registo de contentor pode ser configurado remotamente utilizando o manifesto de implantação ou no próprio dispositivo IoT Edge, atualizando o `config.yaml` ficheiro na pasta do programa de IoT Edge.
* **settings.createOptions** – uma cadeia de caracteres é passada diretamente para o daemon do Docker ao iniciar o contentor de um módulo. A adição de opções de Docker nessa propriedade permite para obter opções avançadas, como a porta de reencaminhamento ou montar volumes para o contentor de um módulo.  
* **estado** – o estado em que o agente do IoT Edge coloca o módulo. Este valor é geralmente definido para *em execução* como a maioria das pessoas querem o agente do IoT Edge para iniciar imediatamente todos os módulos no dispositivo. No entanto, poderia especificar o estado inicial de um módulo a ser parado e aguarde uma hora no futuro informar o agente do IoT Edge para iniciar um módulo. O agente do IoT Edge relata o status de cada módulo para a cloud nas propriedades comunicadas. Uma diferença entre a propriedade pretendida e a propriedade comunicada é um indicador de um dispositivo com comportamento inadequado. Os Estados suportados são:
   * A transferir
   * A executar
   * Estado de funcionamento incorreto
   * Com Falhas
   * Parada
* **restartPolicy** – como o agente do IoT Edge reinicia um módulo. Os valores possíveis incluem:
   * Nunca – o agente do IoT Edge nunca reinicia o módulo.
   * onFailure - se o módulo falhar, o agente do IoT Edge reinicia-lo. Se o módulo foi encerrado corretamente, o agente do IoT Edge não reiniciá-lo.
   * Mau estado de funcionamento - se o módulo falha ou for considerado mau estado de funcionamento, o agente do IoT Edge reinicia-lo.
   * Sempre - se o módulo falha, é considerado em mau estado de funcionamento ou encerra de qualquer forma, o agente do IoT Edge reinicia-lo. 

O agente do IoT Edge envia a resposta de runtime para o IoT Hub. Aqui está uma lista de possíveis respostas:
  * 200 - OK
  * 400 - a configuração de implementação está incorreto ou é inválido.
  * 417 - o dispositivo não tem um conjunto de configurações de implementação.
  * 412 - a versão de esquema na configuração da implementação é inválida.
  * 406 - o dispositivo do IoT Edge é relatórios de status offline ou não de envio.
  * 500 - Erro no runtime do IoT Edge.

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar uma imagem do módulo antes de iniciá-los. 

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, saiba mais sobre o [Gestor de segurança de IoT Edge](iot-edge-security-manager.md)

## <a name="next-steps"></a>Passos Seguintes

[Compreender o Azure IoT Edge certificados](iot-edge-certs.md)
