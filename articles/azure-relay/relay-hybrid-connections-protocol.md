---
title: Guia de protocolo de conexões híbridas Azure Relay / Microsoft Docs
description: Este artigo descreve as interações do lado do cliente com o relé Conexões Híbridas para ligar clientes em funções de ouvinte e remetente.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8a812aa401077b81934d89ada99cf1dc312d8dbc
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862331"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexões híbridas Azure Relay

A Azure Relay é um dos pilares-chave da capacidade da plataforma Azure Service Bus. A nova capacidade de _Conexões Híbridas_ do Relay é uma evolução segura e aberta baseada em HTTP e WebSockets. Substitui a primeira, igualmente chamada _BizTalk Services,_ que foi construída sobre uma fundação de protocolo proprietário. A integração das Ligações Híbridas nos Serviços de Aplicações Azure continuará a funcionar como está.

As Ligações Híbridas permitem a comunicação bidirecional, binária de fluxo e o fluxo de datagrama simples entre duas aplicações em rede. Ou ambas as partes podem residir atrás de NATs ou firewalls.

Este artigo descreve as interações do lado do cliente com o relé Conexões Híbridas para ligar clientes em funções de ouvinte e remetente. Também descreve como os ouvintes aceitam novas ligações e pedidos.

## <a name="interaction-model"></a>Modelo de interação

O relé Hybrid Connections conecta duas partes fornecendo um ponto de encontro na nuvem Azure que os partidos podem descobrir e conectar a partir da perspetiva da sua própria rede. O ponto de encontro chama-se "Conexão Híbrida" nesta e noutra documentação, nas APIs, e também no portal Azure. O ponto final do serviço Hybrid Connections é referido como o "serviço" para o resto deste artigo.

O serviço permite a transmissão de ligações e respostas http(S) e HTTP(S).

O modelo de interação apoia-se na nomenclatura estabelecida por muitas outras APIs de networking. Há um ouvinte que primeiro indica disponibilidade para lidar com as ligações recebidas, e posteriormente as aceita à medida que chegam. Por outro lado, um cliente conecta-se com o ouvinte, esperando que essa ligação seja aceite para estabelecer uma via de comunicação bidis. "Connect", "Listen" e "Accept" são os mesmos termos que se encontram na maioria das APIs da tomada.

Qualquer modelo de comunicação retransmitido tem qualquer uma das partes fazendo ligações de saída em direção a um ponto final de serviço. Isto faz do "ouvinte" também um "cliente" no uso coloquial, e também pode causar outras sobrecargas de terminologia. A terminologia precisa, portanto, utilizada para ligações híbridas é a seguinte:

Os programas de ambos os lados de uma ligação são chamados de "clientes", uma vez que são clientes do serviço. O cliente que espera e aceita ligações é o "ouvinte", ou diz-se que está no "papel de ouvinte". O cliente que inicia uma nova ligação com um ouvinte através do serviço é chamado de "remetente", ou está no "papel de remetente".

### <a name="listener-interactions"></a>Interações de ouvintes

O ouvinte tem cinco interações com o serviço; todos os detalhes do fio são descritos mais tarde neste artigo na secção de referência.

As mensagens Ouvir, Aceitar e Solicitar são recebidas do serviço. As operações de Renovação e Ping são enviadas pelo ouvinte.

#### <a name="listen-message"></a>Ouvir mensagem

Para indicar a prontidão para o serviço de que um ouvinte está pronto a aceitar ligações, cria uma ligação WebSocket de saída. O aperto de mão de ligação tem o nome de uma Ligação Híbrida configurada no espaço de nomes Do Relay, e um símbolo de segurança que confere o "Ouvir" diretamente nesse nome.

Quando o WebSocket é aceite pelo serviço, o registo está completo e o WebSocket estabelecido é mantido vivo como o "canal de controlo" para permitir todas as interações subsequentes. O serviço permite até 25 ouvintes simultâneos para uma Ligação Híbrida. A quota para appHooks deve ser determinada.

Para ligações híbridas, se existirem dois ou mais ouvintes ativos, as ligações recebidas são equilibradas em todas as suas ligações por ordem aleatória; distribuição justa é tentado com o melhor esforço.

#### <a name="accept-message"></a>Aceitar mensagem

