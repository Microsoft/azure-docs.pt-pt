---
title: Publique e subscreva com a Azure IoT Edge | Microsoft Docs
description: Utilize o corretor IoT Edge MQTT para publicar e subscrever mensagens
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: e4043fd8b7c9571b62cbf65d7398754b27375efd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633976"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publicar e subscrever com a Azure IoT Edge

Pode utilizar o corretor MQTT Azure IoT Edge para publicar e subscrever mensagens. Este artigo mostra-lhe como ligar a este corretor, publicar e subscrever mensagens sobre tópicos definidos pelo utilizador, e usar primitivos de mensagens IoT Hub. O corretor IoT Edge MQTT é incorporado no hub IoT Edge. Para obter mais informações, consulte [as capacidades de corretagem do hub IoT Edge](iot-edge-runtime.md).

> [!NOTE]
> O corretor IoT Edge MQTT está atualmente em pré-visualização pública.

## <a name="pre-requisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição válida
- [Azure CLI](/cli/azure/) com a `azure-iot` extensão CLI instalada. Para mais informações, consulte [os passos de instalação da extensão Azure IoT para Azure Azure CLI](/cli/azure/azure-cli-reference-for-iot).
- Um **hub IoT** de SKU ou F1, S1, S2 ou S3.
- Tenha um **dispositivo IoT Edge com a versão 1.2 ou superior**. Uma vez que o corretor MQTT IoT Edge está atualmente em pré-visualização pública, deite as seguintes variáveis ambientais para verdade no recipiente edgeHub para permitir o corretor MQTT:

   | Name | Valor |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Clientes Mosquitto instalados** no dispositivo IoT Edge. Este artigo usa os populares clientes Mosquitto [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) e [MOSQUITTO_SUB.](https://mosquitto.org/man/mosquitto_sub-1.html) Outros clientes MQTT poderiam ser usados em vez disso. Para instalar os clientes Mosquitto num dispositivo Ubuntu, executar o seguinte comando:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Não instale o servidor Mosquitto, uma vez que pode causar o bloqueio das portas MQTT (8883 e 1883) e entrar em conflito com o hub IoT Edge.

## <a name="connect-to-iot-edge-hub"></a>Ligue-se ao hub IoT Edge

A ligação ao hub IoT Edge segue os mesmos passos descritos na [ligação ao IoT Hub com um artigo genérico do cliente MQTT](../iot-hub/iot-hub-mqtt-support.md) ou na [descrição conceptual do artigo do hub IoT Edge](iot-edge-runtime.md). Estes passos são:

1. Opcionalmente, o cliente MQTT estabelece uma *ligação segura* com o hub IoT Edge usando a Segurança da Camada de Transporte (TLS)
2. O cliente MQTT *autentica-se* no hub IoT Edge
3. O hub IoT Edge *autoriza* o cliente MQTT pela sua política de autorização

### <a name="secure-connection-tls"></a>Ligação segura (TLS)

A Transport Layer Security (TLS) é utilizada para estabelecer uma comunicação encriptada entre o cliente e o hub IoT Edge.

Para desativar o TLS, utilize a porta 1883 (MQTT) e ligue o recipiente edgeHub à porta 1883.

Para ativar o TLS, se um cliente ligar na porta 8883 (MQTTS) ao corretor MQTT, será iniciado um canal TLS. O corretor envia a sua cadeia de certificados que o cliente precisa de validar. Para validar a cadeia de certificados, o certificado raiz do corretor MQTT deve ser instalado como um certificado de confiança no cliente. Se o certificado de raiz não for confiável, a biblioteca do cliente será rejeitada pelo corretor MQTT com um erro de verificação do certificado. Os passos a seguir para instalar este certificado de raiz do corretor no cliente são os mesmos que no [caso](how-to-create-transparent-gateway.md) transparente gateway e são descritos na preparação de uma documentação do dispositivo a [jusante.](how-to-connect-downstream-device.md#prepare-a-downstream-device)

### <a name="authentication"></a>Autenticação

Para que um cliente MQTT se autense, primeiro precisa enviar um pacote CONNECT para o corretor MQTT para iniciar uma ligação em seu nome. Este pacote fornece três peças de informação de autenticação: `client identifier` a, `username` a, e a `password` :

- O `client identifier` campo é o nome do dispositivo ou nome do módulo no IoT Hub. Utiliza a seguinte sintaxe:

  - Para um dispositivo: `<device_name>`

  - Para um módulo: `<device_name>/<module_name>`

   Para se ligar ao corretor MQTT, um dispositivo ou um módulo devem ser registados no IoT Hub.

   O corretor não permitirá ligações de vários clientes usando as mesmas credenciais. O corretor desligará o cliente já ligado se um segundo cliente ligar usando as mesmas credenciais.

- O `username` campo é derivado do nome do dispositivo ou módulo, e o nome IoTHub do dispositivo pertence à utilização da seguinte sintaxe:

  - Para um dispositivo: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Para um módulo: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- O `password` campo do pacote CONNECT depende do modo de autenticação:

  - Ao utilizar [a autenticação de teclas simétricas,](how-to-authenticate-downstream-device.md#symmetric-key-authentication)o `password` campo é um símbolo SAS.
  - Ao utilizar a [autenticação auto-assinada X.509,](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)o `password` campo não está presente. Neste modo de autenticação, é necessário um canal TLS. O cliente precisa de ligar à porta 8883 para estabelecer uma ligação TLS. Durante o aperto de mão TLS, o corretor MQTT solicita um certificado de cliente. Este certificado é utilizado para verificar a identidade do cliente e, portanto, o `password` campo não é necessário mais tarde quando o pacote CONNECT é enviado. O envio de um certificado de cliente e do campo de palavra-passe levará a um erro e a ligação será encerrada. As bibliotecas MQTT e as bibliotecas de clientes TLS geralmente têm uma maneira de enviar um certificado de cliente ao iniciar uma ligação. Pode ver um exemplo passo a passo na secção [Usando o Certificado X509 para a autenticação do cliente.](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)

Os módulos implantados pelo IoT Edge utilizam [a autenticação de chaves simétricas](how-to-authenticate-downstream-device.md#symmetric-key-authentication) e podem chamar a API de [carga de trabalho de IoT Edge](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) local para obter programáticamente um token SAS mesmo quando offline.

### <a name="authorization"></a>Autorização

Uma vez que um cliente MQTT é autenticado no hub IoT Edge, ele precisa ser autorizado a ligar. Uma vez ligado, tem de ser autorizado a publicar ou subscrever tópicos específicos. Estas autorizações são concedidas pelo hub IoT Edge com base na sua política de autorização. A política de autorização é um conjunto de declarações expressas como uma estrutura JSON que é enviada para o hub IoT Edge através do seu gémeo. Editar um hub twin IoT Edge para configurar a sua política de autorização.

> [!NOTE]
> Para a pré-visualização pública, a edição das políticas de autorização do corretor MQTT só está disponível através do Visual Studio, Visual Studio Code ou do Azure CLI. O portal Azure não suporta atualmente a edição do hub twin IoT Edge e a sua política de autorização.

Cada declaração de política de autorização consiste na combinação `identities` de, `allow` ou `deny` `operations` efeitos, e `resources` :

- `identities` descrever o tema da política. Deve mapear para o `client identifier` enviado pelos clientes no seu pacote CONNECT.
- `allow` ou `deny` os efeitos definem se permite ou nega operações.
- `operations` definir as ações para autorizar. `mqtt:connect`, `mqtt:publish` e são as três `mqtt:subscribe` ações apoiadas hoje.
- `resources` definir o objeto da política. Pode ser um tópico ou um padrão tópico definido com [wildcards MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)

Abaixo está um exemplo de uma política de autorização que explicitamente não permite que o cliente "rogue_client" se conecte, permite que qualquer cliente Azure IoT se conecte e permite que "sensor_1" publiquem `events/alerts` tópicos.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Algumas coisas a ter em mente ao escrever a sua política de autorização:

- Requer `$edgeHub` dois esquemas versão 1.2
- Por defeito, todas as operações são negadas.
- As declarações de autorização são avaliadas na ordem em que aparecem na definição JSON. Começa por olhar `identities` e, em seguida, selecione o primeiro permitir ou negar declarações que correspondam ao pedido. Em caso de conflitos entre permitir e negar declarações, a declaração de negação vence.
- Várias variáveis (por exemplo, substituições) podem ser utilizadas na política de autorização:
    - `{{iot:identity}}` representa a identidade do cliente atualmente ligado. Por exemplo, uma identidade do dispositivo ou `myDevice` uma identidade de módulo como `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` representa a identidade do dispositivo atualmente ligado. Por exemplo, uma identidade do dispositivo ou `myDevice` a identidade do dispositivo onde um módulo está a funcionar como `myEdgeDevice` .
    - `{{iot:module_id}}` representa a identidade do módulo atualmente ligado. Esta variável está em branco para dispositivos conectados, ou uma identidade de módulo como `SampleModule` .
    - `{{iot:this_device_id}}` representa a identidade do dispositivo IoT Edge que executa a política de autorização. Por exemplo, `myIoTEdgeDevice`.

As autorizações para tópicos de hub IoT são tratadas de forma ligeiramente diferente dos tópicos definidos pelo utilizador. Aqui estão os pontos-chave a lembrar:

- Os dispositivos ou módulos Azure IoT precisam de uma regra de autorização explícita para se ligarem ao corretor MQTT do hub IoT Edge. Uma política de autorização de ligação por defeito é fornecida abaixo.
- Os dispositivos ou módulos Azure IoT podem aceder aos seus próprios tópicos de hub IoT por padrão sem qualquer regra de autorização explícita. No entanto, as autorizações decorrem das relações entre pais e filhos neste caso e essas relações devem ser definidas. Os módulos IoT Edge são automaticamente definidos como crianças do seu dispositivo IoT Edge, mas os dispositivos precisam de ser explicitamente definidos como crianças do seu gateway IoT Edge.

Aqui está uma política de autorização padrão que pode ser usada para permitir que todos os dispositivos ou módulos Azure IoT **se conectem** ao corretor:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Agora que entende como se conectar com o corretor IoT Edge MQTT, vamos ver como pode ser usado para publicar e subscrever mensagens primeiro em tópicos definidos pelo utilizador, depois em tópicos de hub IoT e, finalmente, para outro corretor MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publicar e subscrever tópicos definidos pelo utilizador

Neste artigo, você usará um cliente chamado **sub_client** que subscreve um tópico e outro cliente chamado **pub_client** que publica para um tópico. Utilizaremos a [autenticação da chave simétrica,](how-to-authenticate-downstream-device.md#symmetric-key-authentication) mas o mesmo pode ser feito com [a autenticação auto-assinada X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) ou [com a autenticação assinada por X.509 CA.](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)

### <a name="create-publisher-and-subscriber-clients"></a>Criar clientes editores e assinantes

Crie dois dispositivos IoT no IoT Hub e obtenha as suas palavras-passe. Utilizando o CLI Azure do seu terminal para:

1. Crie dois dispositivos IoT no IoT Hub, pai-los no seu dispositivo IoT Edge:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Obtenha as suas palavras-passe gerando um token SAS:

    - Para um dispositivo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       onde 3600 é a duração do token SAS em segundos (por exemplo, 3600 = 1 hora).
    
    - Para um módulo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       onde 3600 é a duração do token SAS em segundos (por exemplo, 3600 = 1 hora).

3. Copie o token SAS, que é o valor correspondente à tecla "sas" da saída. Aqui está um exemplo de saída do comando Azure CLI acima:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorizar clientes editores e assinantes

Para autorizar o editor e o assinante, edite o hub twin IoT Edge criando uma implementação IoT Edge através do código Azure CLI, Visual Studio ou Visual Studio para incluir a seguinte política de autorização:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Autenticação de chaves simétricas sem TLS

#### <a name="subscribe"></a>Subscrever

Ligue o seu cliente MQTT **sub_client** ao corretor MQTT e subscreva o `test_topic` através da execução do seguinte comando no seu dispositivo IoT Edge:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

onde `<edge_device_address>`  =  `localhost` neste exemplo desde que o cliente está a funcionar no mesmo dispositivo que o IoT Edge.

Note que a porta 1883 (MQTT), sem TLS, é utilizada neste primeiro exemplo. Outro exemplo com a porta 8883 (MQTTS), com TLS ativado, é mostrado na secção seguinte.

O **sub_client** cliente MQTT já começou e está à espera de mensagens `test_topic` recebidas.

#### <a name="publish"></a>Publicar

Ligue **o** seu pub_client cliente MQTT ao corretor MQTT e publique uma mensagem na mesma `test_topic` que acima, executando o seguinte comando no seu dispositivo IoT Edge a partir de outro terminal:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

onde `<edge_device_address>`  =  `localhost` neste exemplo desde que o cliente está a funcionar no mesmo dispositivo que o IoT Edge.

Executando o comando, o **cliente sub_client** MQTT recebe a mensagem "olá".

### <a name="symmetric-keys-authentication-with-tls"></a>Autenticação de chaves simétricas com TLS

Para ativar o TLS, a porta deve ser alterada de 1883(MQTT) para 8883(MQTTS) e os clientes devem ter o certificado raiz do corretor MQTT para poder validar a cadeia de certificados enviada pelo corretor MQTT. Isto pode ser feito seguindo os passos fornecidos na secção [Ligação Secure (TLS)](#secure-connection-tls).

Como os clientes estão a funcionar no mesmo dispositivo que o corretor MQTT no exemplo acima, os mesmos passos aplicam-se para ativar o TLS apenas alterando o número de porta de 1883 (MQTT) para 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publicar e subscrever tópicos do IoT Hub

Os [SDKs de Dispositivo Azure IoT](https://github.com/Azure/azure-iot-sdks) já permitem que os clientes realizem operações IoT Hub, mas não permitem a publicação/subscrição de tópicos definidos pelo utilizador. As operações do IoT Hub podem ser realizadas utilizando quaisquer clientes MQTT que utilizem a semântica de publicação e subscrevam semântica desde que os protocolos primitivos do hub IoT sejam respeitados. Vamos analisar as especificidades para entender como estes protocolos funcionam.

### <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria para o IoT Hub

O envio de dados de telemetria para o IoT Hub é semelhante à publicação de um tópico definido pelo utilizador, mas utilizando um tópico específico do IoT Hub:

- Para um dispositivo, a telemetria é enviada sobre o tópico: `devices/<device_name>/messages/events/`
- Para um módulo, a telemetria é enviada sobre o tópico: `devices/<device_name>/<module_name>/messages/events/`

Além disso, crie uma rota como `FROM /messages/* INTO $upstream` enviar telemetria do corretor IoT Edge MQTT para o hub IoT. Para saber mais sobre o encaminhamento, consulte [as rotas de declarar.](module-composition.md#declare-routes)

### <a name="get-twin"></a>Obter gémeos

Obter o dispositivo/módulo twin não é um padrão típico de MQTT. O cliente precisa emitir um pedido para o gémeo que o IoT Hub vai servir.

Para receber gémeos, o cliente precisa subscrever um tópico específico do IoT `$iothub/twin/res/#` Hub. Este nome tópico é herdado do IoT Hub, e todos os clientes precisam subscrever o mesmo tópico. Não significa que os dispositivos ou módulos recebam o gémeo uns dos outros. O ioT Hub e o ioT Edge hub sabem qual o gémeo que deve ser entregue onde, mesmo que todos os dispositivos ouçam o mesmo nome tópico. 

Uma vez feita a subscrição, o cliente precisa de pedir o gémeo publicando uma mensagem para um tópico específico do IoT Hub `$iothub/twin/GET/?rid=<request_id>/#` onde  `<request_id>` é um identificador arbitrário. O hub IoT enviará então a sua resposta com os dados solicitados sobre o tema `$iothub/twin/res/200/?rid=<request_id>` , a que o cliente subscreve. É assim que um cliente pode emparelhar os seus pedidos com as respostas.

### <a name="receive-twin-patches"></a>Receber patches gémeos

Para receber patches duplos, um cliente precisa subscrever o tópico especial IoTHub `$iothub/twin/PATCH/properties/desired/#` . Uma vez feita a subscrição, o cliente recebe os patches gémeos enviados pela IoT Hub sobre este tema. 

### <a name="receive-direct-methods"></a>Receber métodos diretos

Receber um método direto é semelhante a receber gémeos completos com a adição de que o cliente precisa confirmar que recebeu a chamada. Em primeiro lugar, o cliente subscreve o tópico especial do hub `$iothub/methods/POST/#` IoT. Em seguida, uma vez que um método direto é recebido sobre este tópico, o cliente precisa extrair o identificador de pedido `rid` do sub-tópico em que o método direto é recebido e finalmente publicar uma mensagem de confirmação sobre o tópico especial do hub IoT `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Enviar métodos diretos

Enviar um método direto é uma chamada HTTP e, portanto, não passa pelo corretor MQTT. Para enviar um método direto para o hub IoT, consulte [compreender e invocar métodos diretos.](../iot-hub/iot-hub-devguide-direct-methods.md) Para enviar um método direto localmente para outro módulo, consulte este [exemplo de invocação do método direto Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publicar e subscrever entre corretores MQTT

Para ligar dois corretores MQTT, o hub IoT Edge inclui uma ponte MQTT. Uma ponte MQTT é comumente usada para ligar um corretor MQTT correndo a outro corretor MQTT. Apenas um subconjunto do tráfego local é normalmente empurrado para outro corretor.

> [!NOTE]
> A ponte hub IoT Edge só pode ser utilizada entre dispositivos IoT Edge aninhados. Não pode ser usado para enviar dados para o hub IoT, uma vez que o hub IoT não é um corretor MQTT completo. Para saber mais suporte de corretor MQTT hub IoT, consulte [Comunicar com o seu hub IoT utilizando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md). Para saber mais sobre os dispositivos IoT Edge de nidificação, consulte [conecte um dispositivo IoT Edge a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

Numa configuração aninhada, a ponte MQTT do hub IoT Edge atua como cliente do corretor MQTT, pelo que as regras de autorização devem ser definidas no EdgeHub principal para permitir que o edgeHub da criança publique e subscreva tópicos específicos definidos pelo utilizador para os quais a ponte está configurada.

A ponte IoT Edge MQTT é configurada através de uma estrutura JSON que é enviada para o hub IoT Edge através do seu gémeo. Editar um hub IoT Edge twin para configurar a sua ponte MQTT.

> [!NOTE]
> Para a pré-visualização pública, a configuração da ponte MQTT só está disponível via Visual Studio, Visual Studio Code ou Azure CLI. O portal Azure não suporta atualmente a edição do hub twin IoT Edge e a sua configuração de ponte MQTT.

A ponte MQTT pode ser configurada para ligar um corretor MQTT do hub IoT Edge a vários corretores externos. Para cada corretor externo, são necessárias as seguintes definições:

- `endpoint` é o endereço do corretor MQTT remoto a que se conecta. Apenas os dispositivos IoT Edge dos pais são atualmente suportados e são definidos pela variável `$upstream` .
- `settings` define quais tópicos para fazer a ponte para um ponto final. Pode haver várias configurações por ponto final e os seguintes valores são usados para configurá-lo:
    - `direction`: quer `in` subscrever os tópicos do corretor remoto ou `out` publicar os tópicos do corretor remoto
    - `topic`: padrão tópico central a ser combinado. [Os wildcards MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) podem ser usados para definir este padrão. Prefixos diferentes podem ser aplicados a este padrão tópico no corretor local e corretor remoto.
    - `outPrefix`: Prefixo que é aplicado ao `topic` padrão no corretor remoto.
    - `inPrefix`: Prefixo que é aplicado ao `topic` padrão no corretor local.

Abaixo está um exemplo de uma configuração de ponte IoT Edge MQTT que republica todas as mensagens recebidas sobre tópicos `alerts/#` de um dispositivo IoT Edge para um dispositivo IoT Edge infantil sobre os mesmos tópicos, e republica todas as mensagens enviadas sobre tópicos `/local/telemetry/#` de um dispositivo IoT Edge infantil para um dispositivo IoT Edge dos pais sobre `/remote/messages/#` tópicos.

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Outras notas sobre a ponte MQTT do hub IoT Edge:
- O protocolo MQTT será automaticamente utilizado como protocolo a montante quando o corretor MQTT for utilizado e que o IoT Edge é utilizado numa configuração aninhada, por exemplo, com um `parent_hostname` especificado. Para saber mais sobre os protocolos a montante, consulte [a comunicação cloud](iot-edge-runtime.md#cloud-communication). Para saber mais sobre configurações aninhadas, consulte [ligar um dispositivo IoT Edge a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Passos seguintes

[Compreenda o hub IoT Edge](iot-edge-runtime.md#iot-edge-hub)
