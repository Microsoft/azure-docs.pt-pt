---
title: Saiba como o tempo de execução gere os dispositivos - Azure IoT Edge Microsoft Docs
description: Saiba como o tempo de funcionaamento do IoT Edge gere módulos, segurança, comunicação e reporte nos seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 8cbfc374a5964983c43594fef5d97986e51c0d83
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971698"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Compreenda o tempo de execução Azure IoT Edge e a sua arquitetura

O runtime do IoT Edge é uma coleção de programas que transformam um dispositivo num dispositivo do IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para funcionar na borda e comunicar os resultados.

O tempo de execução IoT Edge é responsável pelas seguintes funções nos dispositivos IoT Edge:

* Instale e atualize cargas de trabalho no dispositivo.
* Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
* Certifique-se de que [os módulos IoT Edge](iot-edge-modules.md) estão sempre a funcionar.
* Informe a saúde do módulo na nuvem para monitorização remota.
* Gerir a comunicação entre dispositivos a jusante e dispositivos IoT Edge.
* Gerir a comunicação entre módulos no dispositivo IoT Edge.
* Gerir a comunicação entre o dispositivo IoT Edge e a nuvem.

![O tempo de execução comunica insights e saúde do módulo ao IoT Hub](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do tempo de funcionação do IoT Edge enquadram-se em duas categorias: comunicação e gestão de módulos. Estas duas funções são executadas por dois componentes que fazem parte do tempo de execução IoT Edge.O *hub IoT Edge* é responsável pela comunicação, enquanto o agente *IoT Edge* implementa e monitoriza os módulos.

Tanto o hub IoT Edge como o agente IoT Edge são módulos, tal como qualquer outro módulo que funciona num dispositivo IoT Edge. Às vezes são referidos como os módulos de tempo de *funcionação.*

## <a name="iot-edge-hub"></a>Hub IoT Edge

O hub IoT Edge é um dos dois módulos que compõem o tempo de execução Azure IoT Edge. Atua como um representante local para o IoT Hub expondo os mesmos pontos finais do protocolo que o IoT Hub. Esta consistência significa que os clientes (sejam dispositivos ou módulos) podem ligar-se ao tempo de funcionaamento do IoT Edge tal como fariam com o IoT Hub.

>[!NOTE]
> O hub IoT Edge suporta clientes que se conectam usando MQTT ou AMQP. Não suporta clientes que utilizem HTTP.

O hub IoT Edge não é uma versão completa do IoT Hub a funcionar localmente. O hub IoT Edge delega silenciosamente algumas tarefas para o IoT Hub. Por exemplo, o hub IoT Edge encaminha pedidos de autenticação para o IoT Hub quando um dispositivo tenta ligar-se pela primeira vez. Após a primeira ligação ser estabelecida, as informações de segurança são colocadas localmente pelo hub IoT Edge. As futuras ligações a partir desse dispositivo são permitidas sem ter de autenticar novamente a nuvem.

Para reduzir a largura de banda que a sua solução IoT Edge utiliza, o hub IoT Edge otimiza quantas ligações reais são feitas à nuvem. O hub IoT Edge retira ligações lógicas de módulos ou dispositivos a jusante e combina-as para uma única ligação física à nuvem. Os detalhes deste processo são transparentes para o resto da solução. Os clientes acham que têm a sua própria ligação com a nuvem, mesmo estando a ser enviados pela mesma ligação.

![IoT Edge hub é uma porta de entrada entre dispositivos físicos e IoT Hub](./media/iot-edge-runtime/Gateway.png)

O hub IoT Edge pode determinar se está ligado ao IoT Hub. Se a ligação for perdida, o hub IoT Edge guarda mensagens ou duas atualizações localmente. Uma vez restabelecida uma ligação, sincroniza todos os dados. A localização utilizada para esta cache temporária é determinada por uma propriedade do módulo twin do hub IoT Edge. O tamanho da cache não está tapado e crescerá enquanto o dispositivo tiver capacidade de armazenamento.Para obter mais informações, consulte [as capacidades offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação de módulos

O hub IoT Edge facilita a comunicação do módulo para o módulo. A utilização do hub IoT Edge como corretor de mensagens mantém os módulos independentes uns dos outros. Os módulos apenas precisam de especificar as entradas nas quais aceitam mensagens e as saídas às quais escrevem mensagens. Um desenvolvedor de soluções pode coser estas entradas e saídas em conjunto para que os módulos processem dados na ordem específica para essa solução.

![IoT Edge Hub facilita comunicação módulo-módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados para o hub IoT Edge, um módulo chama o método SendEventAsync. O primeiro argumento especifica em que saída enviar a mensagem. O pseudocódigo que se segue envia uma mensagem na **saída1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Para receber uma mensagem, registe uma chamada que processa as mensagens que chegam numa entrada específica. O pseudocódigo seguinte regista a mensagem de função O Processor a utilizar para processar todas as mensagens recebidas no **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe MóduloClient e seus métodos de comunicação, consulte a referência API para a sua língua SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient) [Java,](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

O desenvolvedor de soluções é responsável por especificar as regras que determinam como o hub IoT Edge transmite mensagens entre módulos. As regras de encaminhamento são definidas na nuvem e empurradas para o hub IoT Edge no seu módulo twin. A mesma sintaxe para as rotas IoT Hub é usada para definir rotas entre módulos em Azure IoT Edge. Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

![As rotas entre os módulos passam pelo hub IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente IoT Edge

O agente IoT Edge é o outro módulo que compõe o tempo de execução Azure IoT Edge. É responsável pela instantânea de módulos, garantindo que continuam a funcionar, e reportando o estado dos módulos de volta ao IoT Hub. Estes dados de configuração são escritos como uma propriedade do módulo de agente IoT Edge twin.

O [daemon de segurança IoT Edge](iot-edge-security-manager.md) inicia o agente IoT Edge no arranque do dispositivo. O agente recupera o módulo gémeo do IoT Hub e inspeciona o manifesto de implantação. O manifesto de implantação é um ficheiro JSON que declara os módulos que precisam de ser iniciados.

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é utilizado pelo agente IoT Edge para controlar o ciclo de vida do módulo. Algumas das propriedades mais interessantes são:

* **definições.imagem** – A imagem do recipiente que o agente IoT Edge utiliza para iniciar o módulo. O agente IoT Edge deve ser configurado com credenciais para o registo do contentor se a imagem estiver protegida por uma palavra-passe. As credenciais para o registo do contentor podem ser configuradas remotamente utilizando o manifesto de implantação, ou no próprio dispositivo IoT Edge, atualizando o `config.yaml` ficheiro na pasta do programa IoT Edge.
* **definições.createOptions** – Uma corda que é passada diretamente para o daemon do contentor Moby ao iniciar um recipiente de módulo. A adição de opções nesta propriedade permite configurações avançadas como o encaminhamento de portas ou a montagem de volumes no contentor de um módulo.  
* **estado** – O estado em que o agente IoT Edge coloca o módulo. Normalmente, este valor está definido para *funcionar,* uma vez que a maioria das pessoas quer que o agente IoT Edge inicie imediatamente todos os módulos do dispositivo. No entanto, pode especificar o estado inicial de um módulo a ser parado e esperar por um tempo futuro para dizer ao agente IoT Edge para iniciar um módulo.O agente IoT Edge reporta o estado de cada módulo de volta à nuvem nas propriedades relatadas. Uma diferença entre a propriedade desejada e a propriedade reportada é um indicador de um dispositivo de mau comportamento. Os estatutos suportados são:

  * Download
  * Em Execução
  * Mau estado de funcionamento
  * Com falhas
  * Parada

* **reiniciarPolícia** – Como o agente IoT Edge reinicia um módulo. Valores possíveis incluem:
  
  * `never` – O agente IoT Edge nunca reinicia o módulo.
  * `on-failure` - Se o módulo falhar, o agente IoT Edge reinicia-o. Se o módulo desligar-se limpo, o agente IoT Edge não o reinicia.
  * `on-unhealthy` - Se o módulo se despenhar ou for considerado insalubre, o agente IoT Edge reinicia-o.
  * `always` - Se o módulo falhar, for considerado insalubre ou se desligar de alguma forma, o agente IoT Edge reinicia-o.

* **imagemPullPolicy** - Se o agente IoT Edge tenta puxar a imagem mais recente para um módulo automaticamente ou não. Se não especificar um valor, o padrão é *onCreate*. Valores possíveis incluem:

  * `on-create` - Ao iniciar um módulo ou atualizar um módulo com base num novo manifesto de implantação, o agente IoT Edge tentará retirar a imagem do módulo do registo do contentor.
  * `never` - O agente IoT Edge nunca tentará retirar a imagem do módulo do registo do contentor. Com esta configuração, é responsável por colocar a imagem do módulo no dispositivo e gerir quaisquer atualizações de imagem.

O agente IoT Edge envia resposta de tempo de execução para o IoT Hub. Aqui está uma lista de possíveis respostas:
  
* 200 - OK
* 400 - A configuração de implantação é mal formada ou inválida.
* 417 - O dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão de esquema na configuração de implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

### <a name="security"></a>Segurança

O agente IoT Edge desempenha um papel crítico na segurança de um dispositivo IoT Edge. Por exemplo, executa ações como verificar a imagem de um módulo antes de começar.

Para obter mais informações sobre o quadro de segurança Azure IoT Edge, leia sobre o [gestor de segurança IoT Edge](iot-edge-security-manager.md).

## <a name="runtime-quality-telemetry"></a>Telemetria de qualidade de tempo de execução

O IoT Edge recolhe telemetria anonimizada a partir do tempo de funcionação do hospedeiro e dos módulos do sistema para melhorar a qualidade do produto. Esta informação chama-se telemetria de qualidade de tempo de execução (RQT). O RQT é periodicamente enviado como mensagens de dispositivo para nuvem para ioT Hub do IoT Edge Agent. As mensagens RQT não aparecem na telemetria regular do cliente e não consomem qualquer quota de mensagem.

Uma lista completa das métricas recolhidas pelo edgeAgent e edgeHub está disponível na [secção métricas disponíveis do artigo de métricas de tempo de execução Access IoT Edge](how-to-access-built-in-metrics.md#available-metrics). Um subconjunto destas métricas é recolhido pelo IoT Edge Agent como parte do RQT. As métricas recolhidas como parte do RQT incluem a `ms_telemetry` etiqueta.

Como parte da anonimização, qualquer informação pessoalmente ou organizacionalmente identificável, como nomes de dispositivos e módulos, são removidas antes do upload.

A frequência padrão de RQT é uma mensagem enviada para o IoT Hub a cada 24 horas e a recolha local por edgeAgent a cada hora.

Se desejar optar por não fazer RQT, existem duas formas de o fazer:

* Definir a `SendRuntimeQualityTelemetry` variável ambiente `false` para **edgeAgent,** ou
* Desmarque a opção no portal Azure durante a implementação.

## <a name="next-steps"></a>Passos seguintes

* [Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)
* [Saiba mais sobre as métricas de tempo de execução ioT Edge](how-to-access-built-in-metrics.md)