Quando um remetente abre uma nova ligação no serviço, o serviço escolhe e notifica um dos ouvintes ativos na Ligação Híbrida. Esta notificação é enviada ao ouvinte sobre o canal de controlo aberto como uma mensagem JSON. A mensagem contém o URL do ponto final WebSocket a que o ouvinte deve ligar para aceitar a ligação.

O URL pode e deve ser utilizado diretamente pelo ouvinte sem qualquer trabalho extra.
As informações codificadas só são válidas por um curto período de tempo, essencialmente enquanto o remetente estiver disposto a aguardar que a ligação seja estabelecida de ponta a ponta. O máximo a assumir é de 30 segundos. O URL só pode ser utilizado para uma tentativa de ligação bem sucedida. Assim que a ligação WebSocket com o URL de encontro for estabelecida, toda a atividade neste WebSocket é retransmitida de e para o remetente. Isto acontece sem qualquer intervenção ou interpretação por parte do serviço.

### <a name="request-message"></a>Mensagem de pedido

Além das ligações WebSocket, o ouvinte também pode receber quadros de pedido HTTP de um remetente, se esta capacidade estiver explicitamente ativada na Ligação Híbrida.

Os ouvintes que se ligam às Ligações Híbridas com suporte HTTP DEVEM lidar com o `request` gesto. Um ouvinte que não lida `request` e, portanto, causa erros de tempo limite repetidos durante a ligação pode ser bloqueado pelo serviço no futuro.

Os metadados de cabeçalho de quadro HTTP são traduzidos em JSON para um manuseamento mais simples pela estrutura do ouvinte, também porque as bibliotecas de análise de cabeçalho HTTP são mais raras do que os párocos JSON. Os metadados HTTP que só são relevantes para a relação entre o remetente e o gateway HTTP do Relé, incluindo informações de autorização, não são reencaminhados. Os corpos de pedido HTTP são transferidos de forma transparente como quadros binários do WebSocket.

O ouvinte pode responder a pedidos HTTP usando um gesto de resposta equivalente.

O fluxo de pedido/resposta utiliza o canal de controlo por predefinição, mas pode ser "atualizado" para um webSocket de encontro distinto sempre que necessário. As ligações WebSocket distintas melhoram a produção para cada conversa do cliente, mas sobrecarregam o ouvinte com mais ligações que precisam de ser tratadas, o que pode não ser um desejo para clientes leves.

