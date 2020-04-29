---
title: Guia de protocolo de ligações híbridas Azure Relay [ Guia de protocolos de ligações híbridas de retransmissão azul ] Microsoft Docs
description: Este artigo descreve as interações do lado do cliente com o relé De Conexões Híbridas para ligar clientes em funções de ouvinte e remetente.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: clemensv
ms.openlocfilehash: 68668452152064584d1c419a3053ccb642b103f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514957"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexões híbridas Azure Relay

O Azure Relay é um dos pilares de capacidade chave da plataforma Azure Service Bus. A nova capacidade de _Conexões Híbridas_ da Relay é uma evolução segura e de protocolo aberto baseada em HTTP e WebSockets. Substitui a primeira funcionalidade _bizTalk Services,_ igualmente nomeada, que foi construída sobre uma fundação de protocolo proprietário. A integração das Conexões Híbridas nos Serviços de Aplicações Azure continuará a funcionar como está.

As Ligações Híbridas permitem uma comunicação bidirecional, de fluxo binário e um simples fluxo de datagram entre duas aplicações em rede. Qualquer uma das partes pode residir atrás de NATs ou firewalls.

Este artigo descreve as interações do lado do cliente com o relé De Conexões Híbridas para ligar clientes em funções de ouvinte e remetente. Também descreve como os ouvintes aceitam novas ligações e pedidos.

## <a name="interaction-model"></a>Modelo de interação

O relé De Conexões Híbridas liga duas partes fornecendo um ponto de encontro na nuvem Azure que as partes podem descobrir e ligar a partir da perspetiva da sua própria rede. O ponto de encontro chama-se "Conexão Híbrida" nesta e noutra documentação, nas APIs, e também no portal Azure. O ponto final do serviço De conexões híbridas é referido como o "serviço" para o resto deste artigo.

O serviço permite a transmissão de ligações web socket e pedidos e respostas HTTP(S).

O modelo de interação apoia-se na nomenclatura estabelecida por muitas outras APIs em rede. Há um ouvinte que primeiro indica disponibilidade para lidar com as ligações de entrada, e posteriormente aceita-as à medida que chegam. Por outro lado, um cliente liga-se ao ouvinte, esperando que essa ligação seja aceite para estabelecer uma via de comunicação bidirecional. "Connect", "Listen" e "Accept" são os mesmos termos que se encontram na maioria das APIs da tomada.

Qualquer modelo de comunicação retransmitido tem qualquer uma das partes a fazer ligações de saída para um ponto final de serviço. Isto faz do "ouvinte" também um "cliente" no uso coloquial, e também pode causar outras sobrecargas de terminologia. A terminologia precisa, portanto, utilizada para ligações híbridas é a seguinte:

Os programas de ambos os lados de uma ligação são chamados de "clientes", uma vez que são clientes do serviço. O cliente que espera e aceita ligações é o "ouvinte", ou diz-se que está no "papel de ouvinte". O cliente que inicia uma nova ligação para um ouvinte através do serviço é chamado de "remetente", ou está na "função remetente".

### <a name="listener-interactions"></a>Interações de ouvintes

O ouvinte tem cinco interações com o serviço; todos os detalhes do fio são descritos mais tarde neste artigo na secção de referência.

As mensagens Ouvir, Aceitar e Solicitar são recebidas do serviço. As operações de Renovação e Ping são enviadas pelo ouvinte.

#### <a name="listen-message"></a>Ouvir mensagem

Para indicar prontidão ao serviço de que um ouvinte está pronto para aceitar ligações, cria uma ligação WebSocket de saída. O aperto de mão de ligação tem o nome de uma Conexão Híbrida configurada no espaço de nome retransmissor, e um símbolo de segurança que confere o "Ouvir" direito nesse nome.

Quando o WebSocket é aceite pelo serviço, o registo é completo e o WebSocket estabelecido é mantido vivo como o "canal de controlo" para permitir todas as interações subsequentes. O serviço permite até 25 ouvintes simultâneos para uma Ligação Híbrida. A quota para AppHooks deve ser determinada.

Para ligações híbridas, se houver dois ou mais ouvintes ativos, as ligações de entrada são equilibradas em todas elas por ordem aleatória; a distribuição justa é tentada com o melhor esforço.

