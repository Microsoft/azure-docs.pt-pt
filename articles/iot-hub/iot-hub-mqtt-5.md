---
title: Suporte Azure IoT Hub MQTT 5 (pré-visualização)
description: Saiba mais sobre o suporte MQTT 5 do IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603554"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Visão geral de suporte do IoT Hub MQTT 5 (pré-visualização)

**Versão:** 2.0 **versão api:** 2020-10-01-pré-visualização

Este documento define o plano de dados IoT Hub API sobre o protocolo MQTT versão 5.0. Consulte [a API Reference](iot-hub-mqtt-5-reference.md) para definições completas nesta API.

## <a name="prerequisites"></a>Pré-requisitos

- [Ativar o modo de pré-visualização](iot-hub-preview-mode.md) num novo hub IoT para experimentar o MQTT 5.
- É necessário conhecimento prévio da [especificação MQTT 5.](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html)

## <a name="level-of-support-and-limitations"></a>Nível de apoio e limitações

O suporte do IoT Hub para MQTT 5 está em pré-visualização e limitado de formas seguintes (comunicado ao cliente através de `CONNACK` propriedades, salvo indicação explícita em contrário):

- Ainda não há suporte oficial do [Azure IoT Hub SDK.](iot-hub-devguide-sdks.md)
- Os identificadores de assinatura não são suportados.
- As subscrições partilhadas não são suportadas.
- `RETAIN` não é apoiado.
- `Maximum QoS` é `1`.
- `Maximum Packet Size` está `256 KiB` (sujeito a novas restrições por operação).
- As identificações de clientes atribuídas não são suportadas.
- `Keep Alive` limita-se `19 min` a (atraso máximo para verificação de vida – `28.5 min` ).
- `Topic Alias Maximum` é `10`.
- `Response Information` não é apoiado; `CONNACK` não devolve `Response Information` propriedade mesmo `CONNECT` que contenha `Request Response Information` propriedade.
- `Receive Maximum` (o número máximo de pacotes não `PUBLISH` reconhecidos autorizados (na direção do servidor do cliente) com `QoS: 1` ) é `16` .
- Um único cliente não pode ter mais do que `50` assinaturas.
  Quando o limite for atingido, `SUBACK` devolverá `0x97` o código de razão (Quota excedido) para as subscrições.

## <a name="connection-lifecycle"></a>Ciclo de vida de conexão

### <a name="connection"></a>Ligação