No canal de controlo, os órgãos de pedido e resposta limitam-se, no máximo, a 64 kB de tamanho. Os metadados do cabeçalho HTTP estão limitados a um total de 32 kB. Se o pedido ou a resposta excederem esse limiar, o ouvinte DEVE atualizar para um site webSocket utilizando um gesto equivalente ao manuseamento do [Accept](#accept-message).

Para pedidos, o serviço decide se deve encaminhar os pedidos para o canal de controlo. Isto inclui, mas não pode limitar-se aos casos em que um pedido exceda 64 kB (cabeçalhos mais corpo) imediatamente, ou se o pedido for enviado com [codificação de transferência "em pedaços"](https://tools.ietf.org/html/rfc7230#section-4.1) e o serviço tiver motivos para esperar que o pedido exceda 64kB ou ler o pedido não seja instantâneo. Se o serviço optar por entregar o pedido em vez do encontro, apenas passa o endereço de encontro ao ouvinte.
O ouvinte deve então estabelecer o ponto de encontro WebSocket e o serviço prontamente entrega o pedido completo, incluindo corpos sobre o encontro WebSocket. A resposta DEVE também utilizar o encontro WebSocket.

Para pedidos que cheguem ao canal de controlo, o ouvinte decide se responde sobre o canal de controlo ou através do encontro. O serviço DEVE incluir um endereço de encontro com cada pedido encaminhado sobre o canal de controlo. Este endereço é válido apenas para atualizar a partir do pedido atual.

Se o ouvinte optar por fazer o upgrade, ele liga-se e entrega prontamente a resposta sobre a tomada de encontro estabelecida.

Uma vez estabelecido o encontro WebSocket, o ouvinte deve mantê-lo para posterior tratamento de pedidos e respostas do mesmo cliente. O serviço manterá o WebSocket enquanto a ligação https com o remetente persistir e irá encaminhar todos os pedidos subsequentes desse remetente sobre o WebSocket mantido. Se o ouvinte optar por deixar o encontro WebSocket de lado, o serviço também deixará cair a ligação ao remetente, independentemente de um pedido subsequente já estar em curso.

#### <a name="renew-operation"></a>Renovar operação

O sinal de segurança que deve ser utilizado para registar o ouvinte e manter o canal de controlo pode expirar enquanto o ouvinte estiver ativo. A expiração do token não afeta as ligações em curso, mas faz com que o canal de controlo seja abandonado pelo serviço no momento ou logo após o fim do prazo de validade. A operação "renovar" é uma mensagem JSON que o ouvinte pode enviar para substituir o símbolo associado ao canal de controlo, para que o canal de controlo possa ser mantido por longos períodos.

#### <a name="ping-operation"></a>Operação ping

Se o canal de controlo permanecer inativo durante muito tempo, os intermediários a caminho, tais como equilibradores de carga ou NATs podem deixar cair a ligação TCP. A operação "ping" evita isso enviando uma pequena quantidade de dados no canal que lembra a todos na rota da rede que a ligação deve estar viva, e também serve como um teste "ao vivo" para o ouvinte. Se o ping falhar, o canal de controlo deve ser considerado inutilizável e o ouvinte deve voltar a ligar-se.

### <a name="sender-interaction"></a>Interação remetente

O remetente tem duas interações com o serviço: liga uma Tomada Web ou envia pedidos via HTTPS. Os pedidos não podem ser enviados por uma Tomada Web a partir da função de remetente.

#### <a name="connect-operation"></a>Operação de ligação

A operação "connect" abre um WebSocket no serviço, fornecendo o nome da Conexão Híbrida e (opcionalmente, mas requerido por padrão) um símbolo de segurança que confere a permissão de "Enviar" na cadeia de consulta. O serviço interage então com o ouvinte da forma descrita anteriormente, e o ouvinte cria uma ligação de encontro que se junta a este WebSocket. Após a aceitação do WebSocket, todas as interações adicionais nesse WebSocket estão com um ouvinte conectado.

#### <a name="request-operation"></a>Operação de pedido

Para ligações híbridas para as quais a funcionalidade foi ativada, o remetente pode enviar pedidos HTTP em grande parte ilimitados aos ouvintes.

Com exceção de um token de acesso retransmissor que esteja incorporado na cadeia de consulta ou num cabeçalho HTTP do pedido, o Relé é totalmente transparente para todas as operações HTTP no endereço Retransmissão e todos os sufixos da trajetória do endereço do Relé, deixando o ouvinte totalmente controlado pela autorização de ponta a ponta e até mesmo com características de extensão HTTP como [o CORS](https://www.w3.org/TR/cors/).

A autorização do remetente com o ponto final do Relé é ligada por predefinição, mas é OPCIONAL. O proprietário da Ligação Híbrida pode optar por permitir remetentes anónimos. O serviço intercetará, inspecionará e retirará as informações de autorização da seguinte forma:

1. Se a cadeia de consulta contiver uma `sb-hc-token` expressão, a expressão será sempre retirada da cadeia de consulta. Será avaliado se a autorização de retransmissão for ligada.
2. Se os cabeçalhos de pedido contiverem um `ServiceBusAuthorization` cabeçalho, a expressão do cabeçalho será sempre retirada da coleção do cabeçalho.
   Será avaliado se a autorização de retransmissão for ligada.
3. Só se a autorização de retransmissão for ligada, e se os cabeçalhos de pedido contiverem um `Authorization` cabeçalho, e nenhuma das expressões anteriores estiver presente, o cabeçalho será avaliado e despido. Caso contrário, o `Authorization` é sempre transmitido como é.

Se não houver um ouvinte ativo, o serviço devolverá um código de erro 502 "Bad Gateway". Se o serviço não parecer tratar do pedido, o serviço devolverá um "Tempo de Gateway" de 504 após 60 segundos.

### <a name="interaction-summary"></a>Resumo da interação

O resultado deste modelo de interação é que o cliente remetente sai do aperto de mão com um WebSocket "limpo", que está ligado a um ouvinte e que não precisa de mais preâmbulos ou preparação. Este modelo permite que praticamente qualquer implementação de cliente WebSocket existente tire facilmente partido do serviço De Conexões Híbridas, fornecendo um URL construído corretamente na sua camada de cliente WebSocket.

A ligação de encontro WebSocket que o ouvinte obtém através da interação aceitação também é limpa e pode ser entregue a qualquer implementação do servidor WebSocket existente com alguma abstração extra mínima que distingue entre operações de "aceitar" nos ouvintes da rede local do seu quadro e operações remotas de "aceitar" ligações híbridas.

O modelo http request/response dá ao remetente uma área de superfície de protocolo HTTP em grande parte ilimitada com uma camada de autorização OPCIONAL. O ouvinte obtém uma secção de cabeçalho de pedido HTTP pré-analisada que pode ser transformada de volta num pedido HTTP a jusante ou manuseado como está, com quadros binários carregando corpos HTTP. As respostas usam o mesmo formato. As interações com menos de 64 KB do corpo de pedido e resposta podem ser manuseadas sobre uma única Tomada Web que é partilhada para todos os remetentes. Pedidos e respostas maiores podem ser manuseados utilizando o modelo de encontro.

## <a name="protocol-reference"></a>Referência do protocolo

Esta secção descreve os detalhes das interações do protocolo descritas anteriormente.

Todas as ligações WebSocket são feitas na porta 443 como uma atualização de HTTPS 1.1, que é geralmente abstrata por alguma estrutura WebSocket ou API. A descrição aqui é mantida neutra, sem sugerir um quadro específico.

### <a name="listener-protocol"></a>Protocolo do ouvinte

O protocolo do ouvinte consiste em dois gestos de ligação e três operações de mensagem.

#### <a name="listener-control-channel-connection"></a>Ligação do canal de controlo do ouvinte

O canal de controlo é aberto com a criação de uma ligação WebSocket para:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address`É o nome de domínio totalmente qualificado do espaço de nome Azure Relay que acolhe a Conexão Híbrida, tipicamente do formulário `{myname}.servicebus.windows.net` .

As opções de parâmetros de cadeia de consulta são as seguintes.

| Parâmetro        | Obrigatório | Descrição
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sim      | Para o papel de ouvinte, o parâmetro deve ser **sb-hc-action=ouvir**
| `{path}`         | Sim      | O caminho do espaço de nome codificado por URL da Ligação Híbrida pré-configurada para registar este ouvinte. Esta expressão é anexada à parte do `$hc/` caminho fixo.
| `sb-hc-token`    | Sim\*    | O ouvinte deve fornecer um token de acesso compartilhado de serviço de serviço codificado por URL para o espaço de nome ou conexão híbrida que confere o direito **de escuta.**
| `sb-hc-id`       | Não       | Este ID opcional fornecido pelo cliente permite o rastreio de diagnóstico de ponta a ponta.

Se a ligação WebSocket falhar devido à não inscrição do caminho de Ligação Híbrida, ou a um token inválido ou em falta, ou a qualquer outro erro, o feedback de erro é fornecido utilizando o modelo regular de feedback do estado HTTP 1.1. A descrição do estado contém um id de rastreio de erro que pode ser comunicado ao pessoal de apoio da Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado   | O sinal de segurança está em falta ou mal formado ou inválido.
| 403  | Proibido      | O símbolo de segurança não é válido para este caminho para esta ação.
| 500  | Erro Interno | Algo correu mal no serviço.

Se a ligação WebSocket for intencionalmente desligada pelo serviço depois de ter sido inicialmente configurada, a razão para o fazer é comunicada usando um código de erro de protocolo WebSocket apropriado, juntamente com uma mensagem de erro descritiva que também inclui um ID de rastreamento. O serviço não desliga o canal de controlo sem encontrar uma condição de erro. Qualquer paragem limpa é controlada pelo cliente.

| Estado WS | Descrição
| --------- | -------------------------------------------------------------------------------
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O sinal de segurança expirou, pelo que a política de autorização é violada.
| 1011      | Algo correu mal no serviço.

#### <a name="accept-handshake"></a>Aceite o aperto de mão

A notificação "aceitar" é enviada pelo serviço ao ouvinte através do canal de controlo previamente estabelecido como uma mensagem JSON num quadro de texto WebSocket. Não há resposta a esta mensagem.

A mensagem contém um objeto JSON chamado "aceitar", que define as seguintes propriedades neste momento:

* **endereço** – o string URL a ser usado para estabelecer o WebSocket ao serviço para aceitar uma ligação de entrada.
* **id** – o identificador único para esta ligação. Se o ID foi fornecido pelo cliente remetente, é o valor fornecido pelo remetente, caso contrário, é um valor gerado pelo sistema.
* **connectHeaders** – todos os cabeçalhos HTTP que foram fornecidos ao ponto final do Relé pelo remetente, que também inclui o Protocolo Sec-WebSocket e os cabeçalhos Sec-WebSocket-Extensions.

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

Se a mensagem "aceitar" tiver um `Sec-WebSocket-Protocol` cabeçalho, espera-se que o ouvinte só aceite o WebSocket se apoiar esse protocolo. Além disso, define o cabeçalho à medida que o WebSocket é estabelecido.

O mesmo se aplica ao `Sec-WebSocket-Extensions` cabeçalho. Se o quadro suportar uma extensão, deve definir o cabeçalho na resposta do lado do servidor do aperto de mão necessário `Sec-WebSocket-Extensions` para a extensão.

O URL deve ser utilizado como destinar-se ao estabelecimento da tomada de aceitação, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar uma tomada, o parâmetro deve ser `sb-hc-action=accept`
| `{path}`       | Sim      | (ver o parágrafo seguinte)
| `sb-hc-id`     | Não       | Consulte a descrição anterior do **id**.

`{path}` é o caminho do espaço de nome codificado por URL da ligação híbrida pré-configurada para registar este ouvinte. Esta expressão é anexada à parte do `$hc/` caminho fixo.

A `path` expressão pode ser estendida com um sufixo e uma expressão de corda de consulta que segue o nome registado após um corte para a frente de separação.
Isto permite ao cliente remetente passar argumentos de despacho para o ouvinte aceitante quando não é possível incluir cabeçalhos HTTP. A expectativa é que o quadro do ouvinte analise a parte do caminho fixo e o nome registado do caminho e faça com que o restante, possivelmente sem quaisquer argumentos de cadeia de consulta, seja prefixado no `sb-` pedido de decisão sobre se aceita a ligação.

Para mais informações, consulte a seguinte secção "Protocolo de Remetente".

Se houver um erro, o serviço pode responder da seguinte forma:

| Código | Erro          | Descrição
| ---- | -------------- | -----------------------------------
| 403  | Proibido      | O URL não é válido.
| 500  | Erro Interno | Algo correu mal no serviço.

 Após a ligação ter sido estabelecida, o servidor desliga o WebSocket quando o remetente WebSocket se desliga, ou com o seguinte estado:

| Estado WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a ligação.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O sinal de segurança expirou, pelo que a política de autorização é violada. |
| 1011      | Algo correu mal no serviço.                                            |

##### <a name="rejecting-the-socket"></a>Rejeitando a tomada

 Rejeitar a tomada depois de inspecionar a `accept` mensagem requer um aperto de mão semelhante para que o código de estado e a descrição do estado que comunicam o motivo da rejeição possam fluir de volta para o remetente.

 A escolha do design do protocolo aqui é usar um aperto de mão WebSocket (que é projetado para terminar em um estado de erro definido) para que as implementações do cliente ouvinte possam continuar a depender de um cliente WebSocket e não precisam de empregar um cliente HTTP extra, nu.

 Para rejeitar a tomada, o cliente retira o endereço URI da `accept` mensagem e anexa dois parâmetros de cadeia de consulta à mensagem, da seguinte forma:

| Param                   | Obrigatório | Descrição                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Sim      | Código de estado HTTP numérico.                |
| sb-hc-statusDescription | Sim      | Razão legível para a rejeição. |

O URI resultante é então utilizado para estabelecer uma ligação WebSocket.

Ao completar corretamente, este aperto de mão falha intencionalmente com um código de erro HTTP 410, uma vez que não foi estabelecido nenhum WebSocket. Se algo correr mal, os seguintes códigos descrevem o erro:

| Código | Erro          | Descrição                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Proibido      | O URL não é válido.                |
| 500  | Erro Interno | Algo correu mal no serviço. |

#### <a name="request-message"></a>Mensagem de pedido

A `request` mensagem é enviada pelo serviço ao ouvinte através do canal de controlo. A mesma mensagem também é enviada sobre o encontro WebSocket uma vez estabelecido.

O `request` é composto por duas partes: um cabeçalho e uma estrutura de corpo binário.
Se não houver corpo, os quadros do corpo são omitidos. O indicador para se um corpo está presente é a `body` propriedade booleana na mensagem de pedido.

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

O porta-mãos do ouvinte MUST recebendo o corpo do pedido dividido em vários quadros binários (ver [fragmentos WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
O pedido termina quando for recebida uma moldura binária com o conjunto de bandeira FIN.

Para um pedido sem um corpo, só há uma moldura de texto.

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

O conteúdo do JSON `request` é o seguinte:

* **endereço** - cadeia URI. Este é o endereço de encontro a utilizar para este pedido. Se o pedido de entrada for superior a 64 kB, o restante desta mensagem fica vazio e o cliente DEVE iniciar um aperto de mão de encontro equivalente à `accept` operação descrita abaixo. O serviço colocará então o completo `request` na tomada web estabelecida. Se se pode esperar que a resposta exceda 64 kB, o ouvinte DEVE também iniciar um aperto de mão de encontro e, em seguida, transferir a resposta sobre a tomada web estabelecida.
* **id** – corda. O identificador único para este pedido.
* **solicitadoresHeaders** – este objeto contém todos os cabeçalhos HTTP que foram fornecidos ao ponto final pelo remetente, com exceção das informações de autorização, conforme [explicado acima](#request-operation), e cabeçalhos que se relacionam estritamente com a ligação com o gateway. Especificamente, todos os cabeçalhos definidos ou reservados em [RFC7230,](https://tools.ietf.org/html/rfc7230) `Via` exceto, são despojados e não reencaminhados:

  * `Connection` (RFC7230, Secção 6.1)
  * `Content-Length`  (RFC7230, Secção 3.3.2)
  * `Host`  (RFC7230, Secção 5.4)
  * `TE`  (RFC7230, Secção 4.3)
  * `Trailer`  (RFC7230, Secção 4.4)
  * `Transfer-Encoding`  (RFC7230, Secção 3.3.1)
  * `Upgrade` (RFC7230, Secção 6.7)
  * `Close`  (RFC7230, Secção 8.1)

* **requestTarget** – cadeia. Este imóvel detém o  ["Target request" (RFC7230, Secção 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) do pedido. Isto inclui a porção de cadeia de consulta, que é despojada de TODOS os `sb-hc-` parâmetros pré-fixados.
* **método** - corda. Este é o método do pedido, por [RFC7231, Secção 4](https://tools.ietf.org/html/rfc7231#section-4). O `CONNECT` método NÃO DEVE ser utilizado.
* **corpo** - boolean. Indica se segue uma ou mais molduras binárias do corpo.

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

##### <a name="responding-to-requests"></a>Respondendo a pedidos

O recetor DEVE responder. O incumprimento repetido dos pedidos, mantendo a ligação, pode resultar no bloqueio do ouvinte.

As respostas podem ser enviadas em qualquer encomenda, mas cada pedido deve ser respondido no prazo de 60 segundos ou a entrega será reportada como tendo falhado. O prazo de 60 segundos é contado até que o `response` quadro tenha sido recebido pelo serviço. Uma resposta contínua com várias molduras binárias não pode ficar inativa por mais de 60 segundos ou é terminada.

Se o pedido for recebido através do canal de controlo, a resposta DEVE ser enviada no canal de controlo a partir do local onde o pedido foi recebido ou DEVE ser enviada através de um canal de encontro.

A resposta é um objeto JSON chamado "resposta". As regras para manusear o conteúdo corporal são exatamente como com a `request` mensagem e com base na `body` propriedade.

* **requestId** - cadeia. Necessário. O `id` valor patrimonial da `request` mensagem a que está a ser respondida.
* **statusCode** – número. Necessário. um código de estado HTTP numérico que indica o resultado da notificação. Todos os códigos de estado de [RFC7231, secção 6](https://tools.ietf.org/html/rfc7231#section-6) são permitidos, com exceção [de 502 "Bad Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e [504 "Gateway Timeout".](https://tools.ietf.org/html/rfc7231#section-6.6.5)
* **statusDescrição** - cadeia. Opcional. Frase de razão do código de estado HTTP por [RFC7230, Secção 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **respostaHeaders** – cabeçalhos HTTP a definir numa resposta HTTP externa.
  Tal como acontece com os `request` cabeçalhos definidos RFC7230 NÃO devem ser utilizados.
* **corpo** - boolean. Indica se os quadros binários do corpo seguem-se.

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

Para respostas superiores a 64 kB, a resposta DEVE ser entregue sobre uma tomada de encontro. Além disso, se o pedido exceder 64 kB, e o `request` único contiver o campo de endereços, deve ser estabelecida uma tomada de encontro para obter o `request` . Uma vez estabelecida uma tomada de encontro, as respostas ao respetivo cliente e os pedidos subsequentes desse respetivo cliente DEVEM ser entregues sobre a tomada de encontro enquanto este persiste.

O `address` URL no deve ser utilizado `request` como-é para estabelecer a tomada de encontro, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar uma tomada, o parâmetro deve ser `sb-hc-action=request`

Se houver um erro, o serviço pode responder da seguinte forma:

| Código | Erro           | Descrição
| ---- | --------------- | -----------------------------------
| 400  | Pedido Inválido | Ação não reconhecida ou URL não é válido.
| 403  | Proibido       | A URL expirou.
| 500  | Erro Interno  | Algo correu mal no serviço.

 Após a ligação ter sido estabelecida, o servidor desliga o WebSocket quando a tomada HTTP do cliente se desliga, ou com o seguinte estado:

| Estado WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a ligação.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O sinal de segurança expirou, pelo que a política de autorização é violada. |
| 1011      | Algo correu mal no serviço.                                            |


#### <a name="listener-token-renewal"></a>Renovação simbólica do ouvinte

Quando o sinal do ouvinte está prestes a expirar, pode substituí-lo enviando uma mensagem de texto para o serviço através do canal de controlo estabelecido. A mensagem contém um objeto JSON chamado `renewToken` , que define a seguinte propriedade neste momento:

* **token** – um token de acesso compartilhado de serviço de serviço de serviço codificado por URL para o espaço de nome ou conexão híbrida que confere o direito **de escuta.**

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se a validação do token falhar, o acesso é negado e o serviço de nuvem fecha o canal de controlo WebSocket com um erro. Caso contrário, não há resposta.

| Estado WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | O sinal de segurança expirou, pelo que a política de autorização é violada. |

### <a name="web-socket-connect-protocol"></a>Protocolo de ligação da tomada web

O protocolo do remetente é efetivamente idêntico à forma como um ouvinte é estabelecido.
O objetivo é a máxima transparência para o WebSocket de ponta a ponta. O endereço a que se conecta é o mesmo que para o ouvinte, mas a "ação" difere e o símbolo precisa de uma permissão diferente:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

O _endereço de espaço-nome_ é o nome de domínio totalmente qualificado do espaço de nome Azure Relay que acolhe a Conexão Híbrida, tipicamente do formulário `{myname}.servicebus.windows.net` .

O pedido pode conter cabeçalhos HTTP adicionais arbitrários, incluindo os definidos pela aplicação. Todos os cabeçalhos fornecidos fluem para o ouvinte e podem ser encontrados no `connectHeader` objeto da mensagem de controlo de **aceitação.**

As opções de parâmetro de cadeia de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sim       | Para o papel de remetente, o parâmetro deve ser `sb-hc-action=connect` .
| `{path}`       | Sim       | (ver o parágrafo seguinte)
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um token de acesso compartilhado de serviço de serviço codificado por URL para o espaço de nome ou conexão híbrida que confere o direito **enviar.**
| `sb-hc-id`     | Não        | Um ID opcional que permite o rastreio de diagnóstico de ponta a ponta e é disponibilizado ao ouvinte durante o aperto de mão aceite.

 O `{path}` é o caminho do espaço de nome codificado por URL da ligação híbrida pré-configurada para registar este ouvinte. A `path` expressão pode ser estendida com um sufixo e uma expressão de corda de consulta para comunicar mais. Se a Ligação Híbrida for registada no `hyco` caminho, a `path` expressão pode ser seguida pelos `hyco/suffix?param=value&...` parâmetros de cadeia de consulta definidos aqui. Uma expressão completa pode então ser a seguinte:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

A `path` expressão é transmitida ao ouvinte no endereço URI contido na mensagem de controlo "aceitar".

Se a ligação WebSocket falhar devido à não inscrição do caminho de Ligação Híbrida, a um token inválido ou em falta, ou a qualquer outro erro, o feedback de erro é fornecido utilizando o modelo regular de feedback do estado HTTP 1.1. A descrição do estado contém um ID de rastreio de erro que pode ser comunicado ao pessoal de apoio da Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não encontrado      | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado   | O sinal de segurança está em falta ou mal formado ou inválido.
| 403  | Proibido      | O símbolo de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno | Algo correu mal no serviço.

Se a ligação WebSocket for intencionalmente desligada pelo serviço depois de ter sido inicialmente configurada, a razão para o fazer é comunicada usando um código de erro de protocolo WebSocket apropriado, juntamente com uma mensagem de erro descritiva que também inclui um ID de rastreamento.

| Estado WS | Descrição
| --------- | ------------------------------------------------------------------------------- 
| 1000      | O ouvinte fechou a tomada.
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O sinal de segurança expirou, pelo que a política de autorização é violada.
| 1011      | Algo correu mal no serviço.

### <a name="http-request-protocol"></a>Protocolo de pedido HTTP

O protocolo http request permite pedidos HTTP arbitrários, exceto atualizações de protocolo.
Os pedidos HTTP são apontados para o endereço de tempo de execução regular da entidade, sem a $hc infixo que é usado para ligações híbridas clientes WebSocket.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

O _endereço de espaço-nome_ é o nome de domínio totalmente qualificado do espaço de nome Azure Relay que acolhe a Conexão Híbrida, tipicamente do formulário `{myname}.servicebus.windows.net` .

O pedido pode conter cabeçalhos HTTP adicionais arbitrários, incluindo os definidos pela aplicação. Todos os cabeçalhos fornecidos, exceto os definidos diretamente no RFC7230 (ver [mensagem de pedido)](#request-message)fluem para o ouvinte e podem ser encontrados no `requestHeader` objeto da mensagem de **pedido.**

As opções de parâmetro de cadeia de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um token de acesso compartilhado de serviço de serviço codificado por URL para o espaço de nome ou conexão híbrida que confere o direito **enviar.**

O token também pode ser transportado no `ServiceBusAuthorization` cabeçalho ou `Authorization` HTTP. O token pode ser omitido se a Ligação Híbrida estiver configurada para permitir pedidos anónimos.

Uma vez que o serviço atua efetivamente como um representante, mesmo que não como um verdadeiro representante HTTP, ou adiciona um `Via` cabeçalho ou anota o cabeçalho existente `Via` em conformidade com o [RFC7230, secção 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
O serviço adiciona o nome de anfitrião do espaço de identificação do Relay a `Via` .

| Código | Mensagem  | Descrição                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | O pedido foi tratado por pelo menos um ouvinte.  |
| 202  | Aceite | O pedido foi aceite por pelo menos um ouvinte. |

Se houver um erro, o serviço pode responder da seguinte forma. Se a resposta tem origem no serviço ou no ouvinte pode ser identificada através da presença do `Via` cabeçalho. Se o cabeçalho estiver presente, a resposta é do ouvinte.

| Código | Erro           | Descrição
| ---- | --------------- |--------- |
| 404  | Não encontrado       | O caminho de ligação híbrida é inválido ou o URL base está mal formado.
| 401  | Não autorizado    | O sinal de segurança está em falta ou mal formado ou inválido.
| 403  | Proibido       | O símbolo de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno  | Algo correu mal no serviço.
| 503  | Bad Gateway     | O pedido não podia ser encaminhado para nenhum ouvinte.
| 504  | Tempo de gateway | O pedido foi encaminhado para um ouvinte, mas o ouvinte não reconheceu o recibo no tempo necessário.

## <a name="next-steps"></a>Passos seguintes

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Começar com .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)