#### <a name="accept-message"></a>Aceitar mensagem

Quando um remetente abre uma nova ligação no serviço, o serviço escolhe e notifica um dos ouvintes ativos na Ligação Híbrida. Esta notificação é enviada ao ouvinte sobre o canal de controlo aberto como uma mensagem JSON. A mensagem contém o URL do ponto final do WebSocket a que o ouvinte deve ligar-se para aceitar a ligação.

O URL pode e deve ser utilizado diretamente pelo ouvinte sem qualquer trabalho extra.
As informações codificadas só são válidas por um curto período de tempo, essencialmente enquanto o remetente estiver disposto a esperar que a ligação seja estabelecida de ponta a ponta. O máximo a assumir é de 30 segundos. O URL só pode ser utilizado para uma tentativa de ligação bem sucedida. Assim que a ligação WebSocket com o URL de encontro for estabelecida, toda a atividade adicional neste WebSocket é transmitida de e para o remetente. Isto acontece sem qualquer intervenção ou interpretação por parte do serviço.

### <a name="request-message"></a>Mensagem de pedido

Além das ligações WebSocket, o ouvinte também pode receber quadros de pedido HTTP de um remetente, se esta capacidade estiver explicitamente ativada na Ligação Híbrida.

Os ouvintes que se ligam às `request` Conexões Híbridas com suporte HTTP DEVEM lidar com o gesto. Um ouvinte que não `request` lida com o tratamento e, portanto, causa erros de tempo repetidos enquanto está ligado pode ser listado na lista negra pelo serviço no futuro.

Os metadados do cabeçalho do quadro HTTP são traduzidos para JSON para um manuseamento mais simples pela estrutura do ouvinte, também porque as bibliotecas de análise de cabeçalho http são mais raras do que os parsers JSON. Os metadados HTTP que só são relevantes para a relação entre o remetente e o gateway REL HTTP, incluindo informações de autorização, não são reencaminhados. Os organismos de pedido HTTP são transferidos de forma transparente como quadros binários do WebSocket.

O ouvinte pode responder aos pedidos de HTTP usando um gesto de resposta equivalente.

O fluxo de pedido/resposta utiliza o canal de controlo por defeito, mas pode ser "atualizado" para um webSocket distinto sempre que necessário. As ligações WebSocket distintas melhoram a produção para cada conversa de cliente, mas sobrecarregam o ouvinte com mais ligações que precisam de ser manuseadas, o que pode não ser desejado para clientes leves.