Para ligar um cliente ao IoT Hub utilizando esta API, estabeleça a ligação de acordo com a especificação MQTT 5.
O cliente deve enviar `CONNECT` o pacote dentro de 30 segundos após um aperto de mão TLS bem sucedido, ou o servidor fecha a ligação.
Aqui está um exemplo de `CONNECT` pacote:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` a propriedade é necessária e identifica qual o método de autenticação utilizado. Para obter mais informações sobre o método de autenticação, consulte [autenticação.](#authentication)
- `Authentication Data` o manuseamento de propriedades depende `Authentication Method` de . Se `Authentication Method` estiver definido para , `SAS` então é necessário e deve conter assinatura `Authentication Data` válida. Para obter mais informações sobre dados de autenticação, consulte [Autenticação.](#authentication)
- `api-version` a propriedade é necessária e deve ser definida como o valor da versão API fornecido no cabeçalho desta especificação para que esta especificação se aplique.
- `host` propriedade define o nome hospedeiro do inquilino. É necessário a menos que a extensão SNI tenha sido apresentada no registo da Client Hello durante o aperto de mão TLS
- `sas-at` define o tempo de ligação.
- `sas-expiry` define o tempo de validade para o SAS fornecido.
- `client-agent` opcionalmente comunica informações sobre o cliente que cria a ligação.

> [!NOTE]
> `Authentication Method` e outras propriedades em toda a especificação com nomes maiúsculas são propriedades de primeira classe em MQTT 5 - são descritas em detalhes na especificação MQTT 5. `api-version` e outras propriedades no caso dash são propriedades do utilizador específicas da IoT Hub API.

O IoT Hub responde com `CONNACK` pacote uma vez que termina com autenticação e recolha de dados para suportar a ligação. Se a ligação for estabelecida com sucesso, `CONNACK` parece:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Estas `CONNACK` propriedades do pacote seguem a [especificação MQTT 5.](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080) Refletem as capacidades do IoT Hub.

### <a name="authentication"></a>Autenticação

O `Authentication Method` imóvel no `CONNECT` cliente define que tipo de autenticação utiliza para esta ligação:

- `SAS` - Assinatura de Acesso Partilhado é fornecida na `CONNECT` `Authentication Data` propriedade de's,
- `X509` - o cliente baseia-se na autenticação do certificado do cliente.

 A autenticação falha se o método de autenticação não corresponder ao método configurado do cliente no IoT Hub.

> [!NOTE]
> Esta API requer que `Authentication Method` a propriedade seja definida em `CONNECT` pacote. Se `Authentication Method` a propriedade não for fornecida, a ligação falha com a `Bad Request` resposta.

A autenticação do nome de utilizador/palavra-passe utilizada em versões API anteriores não é suportada.

#### <a name="sas"></a>SAS

Com a autenticação baseada em SAS, o cliente deve fornecer a assinatura do contexto de conexão. Isto prova a autenticidade da ligação MQTT. A assinatura deve basear-se numa de duas chaves de autenticação na configuração do cliente no IoT Hub ou numa das duas chaves de acesso partilhadas de uma política de [acesso partilhado.](iot-hub-devguide-security.md)

A corda para assinar deve ser formada da seguinte forma:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` é derivado quer da extensão SNI (apresentado pelo cliente no registo Client Hello durante o aperto de mão TLS) ou `host` da propriedade do utilizador em `CONNECT` pacote.
- `Client Id` é Identificador de Cliente em `CONNECT` pacote.
- `sas-policy` - se estiver presente, define a política de acesso do Hub IoT utilizada para a autenticação. Está codificado como propriedade do utilizador no `CONNECT` pacote. Opcional: omiti-lo significa que as definições de autenticação no registo do dispositivo serão utilizadas.
- `sas-at` - se presente, especifica a hora de ligação - tempo atual. Está codificado como propriedade do utilizador do `time` tipo no `CONNECT` pacote.
- `sas-expiry` define o tempo de validade para a autenticação. É uma `time` propriedade de utilizador dactilografada no `CONNECT` pacote. Esta propriedade é necessária.

Para parâmetros opcionais, se omitidos, a corda vazia DEVE ser usada em cadeia para assinar.

O HMAC-SHA256 é utilizado para hash a corda com base numa das teclas simétricas do dispositivo. O valor do haxixe é então definido como valor da `Authentication Data` propriedade.

#### <a name="x509"></a>X509

Se `Authentication Method` a propriedade estiver definida para , O `X509` IoT Hub autentica a ligação com base no certificado de cliente fornecido.

#### <a name="reauthentication"></a>Reauthentication

Se for utilizada a autenticação baseada em SAS, recomendamos a utilização de fichas de autenticação de curta duração. Para manter a ligação autenticada e evitar a desconexão por causa da expiração, o cliente deve reautenticar enviando `AUTH` pacote com `Reason Code: 0x19` (reauthentication):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Regras:

- `Authentication Method` deve ser o mesmo que o usado para a autenticação inicial
- se a ligação foi originalmente autenticada utilizando o SAS com base na Política de Acesso Partilhado, a assinatura utilizada na reauthentication deve basear-se na mesma política.

Se a reauferição for bem sucedida, o IoT Hub envia `AUTH` pacote com `Reason Code: 0x00` (sucesso). Caso contrário, o IoT Hub envia `DISCONNECT` pacote com `Reason Code: 0x87` (Não autorizado) e fecha a ligação.

### <a name="disconnection"></a>Desconexão

O servidor pode desligar o cliente por algumas razões:

- o cliente está a portar-se mal de uma forma que é impossível responder com reconhecimento negativo (ou resposta) diretamente,
- o servidor não está a manter o estado da ligação atualizado,
- cliente com a mesma identidade tem conectado.

