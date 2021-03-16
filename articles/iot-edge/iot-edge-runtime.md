---
title: Saiba como o tempo de execução gere os dispositivos - Azure IoT Edge | Microsoft Docs
description: Saiba como o tempo de funcionaamento do IoT Edge gere módulos, segurança, comunicação e reporte nos seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496257"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Compreenda o tempo de execução Azure IoT Edge e a sua arquitetura

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

O runtime do IoT Edge é uma coleção de programas que transformam um dispositivo num dispositivo do IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para funcionar na borda e comunicar os resultados.

O tempo de execução IoT Edge é responsável pelas seguintes funções nos dispositivos IoT Edge:

* Instale e atualize cargas de trabalho no dispositivo.

* Mantenha os padrões de segurança Azure IoT Edge no dispositivo.

* Certifique-se de que [os módulos IoT Edge](iot-edge-modules.md) estão sempre a funcionar.

* Informe a saúde do módulo na nuvem para monitorização remota.

* Gerir a comunicação entre dispositivos a jusante e dispositivos IoT Edge.

* Gerir a comunicação entre módulos num dispositivo IoT Edge.

* Gerencie a comunicação entre um dispositivo IoT Edge e a nuvem.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Gerir a comunicação entre dispositivos IoT Edge.
::: moniker-end