No canal de controlo, os organismos de pedido e resposta limitam-se a, no máximo, 64 kB de tamanho. Os metadados do cabeçalho HTTP estão limitados a um total de 32 kB. Se o pedido ou a resposta excederesse limiar, o ouvinte DEVE atualizar para um WebSocket de encontro utilizando um gesto equivalente ao manuseamento do [Accept](#accept-message).

Para pedidos, o serviço decide se encaminha os pedidos para o canal de controlo. Isto inclui, mas não pode limitar-se a casos em que um pedido exceda 64 kB (cabeçalhos mais corpo) ou se o pedido for enviado com [codificação de transferência "chunked"](https://tools.ietf.org/html/rfc7230#section-4.1) e o serviço tiver razões para esperar que o pedido exceda 64kB ou ler o pedido não é instantâneo. Se o serviço optar por entregar o pedido em vez de um encontro, apenas passa o endereço de encontro ao ouvinte.
O ouvinte deve então estabelecer o WebSocket de encontro e o serviço entrega prontamente o pedido completo, incluindo corpos sobre o webSocket de encontro. A resposta DEVE também utilizar o WebSocket de encontro.

Para pedidos que chegam ao canal de controlo, o ouvinte decide se responde através do canal de controlo ou através do encontro. O serviço DEVE incluir um endereço de encontro com todos os pedidos encaminhados para o canal de controlo. Este endereço só é válido para a atualização do pedido atual.

Se o ouvinte optar por fazer a atualização, liga-se e entrega prontamente a resposta sobre a tomada de encontro estabelecida.

Uma vez estabelecido o webSocket de encontro, o ouvinte deve mantê-lo para posterior tratamento de pedidos e respostas do mesmo cliente. O serviço manterá o WebSocket enquanto a ligação da tomada HTTPS com o remetente persistir e irá encaminhar todos os pedidos subsequentes desse remetente para a WebSocket mantida. Se o ouvinte optar por retirar o WebSocket do seu lado, o serviço também deixará cair a ligação ao remetente, independentemente de um pedido subsequente já estar em curso.

#### <a name="renew-operation"></a>Renovar operação

O sinal de segurança que deve ser utilizado para registar o ouvinte e manter o canal de controlo pode expirar enquanto o ouvinte estiver ativo. A caducidade do símbolo não afeta as ligações em curso, mas faz com que o canal de controlo seja abandonado pelo serviço em ou logo após o momento de expiração. A operação "renovar" é uma mensagem JSON que o ouvinte pode enviar para substituir o símbolo associado ao canal de controlo, de modo a que o canal de controlo possa ser mantido por longos períodos.

#### <a name="ping-operation"></a>Operação ping

Se o canal de controlo permanecer inativo durante muito tempo, os intermediários a caminho, tais como os equilibradores de carga ou os NATs, podem deixar cair a ligação TCP. A operação "ping" evita que, enviando uma pequena quantidade de dados no canal, que lembre a todos na rota da rede que a ligação deve estar viva, e também serve como um teste "ao vivo" para o ouvinte. Se o ping falhar, o canal de controlo deve ser considerado inutilizável e o ouvinte deve voltar a ligar-se.

### <a name="sender-interaction"></a>Interação com o remetente

O remetente tem duas interações com o serviço: liga uma Web Socket ou envia pedidos via HTTPS. Os pedidos não podem ser enviados por uma Tomada Web a partir da função de remetente.

#### <a name="connect-operation"></a>Operação de ligação

A operação "connect" abre um WebSocket no serviço, fornecendo o nome da Conexão Híbrida e (opcionalmente, mas exigido por padrão) um símbolo de segurança que confere permissão de "Enviar" na corda de consulta. O serviço interage então com o ouvinte da forma descrita anteriormente, e o ouvinte cria uma ligação de encontro que se junta a este WebSocket. Depois de o WebSocket ter sido aceite, todas as interações adicionais sobre o WebSocket estão com um ouvinte conectado.

#### <a name="request-operation"></a>Operação de pedido

Para ligações híbridas para as quais a funcionalidade foi ativada, o remetente pode enviar pedidos http sem restrições aos ouvintes.

Com exceção de um sinal de acesso retransmissor que esteja incorporado na corda de consulta ou num cabeçalho HTTP do pedido, o Retransmissor é totalmente transparente para todas as operações http no endereço Retransmissor e todos os sufixos da rota do endereço Relé, deixando o ouvinte totalmente no controlo da autorização de ponta a ponta e até mesmo funcionalidades de extensão HTTP como [CORS](https://www.w3.org/TR/cors/).

A autorização do remetente com o ponto final do retransmissor é ligada por defeito, mas é OPCIONAL. O proprietário da Ligação Híbrida pode optar por permitir remetentes anónimos. O serviço intercetará, inspecionará e retirará as informações de autorização da seguinte forma:

1. Se a corda de `sb-hc-token` consulta contiver uma expressão, a expressão será sempre retirada da corda de consulta. Será avaliado se a autorização de retransmissão estiver ligada.
2. Se os cabeçalhos `ServiceBusAuthorization` de pedido contiverem um cabeçalho, a expressão do cabeçalho será sempre retirada da coleção do cabeçalho.
   Será avaliado se a autorização de retransmissão estiver ligada.
3. Só se a autorização de retransmissão for `Authorization` ligada, e se os cabeçalhos de pedido contiverem um cabeçalho, e nenhuma das expressões anteriores estiver presente, o cabeçalho será avaliado e despido. Caso contrário, `Authorization`o é sempre transmitido como está.

Se não houver ouvinte ativo, o serviço devolverá um código de erro 502 "Bad Gateway". Se o serviço não parecer tratar do pedido, o serviço devolverá um "Timeout Gateway" 504 após 60 segundos.

### <a name="interaction-summary"></a>Resumo da interação

O resultado deste modelo de interação é que o cliente remetente sai do aperto de mão com um WebSocket "limpo", que está ligado a um ouvinte e que não precisa de mais preâmbulos ou preparação. Este modelo permite que praticamente qualquer implementação de cliente WebSocket existente aproveite facilmente o serviço de Conexões Híbridas fornecendo um URL corretamente construído na sua camada de cliente WebSocket.

A ligação de pontos de encontro WebSocket que o ouvinte obtém através da interação aceita também é limpa e pode ser entregue a qualquer implementação do servidor WebSocket existente com alguma abstração extra mínima que distingue entre operações de "aceitar" nos ouvintes locais da sua estrutura e operações remotas de "aceitação" de Ligações Híbridas.

O modelo http request/response dá ao remetente uma área de superfície de protocolo HTTP em grande parte sem restrições com uma camada de autorização OPCIONAL. O ouvinte obtém uma secção de cabeçalho de pedido HTTP pré-analisada que pode ser transformada num pedido HTTP a jusante ou manuseada como está, com molduras binárias que transportam corpos HTTP. As respostas usam o mesmo formato. As interações com menos de 64 KB de corpo de pedido e resposta podem ser manuseadas sobre uma única Tomada Web que é partilhada para todos os remetentes. Pedidos e respostas maiores podem ser tratados utilizando o modelo de encontro.

## <a name="protocol-reference"></a>Referência protocole

Esta secção descreve os detalhes das interações protocolares descritas anteriormente.

Todas as ligações WebSocket são feitas na porta 443 como uma atualização de HTTPS 1.1, que é comumente abstrata por alguma estrutura WebSocket ou API. A descrição aqui é mantida neutra em termos de implementação, sem sugerir um quadro específico.

### <a name="listener-protocol"></a>Protocolo de escuta

O protocolo do ouvinte consiste em dois gestos de ligação e três operações de mensagens.

#### <a name="listener-control-channel-connection"></a>Ligação do canal de controlo do ouvinte

O canal de controlo é aberto com a criação de uma ligação WebSocket para:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

O `namespace-address` nome de domínio totalmente qualificado do espaço de nome Sulista Azure que acolhe a Conexão Híbrida, tipicamente do formulário. `{myname}.servicebus.windows.net`

As opções de parâmetros de corda de consulta são as seguintes.

| Parâmetro        | Necessário | Descrição
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sim      | Para o papel de ouvinte, o parâmetro deve ser **sb-hc-action=ouvir**
| `{path}`         | Sim      | O caminho espaço de nome codificado por URL da Ligação Híbrida pré-configurada para registar este ouvinte ligado. Esta expressão está anexada `$hc/` à parte do caminho fixo.
| `sb-hc-token`    | Sim\*    | O ouvinte deve fornecer um suporte de acesso partilhado de ônibus de serviço codificado com URL para o espaço de nome ou conexão híbrida que confere o direito de **ouvir.**
| `sb-hc-id`       | Não       | Este ID opcional fornecido pelo cliente permite o rastreio de diagnóstico de ponta a ponta.

Se a ligação WebSocket falhar devido ao facto de a rota de ligação híbrida não estar registada, ou de um token inválido ou em falta, ou de algum outro erro, o feedback de erro é fornecido utilizando o modelo regular de feedback do estado HTTP 1.1. A descrição do estado contém um erro de rastreio que pode ser comunicado ao pessoal de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado   | O sinal de segurança está em falta, mal formado ou inválido.
| 403  | Proibido      | O símbolo de segurança não é válido para este caminho para esta ação.
| 500  | Erro Interno | Algo correu mal no serviço.

Se a ligação WebSocket for desligada intencionalmente pelo serviço após a sua configuração inicial, a razão para o fazer é comunicada utilizando um código de erro de protocolo WebSocket apropriado, juntamente com uma mensagem de erro descritiva que também inclui um ID de rastreio. O serviço não desligará o canal de controlo sem encontrar uma condição de erro. Qualquer encerramento limpo é controlado pelo cliente.

| Estatuto WS | Descrição
| --------- | -------------------------------------------------------------------------------
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O sinal de segurança expirou, portanto a política de autorização é violada.
| 1011      | Algo correu mal no serviço.

#### <a name="accept-handshake"></a>Aceite o aperto de mão

A notificação "aceitar" é enviada pelo serviço ao ouvinte através do canal de controlo previamente estabelecido como uma mensagem JSON num quadro de texto WebSocket. Não há resposta a esta mensagem.

A mensagem contém um objeto JSON chamado "aceitar", que define as seguintes propriedades neste momento:

* **endereço** – a cadeia URL a utilizar para estabelecer o WebSocket ao serviço para aceitar uma ligação de entrada.
* **id** – o identificador único para esta ligação. Se o ID foi fornecido pelo cliente remetente, é o valor fornecido pelo remetente, caso contrário é um valor gerado pelo sistema.
* **ligarHeaders** – todos os cabeçalhos HTTP que foram fornecidos ao ponto final do retransmissor pelo remetente, que também inclui o Sec-WebSocket-Protocol e os cabeçalhos sec-WebSocket-Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

O URL de endereço fornecido na mensagem JSON é utilizado pelo ouvinte para estabelecer o WebSocket para aceitar ou rejeitar a tomada do remetente.

##### <a name="accepting-the-socket"></a>Aceitando a tomada

Para aceitar, o ouvinte estabelece uma ligação WebSocket ao endereço fornecido.

Se a mensagem "aceitar" transportar um `Sec-WebSocket-Protocol` cabeçalho, espera-se que o ouvinte só aceite o WebSocket se suportar esse protocolo. Além disso, define o cabeçalho à medida que o WebSocket é estabelecido.

O mesmo se `Sec-WebSocket-Extensions` aplica ao cabeçalho. Se a estrutura suportar uma extensão, deve definir o cabeçalho `Sec-WebSocket-Extensions` para a resposta do lado do servidor do aperto de mão necessário para a extensão.

O URL deve ser utilizado como é para estabelecer a tomada aceite, mas contém os seguintes parâmetros:

| Parâmetro      | Necessário | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar uma tomada, o parâmetro deve ser`sb-hc-action=accept`
| `{path}`       | Sim      | (ver o parágrafo seguinte)
| `sb-hc-id`     | Não       | Consulte a descrição anterior do **id**.

`{path}`é o caminho espaço de espaço de nome codificado por URL da Ligação Híbrida pré-configurada para registar este ouvinte. Esta expressão está anexada `$hc/` à parte do caminho fixo.

A `path` expressão pode ser estendida com um sufixo e uma expressão de corda de consulta que segue o nome registado após um corte para a frente que separa.
Isto permite ao cliente remetente passar argumentos de envio para o ouvinte que aceita quando não é possível incluir cabeçalhos HTTP. A expectativa é que o quadro do ouvinte analise a parte do caminho fixo e o nome registado do `sb-`caminho e torne o restante, possivelmente sem argumentos de corda de consulta pré-fixados, disponíveis para o pedido de decisão para decidir se aceitam a ligação.

Para mais informações, consulte a secção "Protocolo de Remetente".

Se houver um erro, o serviço pode responder da seguinte forma:

| Código | Erro          | Descrição
| ---- | -------------- | -----------------------------------
| 403  | Proibido      | O URL não é válido.
| 500  | Erro Interno | Algo correu mal no serviço.

 Depois de estabelecida a ligação, o servidor desliga o WebSocket quando o remetente WebSocket desliga, ou com o seguinte estado:

| Estatuto WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a ligação.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O sinal de segurança expirou, portanto a política de autorização é violada. |
| 1011      | Algo correu mal no serviço.                                            |

##### <a name="rejecting-the-socket"></a>Rejeitando a tomada

 Rejeitar a tomada após `accept` inspecionar a mensagem requer um aperto de mão semelhante para que o código de estado e a descrição do estado que comunicam a razão da rejeição possam voltar para o remetente.

 A escolha do design do protocolo aqui é usar um aperto de mão WebSocket (que é projetado para terminar em um estado de erro definido) para que as implementações de clientes ouvintes possam continuar a depender de um cliente WebSocket e não precisam de empregar um cliente http extra, nu.

 Para rejeitar a tomada, o cliente `accept` retira o endereço URI da mensagem e anexa dois parâmetros de corda de consulta, da seguinte forma:

| Param                   | Necessário | Descrição                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCódigo        | Sim      | Código de estado numérico HTTP.                |
| sb-hc-statusDescrição | Sim      | Razão legível humana para a rejeição. |

O URI resultante é então utilizado para estabelecer uma ligação WebSocket.

Ao completar corretamente, este aperto de mão falha intencionalmente com um código de erro HTTP 410, uma vez que não foi estabelecido nenhum WebSocket. Se algo correr mal, os seguintes códigos descrevem o erro:

| Código | Erro          | Descrição                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Proibido      | O URL não é válido.                |
| 500  | Erro Interno | Algo correu mal no serviço. |

#### <a name="request-message"></a>Mensagem de pedido

A `request` mensagem é enviada pelo serviço ao ouvinte através do canal de controlo. A mesma mensagem também é enviada sobre o webSocket de encontro uma vez estabelecido.

As `request` duas partes são constituídas por duas partes: um cabeçalho e uma moldura corporal binária.
Se não houver corpo, as molduras do corpo são omitidas. O indicador para se um corpo está `body` presente é a propriedade booleana na mensagem de pedido.

Para um pedido com um órgão de pedido, a estrutura pode ser assim:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

O cabo do ouvinte MUST recebendo o corpo de pedido dividido em vários quadros binários (ver [fragmentos webSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
O pedido termina quando foi recebida uma moldura binária com o conjunto de bandeira fin.

Para um pedido sem corpo, só há uma moldura de texto.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

O conteúdo da `request` JSON é o seguinte:

* **endereço** - corda URI. Este é o endereço de encontro a utilizar para este pedido. Se o pedido de entrada for superior a 64 kB, o restante desta mensagem fica vazio e `accept` o cliente DEVE iniciar um aperto de mão equivalente à operação descrita abaixo. Em seguida, o `request` serviço colocará o completo na tomada Web estabelecida. Se se pode esperar que a resposta ultrapasse os 64 kB, o ouvinte DEVE também iniciar um aperto de mão de encontro e, em seguida, transferir a resposta sobre a tomada Web estabelecida.
* **id** – corda. O identificador único para este pedido.
* **solicitadoresHeaders** – este objeto contém todos os cabeçalhos HTTP que foram fornecidos ao ponto final pelo remetente, com exceção das informações de autorização [acima](#request-operation)explicadas – e cabeçalhos que se relacionam estritamente com a ligação com o portal. Especificamente, TODOS os cabeçalhos definidos ou `Via`reservados em [RFC7230,](https://tools.ietf.org/html/rfc7230)exceto, são despojados e não reencaminhados:

  * `Connection`(RFC7230, Secção 6.1)
  * `Content-Length`(RFC7230, Secção 3.3.2)
  * `Host`(RFC7230, Secção 5.4)
  * `TE`(RFC7230, Secção 4.3)
  * `Trailer`(RFC7230, Secção 4.4)
  * `Transfer-Encoding`(RFC7230, Secção 3.3.1)
  * `Upgrade`(RFC7230, Secção 6.7)
  * `Close`(RFC7230, Secção 8.1)

* **solicitarTarget** – corda. Este imóvel detém o "Alvo de [Pedido" (RFC7230, Secção 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) do pedido. Isto inclui a porção de corda de `sb-hc-` consulta, que é despojada de todos os parâmetros pré-fixados.
* **método** - corda. Este é o método do pedido, por [RFC7231, Secção 4](https://tools.ietf.org/html/rfc7231#section-4). O `CONNECT` método NÃO DEVE ser utilizado.
* **corpo** – boolean. Indica se segue uma ou mais molduras binárias do corpo.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Responder a pedidos

O recetor DEVE responder. A incapacidade repetida de responder aos pedidos mantendo a ligação pode resultar na colocação da lista negra do ouvinte.

As respostas podem ser enviadas em qualquer encomenda, mas cada pedido deve ser respondido dentro de 60 segundos ou a entrega será reportada como tendo falhado. O prazo de 60 segundos `response` é contado até que o quadro tenha sido recebido pelo serviço. Uma resposta contínua com várias molduras binárias não pode ficar inativa por mais de 60 segundos ou termina.

Se o pedido for recebido através do canal de controlo, a resposta DEVE ser enviada no canal de controlo de onde o pedido foi recebido ou deve ser enviada através de um canal de encontro.

A resposta é um objeto da JSON chamado "resposta". As regras para o manuseamento `request` do conteúdo do `body` corpo são exatamente como com a mensagem e baseadas na propriedade.

* **requestId** – corda. Necessário. O `id` valor patrimonial `request` da mensagem a ser respondida.
* **statusCódigo** – número. Necessário. um código de estado http numérico que indique o resultado da notificação. Todos os códigos de estado do [RFC7231, secção 6,](https://tools.ietf.org/html/rfc7231#section-6) exceto [502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e [504 "Gateway Timeout".](https://tools.ietf.org/html/rfc7231#section-6.6.5)
* **estadoDescrição** - corda. OPCIONAL. FRASE de razão de código de estado HTTP por [RFC7230, Secção 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **respostaHeaders** – Cabeçalhos HTTP a definir numa resposta http externa.
  Tal como `request`acontece com os cabeçalhos definidos RFC7230 NÃO DEVEM ser utilizados.
* **corpo** – boolean. Indica se os quadros do corpo binário seguem(s).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Respondendo via encontro

Para respostas superiores a 64 kB, a resposta DEVE ser entregue sobre uma tomada de ponto de encontro. Além disso, se o pedido exceder `request` 64 kB, e o único contiver o `request`campo de endereços, deve ser estabelecida uma tomada de encontro para obter o . Uma vez estabelecida uma tomada de encontro, as respostas ao respetivo cliente e os pedidos subsequentes desse respetivo cliente DEVEM ser entregues sobre a tomada de encontro enquanto persiste.

O `address` URL `request` no deve ser utilizado como é para estabelecer a tomada de encontro, mas contém os seguintes parâmetros:

| Parâmetro      | Necessário | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar uma tomada, o parâmetro deve ser`sb-hc-action=request`

Se houver um erro, o serviço pode responder da seguinte forma:

| Código | Erro           | Descrição
| ---- | --------------- | -----------------------------------
| 400  | Pedido Inválido | Ação não reconhecida ou URL não válido.
| 403  | Proibido       | A URL expirou.
| 500  | Erro Interno  | Algo correu mal no serviço.

 Após a ligação ter sido estabelecida, o servidor desliga o WebSocket quando a tomada HTTP do cliente se desliga, ou com o seguinte estado:

| Estatuto WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a ligação.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O sinal de segurança expirou, portanto a política de autorização é violada. |
| 1011      | Algo correu mal no serviço.                                            |


#### <a name="listener-token-renewal"></a>Renovação de token de ouvinte

Quando o sinal de escuta estiver prestes a expirar, pode substituí-lo enviando uma mensagem de texto para o serviço através do canal de controlo estabelecido. A mensagem contém um objeto `renewToken`JSON chamado , que define a seguinte propriedade neste momento:

* **ficha** – um suporte de acesso partilhado de ônibus de serviço codificado por URL para o espaço de nome ou ligação híbrida que confere o direito **de ouvir.**

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se a validação do token falhar, o acesso é negado e o serviço de nuvem fecha o canal de controlo WebSocket com um erro. Caso contrário, não há resposta.

| Estatuto WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | O sinal de segurança expirou, portanto a política de autorização é violada. |

### <a name="web-socket-connect-protocol"></a>Protocolo de ligação web socket

O protocolo do remetente é efetivamente idêntico à forma como um ouvinte é estabelecido.
O objetivo é a máxima transparência para o WebSocket de ponta a ponta. O endereço a que se ligar é o mesmo que para o ouvinte, mas a "ação" difere e o símbolo precisa de uma permissão diferente:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

O _nome space-address_ é o nome de domínio totalmente qualificado do espaço de nome `{myname}.servicebus.windows.net`Sulista Azure que acolhe a Conexão Híbrida, tipicamente do formulário .

O pedido pode conter cabeçalhos http extra arbitrários extra, incluindo os definidos pela aplicação. Todos os cabeçalhos fornecidos fluem para `connectHeader` o ouvinte e podem ser encontrados no objeto da mensagem de controlo **de aceitação.**

As opções de parâmetros de corda de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sim       | Para o papel do remetente, `sb-hc-action=connect`o parâmetro deve ser .
| `{path}`       | Sim       | (ver o parágrafo seguinte)
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um suporte de acesso partilhado de ônibus de serviço codificado com URL para o espaço de nome ou conexão híbrida que confere o direito **de envio.**
| `sb-hc-id`     | Não        | Um ID opcional que permite o rastreio de diagnóstico de ponta a ponta e é disponibilizado ao ouvinte durante o aperto de mão aceite.

 É `{path}` o caminho espaço de espaço de nome codificado por URL da Ligação Híbrida pré-configurada para registar este ouvinte. A `path` expressão pode ser estendida com um sufixo e uma expressão de corda de consulta para comunicar mais. Se a Ligação Híbrida `hyco`estiver `path` registada no `hyco/suffix?param=value&...` caminho, a expressão pode ser seguida pelos parâmetros de corda de consulta definidos aqui. Uma expressão completa pode então ser a seguinte:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A `path` expressão é transmitida ao ouvinte no endereço URI contido na mensagem de controlo "aceitar".

Se a ligação WebSocket falhar devido ao facto de a via de ligação híbrida não estar registada, um token inválido ou em falta, ou algum outro erro, o feedback de erro é fornecido utilizando o modelo regular de feedback do estado HTTP 1.1. A descrição do estado contém um ID de rastreio de erros que pode ser comunicado ao pessoal de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado   | O sinal de segurança está em falta, mal formado ou inválido.
| 403  | Proibido      | O símbolo de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno | Algo correu mal no serviço.

Se a ligação WebSocket for desligada intencionalmente pelo serviço depois de ter sido inicialmente configurada, a razão para o fazer é comunicada utilizando um código de erro de protocolo WebSocket apropriado, juntamente com uma mensagem de erro descritiva que também inclui um ID de rastreio.

| Estatuto WS | Descrição
| --------- | ------------------------------------------------------------------------------- 
| 1000      | O ouvinte desligou a tomada.
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O sinal de segurança expirou, portanto a política de autorização é violada.
| 1011      | Algo correu mal no serviço.

### <a name="http-request-protocol"></a>PROTOCOLO DE PEDIDO HTTP

O protocolo de pedido http permite pedidos de HTTP arbitrários, exceto atualizações protocolares.
Os pedidos HTTP são apontados para o endereço de tempo de execução regular da entidade, sem o $hc infixo que é usado para ligações híbridas Clientes WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

O _nome space-address_ é o nome de domínio totalmente qualificado do espaço de nome `{myname}.servicebus.windows.net`Sulista Azure que acolhe a Conexão Híbrida, tipicamente do formulário .

O pedido pode conter cabeçalhos http extra arbitrários extra, incluindo os definidos pela aplicação. Todos os cabeçalhos fornecidos, exceto os diretamente definidos no RFC7230 (ver `requestHeader` mensagem de [pedido](#Request message)) fluem para o ouvinte e podem ser encontrados no objeto da mensagem de **pedido.**

As opções de parâmetros de corda de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um suporte de acesso partilhado de ônibus de serviço codificado com URL para o espaço de nome ou conexão híbrida que confere o direito **de envio.**

O símbolo também pode ser `ServiceBusAuthorization` transportado `Authorization` no cabeçalho ou http. O símbolo pode ser omitido se a Ligação Híbrida estiver configurada para permitir pedidos anónimos.

Uma vez que o serviço atua efetivamente como um representante, mesmo `Via` que não como um `Via` verdadeiro representante http, ou adiciona um cabeçalho ou anota o cabeçalho existente em conformidade com [o RFC7230, secção 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
O serviço adiciona o nome `Via`de anfitrião do espaço de retransmissão a .

| Código | Mensagem  | Descrição                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | O pedido foi tratado por pelo menos um ouvinte.  |
| 202  | Aceite | O pedido foi aceite por pelo menos um ouvinte. |

Se houver um erro, o serviço pode responder da seguinte forma. Se a resposta tem origem no serviço ou no ouvinte pode `Via` ser identificada através da presença do cabeçalho. Se o cabeçalho estiver presente, a resposta é do ouvinte.

| Código | Erro           | Descrição
| ---- | --------------- |--------- |
| 404  | Não encontrado       | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado    | O sinal de segurança está em falta, mal formado ou inválido.
| 403  | Proibido       | O símbolo de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno  | Algo correu mal no serviço.
| 503  | Bad Gateway     | O pedido não podia ser encaminhado para nenhum ouvinte.
| 504  | Tempo de passagem | O pedido foi encaminhado para um ouvinte, mas o ouvinte não reconheceu a receção no tempo necessário.

## <a name="next-steps"></a>Passos seguintes

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Começar com .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)