O servidor pode desligar-se com qualquer código de razão definido na especificação MQTT 5.0. Menções notáveis:

- `135` (Não autorizado) quando a reauthentication falha, o token SAS atual expira ou as credenciais do dispositivo mudam
- `142` (Sessão assumida) quando foi aberta nova ligação com a mesma identidade do cliente.
- `159` (Taxa de ligação excedida) quando a taxa de ligação para o hub IoT excede  
- `131` (Erro específico de implementação) é utilizado para quaisquer erros personalizados definidos nesta API. `status` e `reason` as propriedades serão usadas para comunicar mais detalhes sobre a causa da desconexão (ver [Resposta](#response) para mais detalhes).

## <a name="operations"></a>Operações

Todas as funcionalidades desta API são expressas como operações. Aqui está um exemplo da operação Send Telemetria:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Para obter uma especificação completa das operações nesta API, consulte [referência da API](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Todas as amostras desta especificação são mostradas do ponto de vista do cliente. Sinal `->` significa cliente enviando pacote, `<-` - receber.

### <a name="message-topics-and-subscriptions"></a>Tópicos de mensagens e subscrições

Os tópicos utilizados nas mensagens de operações nesta API começam por `$iothub/` .
A semântica de corretor MQTT não se aplica a estas operações (ver "[Tópicos começando \$ com ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" para mais detalhes).
Os tópicos que começam por `$iothub/` que não estão definidos nesta API não são suportados:

- Envio de mensagens para tópicos indefinidos resulta em `Not Found` resposta (ver [Resposta](#response) para detalhes abaixo),
- A subscrição de tópicos indefinidos resulta com `SUBACK` `Reason Code: 0x8F` (Filtro de Tópico Inválido).

Os nomes de tópicos e nomes de propriedade são sensíveis a casos e devem ser exatamente compatíveis. Por exemplo, `$iothub/telemetry/` não é apoiado enquanto `$iothub/telemetry` é.

> [!NOTE]
> Os wildcards em subscrições em baixo `$iothub/..` não são suportados. Ou seja, um cliente não pode subscrever `$iothub/+` `$iothub/#` ou. Tentar fazê-lo resulta `SUBACK` com `Reason Code: 0xA2` (Subscrições wildcard não suportadas). Apenas os wildcards de um único segmento `+` são suportados em vez de parâmetros de caminho em nome tópico para operações que os possuam.

### <a name="interaction-types"></a>Tipos de interação

Todas as operações nesta API baseiam-se num dos dois tipos de interação:

- Mensagem com reconhecimento opcional (MessageAck)
- Request-Response (ReqRep)

As operações também variam de acordo com a direção (determinada pela direção da mensagem inicial em troca):

- Cliente-a-Servidor (c2s)
- Servidor-a-Cliente (s2c)

Por exemplo, Enviar Telemetria é a operação cliente-servidor do tipo "Mensagem com reconhecimento", enquanto o Método De Gestão é a operação servidor-cliente de Request-Response tipo.

#### <a name="message-acknowledgement-interactions"></a>Interações de reconhecimento de mensagens

A mensagem com a interação de Reconhecimento Opcional (MessageAck) é expressa como uma troca de `PUBLISH` pacotes e `PUBACK` pacotes em MQTT. O reconhecimento é opcional e o remetente pode optar por não o solicitar enviando `PUBLISH` o pacote com `QoS: 0` .

> [!NOTE]
> Se as propriedades em `PUBACK` pacote embora tão devem ser truncadas devido à `Maximum Packet Size` declarada pelo cliente, o IoT Hub conservará o maior número de propriedades do Utilizador que puder caber dentro do limite indicado. As propriedades dos utilizadores listadas primeiro têm mais hipóteses de serem enviadas do que as listadas posteriormente; `Reason String` propriedade tem a menor prioridade.

##### <a name="example-of-simple-messageack-interaction"></a>Exemplo de interação simples messageAck

Mensagem:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Reconhecimento (sucesso):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interações Request-Response

Em Request-Response interações (ReqRep), tanto o Pedido como a Resposta traduzem-se em `PUBLISH` pacotes com `QoS: 0` .

`Correlation Data` propriedade deve ser definida em ambos e é usado para combinar pacote resposta ao pacote Request.

Esta API utiliza um tópico de resposta única `$iothub/responses` para todas as operações da ReqRep. A subscrição de /não subscrição deste tópico para operações cliente-servidor não é necessária - o servidor assume que todos os clientes devem ser subscritos.

##### <a name="example-of-simple-reqrep-interaction"></a>Exemplo de simples interação ReqRep

Pedido:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Resposta (sucesso):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

As interações reqRep não `PUBLISH` suportam pacotes `QoS: 1` como mensagens de pedido ou resposta. Envio de pedido `PUBLISH` resulta em `Bad Request` resposta.

O comprimento máximo suportado na `Correlation Data` propriedade é de 16 bytes. Se `Correlation Data` a propriedade no pacote for definida para um valor superior a `PUBLISH` 16 bytes, o IoT Hub envia `DISCONNECT` com `Bad Request` resultado, e fecha a ligação. Este comportamento aplica-se apenas aos pacotes trocados dentro desta API.

> [!NOTE]
> Os Dados de Correlação são uma sequência de byte arbitrária, por exemplo, não é garantida a cadeia UTF-8.
>
> ReqRep usar tópicos predefinidos para resposta; A propriedade Tópico de Resposta no `PUBLISH` pacote Request (se definida pelo remetente) é ignorada.

O IoT Hub subscreve automaticamente o cliente para tópicos de resposta para todas as operações do Cliente-servidor ReqRep. Mesmo que o cliente não subscreva explicitamente o tópico de resposta, o IoT Hub restabelece a subscrição automaticamente. Para as interações de servidor a cliente ReqRep, ainda é necessário que o dispositivo subscreva.

### <a name="message-properties"></a>Propriedades de mensagens

As propriedades de operação - sistema ou definido pelo utilizador - são expressas como propriedades de pacote em MQTT 5.

Os nomes de propriedade do utilizador são sensíveis a casos e devem ser escritos exatamente como definido. Por exemplo, `Trace-ID` não é apoiado enquanto `trace-id` é.

Pedidos com propriedades do Utilizador fora da especificação e sem prefixo `@` resultam em erro.

As propriedades do sistema são codificadas como propriedades de primeira classe (por exemplo, `Content Type` ) ou como propriedades do Utilizador. A especificação fornece uma lista exaustiva das propriedades suportadas do sistema.
Todas as propriedades de primeira classe são ignoradas, a menos que o suporte para elas esteja explicitamente indicado na especificação.

Sempre que as propriedades definidas pelo utilizador forem permitidas, os seus nomes devem seguir o formato `@{property name}` . As propriedades definidas pelo utilizador suportam apenas valores de cadeia UTF-8 válidos. por exemplo, `MyProperty1` a propriedade com valor deve ser `15` codificada como propriedade do Utilizador com nome `@MyProperty` e `15` valor.

Se o IoT Hub não reconhecer a propriedade do Utilizador, é considerado um erro e o IoT Hub responde `PUBACK` com `Reason Code: 0x83` (erro específico de implementação) e `status: 0100` (Mau Pedido). Se não for solicitado reconhecimento (QoS: 0), `DISCONNECT` o pacote com o mesmo erro será devolvido e a ligação é encerrada.

Esta API define os seguintes tipos de dados para além `string` de:

- `time`: número de milissegundos desde `1970-01-01T00:00:00.000Z` então . por exemplo, `1600987195320` `2020-09-24T22:39:55.320Z` para,
- `u32`: número inteiro não assinado de 32 bits,
- `u64`: número inteiro não assinado de 64 bits,
- `i32`: número inteiro assinado de 32 bits.

### <a name="response"></a>Resposta

As interações podem resultar em resultados diferentes: `Success` `Bad Request` , , e `Not Found` outros.
Os resultados distinguem-se uns dos outros pela `status` propriedade do utilizador. `Reason Code` em `PUBACK` pacotes (para interações messageAck) corresponde `status` em significado, sempre que possível.

> [!NOTE]
> Se o cliente especificar `Request Problem Information: 0` no pacote CONNECT, nenhuma propriedade do utilizador será enviada em `PUBACK` pacotes para cumprir com a especificação MQTT 5, incluindo `status` a propriedade. Neste caso, o cliente ainda pode confiar `Reason Code` para determinar se o reconhecimento é positivo ou negativo. 

Cada interação tem um padrão (ou sucesso). Tem `Reason Code` `0` e `status` propriedade de "não definido". Caso contrário:

- Para interações messageAck, `PUBACK` `Reason Code` obtém-se além de 0x0 (Sucesso). `status` propriedade pode estar presente para clarificar ainda mais o resultado.
- Para interações reqRep, Response `PUBLISH` obtém `status` conjunto de propriedade.
- Uma vez que não há forma de responder às interações do MessageAck `QoS: 0` diretamente, `DISCONNECT` o pacote é enviado com informações de resposta, seguido de desconexão.

Exemplos:

Mau pedido (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Não Autorizado (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Não Autorizado (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Quando necessário, o IoT Hub define as seguintes propriedades do utilizador:

- `status` - O código alargado do IoT Hub para o estado da operação. Este código pode ser usado para diferenciar os resultados.
- `trace-id` – vestígio de ID para a operação; O IoT Hub pode manter diagnósticos adicionais relativos à operação que possa ser usada para investigação interna.
- `reason` - mensagem legível pelo homem que forneça mais informações sobre o porquê da operação ter terminado num estado indicado por `status` propriedade.

> [!NOTE]
> Se o cliente definir `Maximum Packet Size` a propriedade no pacote CONNECT a um valor muito pequeno, nem todas as propriedades do utilizador podem caber e não aparecerão no pacote.
> 
> `reason` é destinado apenas para pessoas e não deve ser usado na lógica do cliente. Esta API permite que as mensagens sejam alteradas em qualquer ponto sem aviso ou alteração de versão.
>
> Se o cliente enviar `RequestProblemInformation: 0` o pacote CONNECT, as propriedades do utilizador não serão incluídas em agradecimentos por [especificação MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Código de estado

`status` propriedade carrega código de estado para funcionamento. Está otimizado para a eficiência de leitura de máquinas.
Consiste em um inteiro não assinado de dois bytes, codificado como hexáxinho em corda como `0501` .
Estrutura de código (mapa de bits):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

O primeiro byte é usado para bandeiras:

- os bits 0 e 1 indicam o tipo de resultados:
  - `00` - sucesso
  - `01` - erro do cliente
  - `10` - erro do servidor
- bit 2: `1` indica que o erro é retripável
- bits 3 a 7 são reservados e devem ser definidos para `0`

O segundo byte contém um código de resposta distinto. Os códigos de erro com diferentes bandeiras podem ter o mesmo segundo valor de byte. Por exemplo, pode haver `0001` , , , , `0101` `0201` `0301` códigos de erro com significado distinto.

Por exemplo, `Too Many Requests` é um cliente, erro retável com código próprio de `1` . O seu valor é `0000 0101 0000 0001` `0x0501` ou.

Os clientes podem usar bits de tipo para identificar se a operação foi concluída com sucesso. Os clientes também podem usar uma parte redando para decidir se é sensato voltar a tentar a operação.

## <a name="recommendations"></a>Recomendações

### <a name="session-management"></a>Gestão de sessões

`CONNACK` pacote carrega `Session Present` propriedade para indicar se servidor restaurado sessão previamente criada. Use esta propriedade para descobrir se subscreve tópicos ou não subscrever desde que a subscrição foi feita anteriormente.

Para `Session Present` confiar, o cliente deve acompanhar as subscrições que estão feitas (isto é, enviar `SUBSCRIBE` pacote e receber com código de `SUBACK` razão bem-sucedido), ou certifique-se de subscrever todos os tópicos numa única `SUBSCRIBE` / `SUBACK` troca. Caso contrário, se o cliente enviar dois `SUBSCRIBE` pacotes, e apenas um deles for processado com sucesso pelo servidor, o servidor comunicará `Session Present: 1` `CONNACK` enquanto apenas uma parte das subscrições do cliente é aceite.

Para evitar o caso em que uma versão mais antiga do cliente não subscreveu todos os tópicos, é melhor subscrever incondicionalmente quando o comportamento do cliente muda (por exemplo, como parte da atualização de firmware). Além disso, para garantir que não são deixadas subscrições velhas (retirando do número máximo permitido de subscrições), descreva explicitamente as subscrições que já não estão em uso.

### <a name="batching"></a>Lotes

Não há um formato especial para enviar um lote de mensagens. Para reduzir a sobrecarga de operações intensivas de recursos em TLS e networking, pacotes de pacotes `PUBLISH` (, `PUBACK` , , e `SUBSCRIBE` assim não) juntos antes de entregá-los à pilha TLS/TCP subjacente. Além disso, o cliente pode tornar o pseudónimo tópico mais fácil dentro do "lote":

- Coloque o nome do tópico completo no primeiro `PUBLISH` pacote para a ligação e associe-lhe o pseudónimo tópico,
- Coloque os seguintes pacotes para o mesmo tópico com nome de tópico vazio e propriedade de pseudónimos tópicos.

## <a name="migration"></a>Migração

Esta secção lista as alterações na API em comparação com a [API anterior da MQTT](iot-hub-mqtt-support.md).

- O protocolo de transporte é MQTT 5. Anteriormente - MQTT 3.1.1.
- As informações de contexto para a autenticação SAS estão contidas `CONNECT` no pacote diretamente em vez de serem codificadas juntamente com a assinatura.
- O método de autenticação é utilizado para indicar o método de autenticação utilizado.
- A Assinatura de Acesso Partilhado é colocada na propriedade Dados de Autenticação. Anteriormente, o campo palavra-passe foi utilizado.
- Os tópicos para operações são diferentes:
  - Telemetria: `$iothub/telemetry` em vez `devices/{Client Id}/messages/events` de,
  - Comandos: `$iothub/commands` em vez `devices/{Client Id}/messages/devicebound` de,
  - Patch Twin Relatado: `$iothub/twin/patch/reported` em vez `$iothub/twin/PATCH/properties/reported` de,
  - Notifique Twin Desired State Alterado: `$iothub/twin/patch/desired` em vez de `$iothub/twin/PATCH/properties/desired` .
- Não é necessária a subscrição para o tópico de resposta das operações de resposta ao pedido do cliente.
- As propriedades do utilizador são usadas em vez de codificar propriedades no segmento de nome tópico.
- os nomes dos imóveis são escritos em convenção de nomeação "dash-case" em vez de abreviaturas com prefixo especial. As propriedades definidas pelo utilizador agora requerem prefixo. Por exemplo, `$.mid` é agora `message-id` , enquanto se torna `myProperty1` `@myProperty1` .
- A propriedade Correlation Data é usada para correlacionar mensagens de pedido e resposta para operações de resposta a pedidos em vez de `$rid` propriedade codificada no tópico.
- `iothub-connection-auth-method` propriedade já não é carimbada em eventos de telemetria.
- Os comandos C2D não serão purgados na ausência de subscrição do dispositivo. Permanecerão na fila até que o dispositivo subscreva ou expirem.

## <a name="examples"></a>Exemplos

### <a name="send-telemetry"></a>Enviar telemetria

Mensagem:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Reconhecimento:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Reconhecimento alternativo (estrangulado):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Enviar get twin's state

Pedido:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Resposta (sucesso):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Resposta (não permitida):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Lidar com a chamada de método direto

Pedido:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Resposta (sucesso):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` não está definido - é uma resposta de sucesso.

Resposta indisponíveis do dispositivo:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Erro ao usar QoS 0, parte 1

Pedido:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Resposta:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Erro ao usar QoS 0, parte 2

Pedido:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Resposta:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Passos seguintes

- Para rever a referência API de pré-visualização MQTT 5, consulte o [plano de dados do IoT Hub MQTT 5 API referência](iot-hub-mqtt-5-reference.md).
- Para seguir uma amostra C#, consulte [o repositório de amostras gitHub](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).