![O tempo de execução comunica insights e saúde do módulo ao IoT Hub](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do tempo de funcionação do IoT Edge enquadram-se em duas categorias: comunicação e gestão de módulos. Estas duas funções são executadas por dois componentes que fazem parte do tempo de execução IoT Edge. O *agente IoT Edge* implanta e monitoriza os módulos, enquanto o hub *IoT Edge* é responsável pela comunicação.

Tanto o agente IoT Edge como o hub IoT Edge são módulos, tal como qualquer outro módulo que funciona num dispositivo IoT Edge. Às vezes são referidos como os módulos de tempo de *funcionação.*

## <a name="iot-edge-agent"></a>Agente IoT Edge

O agente IoT Edge é um dos dois módulos que compõem o tempo de execução Azure IoT Edge. É responsável pela instantânea de módulos, garantindo que continuam a funcionar, e reportando o estado dos módulos de volta ao IoT Hub. Estes dados de configuração são escritos como uma propriedade do módulo de agente IoT Edge twin.

O [daemon de segurança IoT Edge](iot-edge-security-manager.md) inicia o agente IoT Edge no arranque do dispositivo. O agente recupera o módulo gémeo do IoT Hub e inspeciona o manifesto de implantação. O manifesto de implantação é um ficheiro JSON que declara os módulos que precisam de ser iniciados.

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é utilizado pelo agente IoT Edge para controlar o ciclo de vida do módulo. Para obter mais informações sobre todas as propriedades utilizadas pelo agente IoT Edge para controlar módulos, leia sobre as [propriedades do agente IoT Edge e dos gémeos módulos IoT Edge](module-edgeagent-edgehub.md)hub .

O agente IoT Edge envia resposta de tempo de execução para o IoT Hub. Aqui está uma lista de possíveis respostas:
  
* 200 - OK
* 400 - A configuração de implantação é mal formada ou inválida.
* 417 - O dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão de esquema na configuração de implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para obter mais informações sobre a criação de manifestos de implantação, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

### <a name="security"></a>Segurança

O agente IoT Edge desempenha um papel crítico na segurança de um dispositivo IoT Edge. Por exemplo, executa ações como verificar a imagem de um módulo antes de começar.

Para obter mais informações sobre o quadro de segurança Azure IoT Edge, leia sobre o [gestor de segurança IoT Edge](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>Hub IoT Edge

O hub IoT Edge é o outro módulo que compõe o tempo de execução Azure IoT Edge. Atua como um representante local para o IoT Hub expondo os mesmos pontos finais do protocolo que o IoT Hub. Esta consistência significa que os clientes podem ligar-se ao tempo de execução IoT Edge tal como fariam com o IoT Hub.

O hub IoT Edge não é uma versão completa do IoT Hub a funcionar localmente. O hub IoT Edge delega silenciosamente algumas tarefas para o IoT Hub. Por exemplo, o hub IoT Edge descarrega automaticamente as informações de autorização do IoT Hub na sua primeira ligação para permitir a ligação de um dispositivo. Após a primeira ligação ser estabelecida, as informações de autorização são colocadas localmente pelo hub IoT Edge. As futuras ligações desse dispositivo são autorizadas sem ter de descarregar novamente as informações de autorização da nuvem.

### <a name="cloud-communication"></a>Comunicação em nuvem

Para reduzir a largura de banda que a sua solução IoT Edge utiliza, o hub IoT Edge otimiza quantas ligações reais são feitas à nuvem. O hub IoT Edge retira ligações lógicas de módulos ou dispositivos a jusante e combina-as para uma única ligação física à nuvem. Os detalhes deste processo são transparentes para o resto da solução. Os clientes acham que têm a sua própria ligação com a nuvem, mesmo estando a ser enviados pela mesma ligação. O hub IoT Edge pode usar o protocolo AMQP ou O protocolo MQTT para comunicar a montante com a nuvem, independentemente dos protocolos utilizados pelos dispositivos a jusante. No entanto, o hub IoT Edge suporta atualmente apenas combinar ligações lógicas numa única ligação física, utilizando amQP como protocolo a montante e suas capacidades de multiplexing. AMQP é o protocolo a montante padrão.

![IoT Edge hub é uma porta de entrada entre dispositivos físicos e IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

O hub IoT Edge pode determinar se está ligado ao IoT Hub. Se a ligação for perdida, o hub IoT Edge guarda mensagens ou duas atualizações localmente. Uma vez restabelecida uma ligação, sincroniza todos os dados. A localização utilizada para esta cache temporária é determinada por uma propriedade do módulo twin do hub IoT Edge. O tamanho da cache não está tapado e crescerá enquanto o dispositivo tiver capacidade de armazenamento. Para obter mais informações, consulte [as capacidades offline](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

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

Para obter mais informações sobre a classe MóduloClient e seus métodos de comunicação, consulte a referência API para a sua língua SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C,](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient) [Java,](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

O desenvolvedor de soluções é responsável por especificar as regras que determinam como o hub IoT Edge transmite mensagens entre módulos. As regras de encaminhamento são definidas na nuvem e empurradas para o hub IoT Edge no seu módulo twin. A mesma sintaxe para as rotas IoT Hub é usada para definir rotas entre módulos em Azure IoT Edge. Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

![As rotas entre os módulos passam pelo hub IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Comunicação local

O hub IoT Edge facilita a comunicação local. Permite comunicações dispositivo-módulo, módulo-módulo, dispositivo-dispositivo, mediação de mensagens para manter os dispositivos e módulos independentes uns dos outros.

>[!NOTE]
> A funcionalidade de corretor MQTT está em pré-visualização pública com a versão 1.2 do IoT Edge. Deve ser explicitamente ativado.

O hub IoT Edge suporta dois mecanismos de corretagem:

1. As [funcionalidades de encaminhamento de mensagens suportadas pelo IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) e,
2. Um corretor MQTT de uso geral que cumpre a [norma MQTT v3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Utilização de encaminhamento

O primeiro mecanismo de corretagem aproveita as mesmas funcionalidades de encaminhamento que o IoT Hub para especificar como as mensagens são passadas entre dispositivos ou módulos. Os primeiros dispositivos ou módulos especificam as entradas nas quais aceitam mensagens e as saídas às quais escrevem mensagens. Em seguida, um desenvolvedor de soluções pode encaminhar mensagens entre uma fonte, por exemplo, saídas, e um destino, por exemplo, entradas, com filtros potenciais.

![As rotas entre os módulos passam pelo hub IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)

O encaminhamento pode ser utilizado por dispositivos ou módulos construídos com os SDKs do dispositivo Azure IoT, quer através do AMQP, quer através do protocolo MQTT. Todos os primitivos IoT Hub de mensagens, por exemplo, telemetria, métodos diretos, C2D, gémeos, são suportados, mas a comunicação sobre tópicos definidos pelo utilizador não é suportada.

Para obter mais informações sobre rotas, consulte [Como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Utilização do corretor MQTT

O segundo mecanismo de corretagem baseia-se num corretor padrão MQTT. MQTT é um protocolo de transferência de mensagens leves que garante desempenhos ideais em dispositivos restritos a recursos e é um padrão popular de publicação e subscrição. Dispositivos ou módulos subscrevem tópicos para receber mensagens publicadas por outros dispositivos ou módulos. O hub IoT Edge implementa o seu próprio corretor MQTT que segue [as especificações da versão MQTT 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

O corretor MQTT permite dois padrões de comunicação adicionais em comparação com o encaminhamento: radiodifusão local e comunicação ponto-a-ponto. A radiodifusão local é útil quando um dispositivo ou módulo precisa alertar localmente vários outros dispositivos ou módulos. A comunicação ponto-a-ponto permite que dois dispositivos IoT Edge ou dois dispositivos IoT se comuniquem localmente sem ida e volta para a nuvem.

![Publicar e subscrever localmente com o ioT Edge hub](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

O corretor MQTT pode ser utilizado por dispositivos ou módulos construídos com os SDKs de dispositivoS Azure IoT que comunicam através do protocolo MQTT ou de qualquer cliente MQTT de uso geral. Com exceção do C2D, todos os primitivos IoT Hub de mensagens, por exemplo, telemetria, métodos diretos, gémeos são apoiados. Os tópicos especiais IoT Hub utilizados pelos primitivos IoT Hub são suportados e assim são tópicos definidos pelo utilizador.
Este tópico pode ser um tópico especial do IoT Hub ou um tópico definido pelo utilizador.

Ao contrário do mecanismo de encaminhamento, encomendar mensagens é apenas o melhor esforço e não é garantido e a filtragem de mensagens não é suportada pelo corretor. No entanto, a falta destas funcionalidades permite que o corretor MQTT seja mais rápido do que o encaminhamento.

Para mais informações sobre o corretor MQTT, consulte [Publicar e subscrever com a IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Comparação entre mecanismos de corretagem

Aqui estão as funcionalidades disponíveis com cada mecanismo de corretagem:

|Funcionalidades  | Encaminhamento  | Corretor MQTT  |
|---------|---------|---------|
|Telemetria D2C    |     &#10004;    |         |
|Telemetria local     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Gémeo     |    &#10004;     |    &#10004;     |
|C2D para dispositivos     |   &#10004;      |         |
|Ordenação     |    &#10004;     |         |
|Filtragem     |     &#10004;    |         |
|Tópicos definidos pelo utilizador     |         |    &#10004;     |
|Dispositivo-a-Dispositivo     |         |    &#10004;     |
|Radiodifusão local     |         |    &#10004;     |
|Desempenho     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Ligação ao hub IoT Edge

O hub IoT Edge aceita ligações de clientes de dispositivos ou módulos, quer através do protocolo MQTT, quer do protocolo AMQP.

>[!NOTE]
> O hub IoT Edge suporta clientes que se conectam usando MQTT ou AMQP. Não suporta clientes que utilizem HTTP.

Quando um cliente se conecta ao hub IoT Edge, acontece o seguinte:

1. Se for utilizado o Transporte Layer Security (TLS), é construído um canal TLS para estabelecer uma comunicação encriptada entre o cliente e o hub IoT Edge.
2. As informações de autenticação são enviadas do cliente para o hub IoT Edge para se identificar.
3. O hub IoT Edge autoriza ou rejeita a ligação com base na sua política de autorização.

#### <a name="secure-connections-tls"></a>Ligações seguras (TLS)

Por padrão, o hub IoT Edge apenas aceita ligações seguras com a Segurança da Camada de Transporte (TLS), por exemplo, ligações encriptadas que um terceiro não pode desencriptar.

Se um cliente ligar na porta 8883 (MQTTS) ou 5671 (AMQPS) ao hub IoT Edge, deve ser construído um canal TLS. Durante o aperto de mão TLS, o hub IoT Edge envia a sua cadeia de certificados que o cliente precisa de validar. Para validar a cadeia de certificados, o certificado de raiz do hub IoT Edge deve ser instalado como um certificado de confiança no cliente. Se o certificado de raiz não for confiável, a biblioteca do cliente será rejeitada pelo hub IoT Edge com um erro de verificação do certificado.

Os passos a seguir para instalar este certificado de raiz do corretor nos clientes do dispositivo são descritos no [gateway transparente](how-to-create-transparent-gateway.md) e na preparação de uma documentação do dispositivo a [jusante.](how-to-connect-downstream-device.md#prepare-a-downstream-device) Os módulos podem utilizar o mesmo certificado de raiz que o hub IoT Edge, aproveitando a API daemon IoT Edge.

#### <a name="authentication"></a>Autenticação

O IoT Edge Hub só aceita ligações de dispositivos ou módulos com identidade IoT Hub, por exemplo, que tenham sido registados no IoT Hub e tenham um dos três métodos de autenticação do cliente suportados pelo hub IoT para fornecer comprovação da sua identidade: [Autenticação de chaves simétricas,](how-to-authenticate-downstream-device.md#symmetric-key-authentication) [autenticação auto-assinada X.509,](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) [autenticação assinada X.509 CA](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Estas identidades do IoT Hub podem ser verificadas localmente pelo hub IoT Edge para que as ligações ainda possam ser feitas offline.

Notas:

* Atualmente, os módulos IoT Edge suportam apenas a autenticação da chave simétrica.
* Os clientes MQTT com apenas o nome de utilizador local e palavras-passe não são aceites pelo corretor MQTT hub IoT Edge, devem usar identidades IoT Hub.

#### <a name="authorization"></a>Autorização

Uma vez autenticado, o hub IoT Edge tem duas formas de autorizar ligações ao cliente:

* Verificando se um cliente pertence ao seu conjunto de clientes de confiança definidos no IoT Hub. O conjunto de clientes de confiança é especificado através da configuração de relações pai/filho ou dispositivo/módulo no IoT Hub. Quando um módulo é criado no IoT Edge, uma relação de confiança é automaticamente estabelecida entre este módulo e o seu dispositivo IoT Edge. Este é o único modelo de autorização suportado pelo mecanismo de corretagem de encaminhamento.

* Estabelecendo uma política de autorização. Esta política de autorização é um documento que lista todas as identidades autorizadas do cliente que podem aceder aos recursos no hub IoT Edge. Este é o modelo de autorização primária usado pelo corretor MQTT hub IoT Edge, embora as relações entre pais e filhos e dispositivos/módulos também possam ser compreendidas pelo corretor MQTT para tópicos IoT Hub.

### <a name="remote-configuration"></a>Configuração remota

O hub IoT Edge é totalmente controlado pela nuvem. Obtém a sua configuração a partir do IoT Hub através do seu [módulo twin](iot-edge-modules.md#module-twins). Inclui:

* Configuração de rotas
* Políticas de autorização
* Configuração da ponte MQTT

Além disso, várias configurações podem ser feitas através da configuração de [variáveis ambientais no hub IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetria de qualidade de tempo de execução

O IoT Edge recolhe telemetria anónima a partir do tempo de funcionação do hospedeiro e dos módulos do sistema para melhorar a qualidade do produto. Esta informação chama-se telemetria de qualidade de tempo de execução. A telemetria recolhida é periodicamente enviada como mensagens de dispositivo para nuvem para ioT Hub do agente IoT Edge. Estas mensagens não aparecem na telemetria regular do cliente e não consomem qualquer quota de mensagem.

O agente IoT Edge e o hub geram métricas que pode recolher para entender o desempenho do dispositivo. Um subconjunto destas métricas é recolhido pelo IoT Edge Agent como parte da telemetria de qualidade de tempo de execução. As métricas recolhidas para telemetria de qualidade de tempo de funcionação são rotuladas com a etiqueta `ms_telemetry` . Para obter informações sobre todas as métricas disponíveis, consulte [as métricas de acesso incorporadas.](how-to-access-built-in-metrics.md)

Qualquer informação pessoalmente ou organizacionalmente identificável, como nomes de dispositivos e módulos, é removida antes do upload para garantir a natureza anónima da telemetria de qualidade de tempo de execução.

O agente IoT Edge recolhe a telemetria a cada hora e envia uma mensagem para o IoT Hub a cada 24 horas.

Se pretender não enviar telemetria de tempo de execução dos seus dispositivos, existem duas formas de o fazer:

* Definir a `SendRuntimeQualityTelemetry` variável ambiente `false` para **edgeAgent,** ou
* Desmarque a opção no portal Azure durante a implementação.

## <a name="next-steps"></a>Passos seguintes

* [Compreender os módulos do Azure IoT Edge](iot-edge-modules.md)
* [Saiba como implementar módulos e estabelecer rotas no IoT Edge](module-composition.md)
* [Saiba como publicar e subscrever com o IoT Edge](how-to-publish-subscribe.md)
* [Saiba mais sobre as métricas de tempo de execução ioT Edge](how-to-access-built-in-metrics.md)
