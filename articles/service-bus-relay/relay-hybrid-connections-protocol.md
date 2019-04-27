---
title: Guia de protocolo de ligações híbridas de reencaminhamento do Azure | Documentos da Microsoft
description: Guia de protocolo de ligações híbridas de reencaminhamento do Azure.
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
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: e96d0103a03e841f39e8adb88215f6d6e24a305a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420050"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de ligações híbridas de reencaminhamento do Azure

Reencaminhamento do Azure é um dos pilares capacidades-chave da plataforma do Azure Service Bus. A nova _ligações híbridas_ capacidade de reencaminhamento é uma evolução segura, e de protocolo aberto baseada em HTTP e WebSockets. Ela substitui o anterior igualmente chamada _os serviços BizTalk_ funcionalidade que foi criada com base numa Fundação de protocolo proprietário. A integração de ligações híbridas, em serviços aplicacionais do Azure irá continuar a funcionar como-é.

Ligações híbridas permitem a comunicação em fluxo bidirecional, o binário e fluxo de datagrama simples entre as duas aplicações em rede. Qualquer uma das ou ambas as partes podem residir em NATs ou firewalls.

Este artigo descreve as interações do lado do cliente com o reencaminhamento de ligações híbridas de ligação dos clientes em funções do serviço de escuta e o remetente. Ele também descreve como os serviços de escuta aceitam novas ligações e pedidos.

## <a name="interaction-model"></a>Modelo de interação

O reencaminhamento de ligações híbridas liga-se duas partes, fornecendo um ponto de rendezvous na cloud do Azure que as partes podem descobrir e ligar a partir do ponto de vista de sua própria rede. O ponto de rendezvous é chamado "Ligação híbrida" neste e outra documentação, nas APIs e também no portal do Azure. O ponto de extremidade do serviço de ligações híbridas é referido como "serviço" para o restante deste artigo.

Permite que o serviço para retransmitir conexões de soquete de Web e pedidos de HTTP (S) e as respostas.

O modelo de interação depende de nomenclatura estabelecida por muitas outras APIs de rede. Existe um serviço de escuta que primeiro indica a preparação para lidar com as ligações de entrada e, em seguida, aceita-las à medida que chegam. No outro lado, um cliente liga-se para o serviço de escuta, esperando por essa ligação sejam aceites para estabelecer um caminho de comunicação bidirecional. "Ligar", "Escutar" e "Aceitar" é os mesmos termos que encontrará no soquete a maioria das APIs.

Qualquer modelo de comunicação retransmitidas tem qualquer uma das partes tornando as ligações de saída para um ponto de extremidade de serviço. Isso torna o "serviço de escuta" também um "cliente" em uso colloquial e também pode fazer com que outras sobrecargas de terminologia. Segue-se a terminologia precisa, portanto, é utilizada para ligações híbridas:

Os programas em ambos os lados de uma ligação são chamados de "clientes", uma vez que eles são os clientes para o serviço. O cliente que aguarda e aceita ligações é o "serviço de escuta", ou é conhecido como "função de serviço de escuta." O cliente que inicia uma nova ligação para um serviço de escuta através do serviço é chamado "remetente", ou está na "função de remetente".

### <a name="listener-interactions"></a>Interações de serviço de escuta

O serviço de escuta tem cinco interações com o serviço; todos os detalhes de conexão são descritos posteriormente neste artigo na seção de referência.

As mensagens de escuta, aceitar e pedido são recebidas do serviço. A renovação, e as operações de Ping são enviadas pelo serviço de escuta.

#### <a name="listen-message"></a>Ouça a mensagem

Para indicar a preparação para o serviço que é um serviço de escuta pronto para aceitar ligações, ele cria uma ligação de WebSocket de saída. O handshake de ligação tem o nome de uma ligação híbrida configurado no espaço de nomes reencaminhamento e um token de segurança que confere "Listen" diretamente nesse nome.

Quando o WebSocket é aceite pelo serviço, o registo está concluído e o WebSocket estabelecido é mantido ativo como o "canal de controlo" para ativar todas as interações subsequentes. O serviço permite até 25 serviços de escuta em simultâneo uma ligação híbrida. A quota para AppHooks é ser determinada.

Para ligações híbridas, se existirem dois ou mais serviços de escuta ativos, as ligações de entrada são balanceadas entre-los em ordem aleatória; distribuição justa é tentada com melhor esforço.

#### <a name="accept-message"></a>Aceitar a mensagem

Quando um remetente abre uma nova ligação do serviço, o serviço escolhe e notifica um dos serviços de escuta ativos na ligação híbrida. Esta notificação é enviada para o serviço de escuta através do canal de controlo aberto como uma mensagem JSON. A mensagem contém o URL do ponto de extremidade do WebSocket que o serviço de escuta tem de ligar para aceitar a ligação.

O URL pode e deve ser utilizado diretamente pelo serviço de escuta sem nenhum trabalho extra.
As informações de codificada só são válidas durante um curto período de tempo, essencialmente para desde que o remetente está disposto a esperar que a conexão seja estabelecida ponto-a-ponto. O máximo presumir que é 30 segundos. O URL só pode ser utilizado para a tentativa de uma ligação com êxito. Assim que for estabelecida a conexão WebSocket com o URL de rendezvous, todas as atividades adicionais neste WebSocket é passada de e para o remetente. Isto acontece sem qualquer intervenção ou a interpretação pelo serviço.

### <a name="request-message"></a>Mensagem de pedido

Para além das ligações de WebSocket, o serviço de escuta também pode receber quadros de pedido HTTP de um remetente, se esta capacidade está explicitamente ativada na ligação híbrida.

Serviços de escuta anexar às ligações híbridas com suporte para HTTP tem de processar o `request` gesto. Um serviço de escuta não lida com `request` e, portanto, faz com que os erros de tempo limite repetidos enquanto a ser ligado pode ser bloqueado pelo serviço no futuro.

Metadados de cabeçalho do quadro HTTP é convertido em JSON para a manipulação mais simples pela estrutura de serviço de escuta, também como bibliotecas de análise de cabeçalho HTTP são mais raros que analisadores JSON. Metadados HTTP que só são relevante para a relação entre o remetente e o gateway de reencaminhamento HTTP, incluindo informações de autorização, não será encaminhado. Corpos de pedido HTTP são transferidos transparente como quadros de WebSocket binários.

O serviço de escuta pode responder a pedidos HTTP utilizando um gesto de resposta equivalente.

O fluxo de solicitação/resposta usa o canal de controlo por padrão, mas pode ser "Atualizar" para um encontro distinto WebSocket sempre que for necessário. Ligações de WebSocket distintas melhorar o débito para cada conversação do cliente, mas eles burden o serviço de escuta com mais ligações que têm de ser tratadas, que pode não ser capaz de desejo de terem clientes leves.

Sobre o canal de controlo, corpos de solicitação e resposta estão limitados a no máximo 64 kB de tamanho. Metadados de cabeçalho HTTP estão limitado a um total de 32 kB. Se a solicitação ou resposta exceder esse limite, o serviço de escuta tem de atualizar para um encontro WebSocket usando um gesto equivalente a manipulação da [Accept](#accept-message).

Para pedidos, o serviço decide se deve encaminhar os pedidos através do canal de controlo. Isto inclui, mas não pode estar limitado a casos em que um pedido excede os 64 kB (cabeçalhos mais corpo) imediatamente, ou se a solicitação é enviada com ["a" transfer-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) e o serviço tem razão esperar para o pedido exceder os 64 kB ou ler o pedido não é efetuada instantaneamente. Se o serviço de optar por entregar o pedido ao longo de rendezvous, ele passa apenas o endereço de encontro para o serviço de escuta.
O serviço de escuta, em seguida, tem de estabelecer o encontro WebSocket e o serviço de entrega imediatamente o pedido completo, incluindo corpos sobre o encontro WebSocket. A resposta também tem de utilizar o encontro WebSocket.

Para solicitações que chegam através do canal de controlo, o serviço de escuta decide se é necessário responder através do canal de controlo ou através de encontro. O serviço tem de incluir um endereço de encontro com cada solicitação encaminhada através do canal de controlo. Este endereço só é válido para a atualização do pedido atual.

Se o serviço de escuta optar por atualizar, liga e também imediatamente a resposta aos socket encontro estabelecidos.

Uma vez o encontro que websocket foi estabelecida, o serviço de escuta deverá mantê-lo para ainda mais a manipulação de pedidos e respostas do mesmo cliente. O serviço irá manter o WebSocket para, desde que o HTTPS ligação com o remetente de soquete persiste e encaminhará todos os pedidos subsequentes desse remetente sobre o WebSocket manter. Se escolher o serviço de escuta remover o encontro WebSocket do seu lado, o serviço também removerá a ligação ao remetente, independentemente se um pedido subsequente pode já estar em curso.

#### <a name="renew-operation"></a>A operação de renovação

O token de segurança que deve ser usado para registrar o serviço de escuta e manter o canal de controlo poderão expirar enquanto o serviço de escuta está ativo. A expiração do token não afeta as ligações em curso, mas ele faz com que o canal de controlo a ser removido pelo serviço em ou logo após o momento da expiração. A operação "renovar" é uma mensagem JSON que o serviço de escuta pode enviar para substituir o token associado com o canal de controlo, para que o canal de controlo pode ser mantido por períodos prolongados.

#### <a name="ping-operation"></a>Operação de ping

Se o canal de controlo permanece ocioso por muito tempo, intermediários a caminho, tais como carga balanceadores ou NATs podem remover a ligação de TCP. A operação de "ping" evita que o enviando uma pequena quantidade de dados no canal que irá relembrá-todas as pessoas a rota de rede que a conexão deve estar ativo, e ele também serve como um teste de "dinâmicos" para o serviço de escuta. Se o ping falhar, o canal de controlo deve ser considerado inutilizável e o serviço de escuta deve restabelecer a ligação.

### <a name="sender-interaction"></a>Interação de remetente

O remetente possui dois interações com o serviço: ele se conecta um soquete de Web ou envia solicitações por meio de HTTPS. Não não possível enviar pedidos através de um Socket Web, da função do remetente.

#### <a name="connect-operation"></a>Operação de ligação

A operação "ligar" abre um WebSocket no serviço, fornecendo o nome da ligação híbrida e (opcionalmente, mas exigido por predefinição) uma de token de segurança conferring permissão de "Enviar" na cadeia de consulta. O serviço, em seguida, interage com o serviço de escuta do modo descrito anteriormente, e o serviço de escuta cria uma ligação de encontro é associada este WebSocket. Depois de ter sido aceite o WebSocket, são todas as interações mais no que WebSocket com um serviço de escuta ligado.

#### <a name="request-operation"></a>Operação de pedido

Para as ligações híbridas para o qual o recurso foi ativado, o remetente pode enviar pedidos HTTP em grande parte sem restrições para serviços de escuta.

Exceto para um token de acesso de reencaminhamento que é que um incorporado na cadeia de consulta ou num cabeçalho de HTTP do pedido, o reencaminhamento é totalmente transparente para todas as operações de HTTP no endereço de reencaminhamento e todos os sufixos do caminho de endereço de reencaminhamento, deixando a escuta completamente no controle de en autorização de d-a-ponto e até mesmo recursos de extensão HTTP, como [CORS](https://www.w3.org/TR/cors/).

Autorização de remetente com o ponto final de reencaminhamento está ativada por predefinição, mas é opcional. O proprietário da ligação híbrida pode optar por permitir que os remetentes anônimos. O serviço irá interceptar, inspecionar e retirar informações de autorização da seguinte forma:

1. Se a cadeia de consulta contém um `sb-hc-token` expressão, a expressão sempre será eliminada da cadeia de consulta. Será avaliado se a autorização de reencaminhamento está ativada.
2. Se os cabeçalhos de pedido contém um `ServiceBusAuthorization` cabeçalho, o cabeçalho de expressão sempre ser retirada da coleção de cabeçalhos.
   Será avaliado se a autorização de reencaminhamento está ativada.
3. Apenas se a autorização de reencaminhamento estiver ativada, e se os cabeçalhos de pedido contém um `Authorization` cabeçalho e nenhuma das expressões anteriores está presente, o cabeçalho será avaliado e removido. Caso contrário, o `Authorization`sempre é passado como-é.

Se não houver nenhum serviço de escuta ativo, o serviço irá devolver um código de erro "Gateway inválido" 502. Se o serviço não é apresentado manipular a solicitação, o serviço irá devolver um 504 "tempo limite do Gateway" depois de 60 segundos.

### <a name="interaction-summary"></a>Resumida de interação

O resultado deste modelo de interação é que o cliente remetente vem sem o handshake do WebSocket "limpo", de que está ligado a um serviço de escuta e o que precisa sem ainda mais preâmbulos ou preparação. Este modelo permite praticamente qualquer implementação de cliente existente do WebSocket prontamente tirar partido do serviço de ligações híbridas ao fornecer um URL construído corretamente em sua camada de cliente WebSocket.

A ligação de rendezvous WebSocket, que o serviço de escuta obtém através da interação a aceitar também é limpa e pode ser entregue a qualquer implementação do servidor WebSocket existente com alguma abstração extra mínimo que distingue entre operações de "aceitar" em Serviços de escuta de rede local e remoto de ligações híbridas do respetivo framework "aceitam" operações.

O modelo de solicitação/resposta HTTP fornece o remetente uma amplamente sem restrições HTTP protocolo área de superfície com uma camada de autorização opcional. O serviço de escuta obtém uma previamente analisada HTTP pedido seção de cabeçalho que pode ser reativada novamente num pedido HTTP downstream ou processada conforme, com quadros binários carrega os corpos HTTP. Respostas de utilizam o mesmo formato. As interações com menos de 64 KB de corpo de pedido e resposta podem ser processadas num único soquete de Web, que é partilhada por todos os remetentes. Maiores solicitações e respostas podem ser processadas usando o modelo de encontro.

## <a name="protocol-reference"></a>Referência do protocolo

Esta secção descreve os detalhes das interações de protocolo descritos anteriormente.

Todas as ligações de WebSocket são feitas na porta 443 como uma atualização do HTTPS 1.1, que normalmente são abstraídas por alguns framework WebSocket ou API. A descrição aqui é mantida implementação neutro, sem sugerindo uma arquitetura específica.

### <a name="listener-protocol"></a>Protocolo de serviço de escuta

O protocolo de serviço de escuta é constituído por dois gestos de ligação e três operações de mensagem.

#### <a name="listener-control-channel-connection"></a>Ligação de canal de controlo do serviço de escuta

O canal de controlo é aberto com a criação de uma conexão WebSocket para:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

O `namespace-address` é o nome de domínio completamente qualificado do espaço de nomes de reencaminhamento do Azure que aloja a ligação híbrida, normalmente do formulário `{myname}.servicebus.windows.net`.

Seguem-se as opções de parâmetros de cadeia de caracteres de consulta.

| Parâmetro        | Necessário | Descrição
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sim      | Para a função de serviço de escuta, o parâmetro tem de ser **sb-hc-action = escuta**
| `{path}`         | Sim      | O caminho de espaço de nomes com codificação URL da ligação híbrida previamente configurada para registar este serviço de escuta no. Esta expressão é acrescentada para o fixo `$hc/` parte do caminho.
| `sb-hc-token`    | Sim\*    | O serviço de escuta tem de fornecer um válido, com codificação URL Shared Access tokens do Service Bus para o espaço de nomes ou a ligação híbrida que confere a **escutar** certo.
| `sb-hc-id`       | Não       | Este ID opcional fornecido pelo cliente permite que o rastreamento de diagnóstico de ponto-a-ponto.

Se a conexão WebSocket falhar devido ao caminho de ligação híbrida não a ser registada, ou um token inválido ou está em falta ou algum outro erro, os comentários de erro é fornecido com o modelo de comentários de status HTTP 1.1 regular. A descrição do Estado contém um erro-id de controlo que pode ser comunicada ao pessoal de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não Encontrado      | O caminho de ligação híbrida é inválido ou o URL de base tem um formato incorreto.
| 401  | Não autorizado   | O token de segurança está em falta ou tem um formato incorreto ou é inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho para esta ação.
| 500  | Erro Interno | Ocorreu um erro no serviço.

Se a conexão WebSocket é intencionalmente encerrada pelo serviço após ele foi inicialmente configurado, o motivo para se o fizer, é comunicado com um código de erro de protocolo WebSocket adequado juntamente com uma mensagem de erro descritivo que também inclui um ID de controlo. O serviço não será desligado o canal de controlo sem encontrar uma condição de erro. Qualquer encerramento correto é controlado de cliente.

| Estado de WS | Descrição
| --------- | -------------------------------------------------------------------------------
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O token de segurança expirou, portanto, a política de autorização é violada.
| 1011      | Ocorreu um erro no serviço.

#### <a name="accept-handshake"></a>Aceitar o handshake

A notificação "aceitar" é enviada pelo serviço para o serviço de escuta, através do canal de controlo estabelecida anteriormente como uma mensagem JSON num quadro de texto do WebSocket. Não existe nenhuma resposta a esta mensagem.

A mensagem contém um objeto JSON com o nome "Aceito", que define as propriedades seguintes neste momento:

* **endereço** – a cadeia de URL a ser utilizado para estabelecer o WebSocket para o serviço para aceitar uma ligação recebida.
* **ID** – o identificador exclusivo para esta ligação. Se o ID fornecido pelo cliente do remetente, é o valor do remetente fornecido, caso contrário, é um valor gerado pelo sistema.
* **connectHeaders** – todos os cabeçalhos HTTP que foram fornecidos pelo remetente, que também inclui o protocolo de WebSocket de seg e os cabeçalhos Sec-WebSocket-extensões para o ponto final de reencaminhamento.

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

O URL de endereço fornecido na mensagem do JSON é utilizado pelo serviço de escuta para estabelecer o WebSocket para aceitar ou rejeitar o socket de remetente.

##### <a name="accepting-the-socket"></a>Aceitação de soquete

Para aceitar, o serviço de escuta estabelece uma conexão WebSocket para o endereço fornecido.

Se a mensagem "aceitar" acarreta um `Sec-WebSocket-Protocol` cabeçalho, espera-se que o serviço de escuta só aceita o WebSocket se ele oferece suporte esse protocolo. Além disso, ele define o cabeçalho à medida que o WebSocket é estabelecida.

O mesmo se aplica para o `Sec-WebSocket-Extensions` cabeçalho. Se a estrutura oferece suporte uma extensão, ele deve definir o cabeçalho para a resposta do lado do servidor de necessários `Sec-WebSocket-Extensions` handshake para a extensão.

O URL tem de ser utilizado como-é para o estabelecimento de accept socket, mas contém os seguintes parâmetros:

| Parâmetro      | Necessário | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro tem de ser `sb-hc-action=accept`
| `{path}`       | Sim      | (veja o parágrafo a seguir)
| `sb-hc-id`     | Não       | Consulte a descrição anterior dos **id**.

`{path}` é o caminho de espaço de nomes com codificação URL da ligação híbrida pré-configurada em que pretende registar este serviço de escuta. Esta expressão é acrescentada para o fixo `$hc/` parte do caminho.

O `path` expressão pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta que se segue o nome registado após uma barra de separar.
Isto permite ao cliente de remetente passar os argumentos de distribuição para o serviço de escuta aceitar quando não é possível incluir cabeçalhos HTTP. A expectativa é que a estrutura de serviço de escuta analisa a parte de caminho fixo e o nome registado do caminho e faz o resto, possivelmente, sem nenhum argumento de cadeia de caracteres de consulta o prefixo `sb-`, disponível para o aplicativo para decidir para aceitar ou a ligação.

Para obter mais informações, consulte a secção "Protocolo de remetente".

Se houver um erro, o serviço pode responder a da seguinte forma:

| Código | Erro          | Descrição
| ---- | -------------- | -----------------------------------
| 403  | Proibido      | O URL não é válido.
| 500  | Erro Interno | Ocorreu um erro no serviço

 Depois da ligação for estabelecida, o servidor encerra o WebSocket quando o remetente WebSocket é encerrada para baixo, ou com os seguintes Estados:

| Estado de WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente encerra a conexão.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização é violada. |
| 1011      | Ocorreu um erro no serviço.                                            |

##### <a name="rejecting-the-socket"></a>Rejeitar o socket

 Rejeitar o socket depois inspecionar o `accept` mensagem requer um handshake semelhante para que o código de estado e a descrição de estado ao comunicar o motivo para a rejeição pode fluir de volta para o remetente.

 A escolha de design do protocolo é usar um handshake WebSocket (que foi concebido para terminar num Estado de erro definido), para que as implementações de cliente do serviço de escuta pode continuar a confiar num cliente do WebSocket e não é necessário empregar adicionais, bare cliente HTTP.

 Para rejeitar o socket, o cliente usa o endereço de URI do `accept` da mensagem e acrescenta dois parâmetros de cadeia de caracteres de consulta, da seguinte forma:

| Param                   | Necessário | Descrição                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Sim      | Código de estado HTTP numérico.                |
| sb-hc-statusDescription | Sim      | Motivo legível humano na base da rejeição. |

O URI resultante, em seguida, é utilizado para estabelecer uma conexão WebSocket.

Quando concluir corretamente, este handshake intencionalmente ocorre uma falha com um código de erro HTTP 410, uma vez que não existem WebSocket foi estabelecida. Se algo der errado, os códigos seguintes descrevem o erro:

| Código | Erro          | Descrição                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Proibido      | O URL não é válido.                |
| 500  | Erro Interno | Ocorreu um erro no serviço. |

#### <a name="request-message"></a>Mensagem de pedido

O `request` mensagem é enviada pelo serviço para o serviço de escuta através do canal de controlo. A mesma mensagem também é enviada pelo encontro WebSocket depois de estabelecido.

O `request` consiste em duas partes: um frame(s) de corpo de cabeçalho e o binário.
Se não houver nenhum corpo, os quadros de corpo são omitidos. O indicador para se estiver presente um corpo é o valor de booleano `body` propriedade na mensagem do pedido.

Para um pedido com um corpo de pedido, a estrutura pode ser assim:

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

O serviço de escuta tem de processar o corpo do pedido dividir por vários quadros binários a receber (consulte [fragmentos de WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
O pedido termina quando um quadro binário com o conjunto de sinalizador FIN foi recebido.

Para um pedido sem um corpo, existe apenas um quadro de texto.

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

O conteúdo JSON para `request` é o seguinte:

* **endereço** -cadeia URI. Este é o endereço de encontro a utilizar para este pedido. Se a solicitação de entrada é maior do que 64 kB, o restante desta mensagem seja deixado em branco e o cliente tem de iniciar um handshake de rendezvous equivalente para o `accept` operação descrito abaixo. O serviço irá, em seguida, colocar o completa `request` no socket Web estabelecido. Se a resposta é obrigada a exceder os 64 kB, o serviço de escuta tem também iniciar um handshake de rendezvous e, em seguida, transferir a resposta ao longo de socket Web estabelecido.
* **ID** – cadeia de caracteres. O identificador exclusivo para este pedido.
* **requestHeaders** – este objeto contém todos os cabeçalhos HTTP que tiverem sido fornecidos para o ponto final pelo remetente, com exceção das informações de autorização conforme explicado [acima](#request-operation)e cabeçalhos estritamente relacionados com o ligação com o gateway. Especificamente, todos os cabeçalhos definidos ou reservado em [RFC7230](https://tools.ietf.org/html/rfc7230), exceto `Via`, são removidos e não são reencaminhados:

  * `Connection` (RFC7230, secção 6.1)
  * `Content-Length`  (RFC7230, secção 3.3.2)
  * `Host`  (RFC7230, secção 5.4)
  * `TE`  (RFC7230, secção 4.3)
  * `Trailer`  (RFC7230, secção 4.4)
  * `Transfer-Encoding`  (RFC7230, secção 3.3.1)
  * `Upgrade` (RFC7230, secção 6.7)
  * `Close`  (RFC7230, secção 8.1)

* **requestTarget** – cadeia de caracteres. Esta propriedade contém a ["Destino do pedido" (RFC7230, secção 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) do pedido. Isto inclui a parte de cadeia de caracteres de consulta, que é despida de todos os `sb-hc-` parâmetros o prefixo.
* **método** -cadeia de caracteres. Este é o método do pedido, por [RFC7231, secção 4](https://tools.ietf.org/html/rfc7231#section-4). O `CONNECT` não pode ser utilizado o método.
* **corpo** – booleano. Indica se o quadro de binários de corpo de um ou mais segue.

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

O destinatário tem de responder. Falhas repetidas para responder a pedidos enquanto mantém a ligação poderão resultar no ouvinte da introdução bloqueado.

As respostas podem ser enviadas por qualquer ordem, mas cada pedido deve ser respondido em 60 segundos ou a entrega será considerada como tendo falhou. O prazo de 60 segundos é contabilizado até o `response` quadro foi recebido pelo serviço. Uma resposta em curso com vários quadros binários não pode se tornar inativa durante mais de 60 segundos ou é encerrado.

Se a solicitação é recebida através do canal de controlo, ou deve ser enviada a resposta do canal de controlo de onde o pedido foi recebido ou têm de ser enviado através de um canal de encontro.

A resposta é um objeto JSON com o nome "resposta". As regras de conteúdo do corpo de tratamento são exatamente como com o `request` da mensagem e com base no `body` propriedade.

* **requestId** – cadeia de caracteres. É NECESSÁRIO. O `id` valor de propriedade do `request` mensagem a ser emitida.
* **statusCode** – número. É NECESSÁRIO. código de estado HTTP numérico que indica o resultado da notificação. Todos os códigos de estado de [RFC7231, secção 6](https://tools.ietf.org/html/rfc7231#section-6) têm permissão, exceto para [502 "Gateway inválido"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e [504 "tempo limite do Gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **Descriçãoestado** -cadeia de caracteres. OPCIONAL. Frase de razão do código de estado HTTP por [RFC7230, secção 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – cabeçalhos de HTTP para ser definida numa resposta HTTP externa.
  Tal como acontece com o `request`, RFC7230 cabeçalhos definidos não pode ser utilizados.
* **corpo** – booleano. Indica se o corpo binário frame(s) follow(s).

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

##### <a name="responding-via-rendezvous"></a>Responder através de rendezvous

Para as respostas que excedam 64 kB, a resposta tem de ser entregue num soquete de encontro. Também, se o pedido exceder os 64 kB e o `request` contém apenas o campo de endereço, é necessário estabelecer um soquete de encontro para obter o `request`. Assim que tiver sido estabelecido um soquete de rendezvous, as respostas para o respetivo cliente e as solicitações subseqüentes desse cliente respectivos tem de ser entregue através de socket de rendezvous enquanto ele persiste.

O `address` URL no `request` tem de ser utilizada como-é para estabelecer o socket de rendezvous, mas contém os seguintes parâmetros:

| Parâmetro      | Necessário | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro tem de ser `sb-hc-action=request`

Se houver um erro, o serviço pode responder a da seguinte forma:

| Código | Erro           | Descrição
| ---- | --------------- | -----------------------------------
| 400  | Pedido Inválido | Ação não reconhecida ou URL não é válido.
| 403  | Proibido       | O URL tiver expirado.
| 500  | Erro Interno  | Ocorreu um erro no serviço

 Depois da ligação for estabelecida, o servidor encerra o WebSocket quando socket HTTP do cliente é desligado ou com os seguintes Estados:

| Estado de WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente encerra a conexão.                                    |
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização é violada. |
| 1011      | Ocorreu um erro no serviço.                                            |


#### <a name="listener-token-renewal"></a>Renovação de token do serviço de escuta

Quando o token do serviço de escuta está prestes a expirar, ele pode substituí-lo, enviando uma mensagem de quadros de texto para o serviço através do canal de controlo estabelecida. A mensagem contém um objeto JSON chamado `renewToken`, que define a seguinte propriedade neste momento:

* **token** – um token de acesso de partilhado do Service Bus válido, com codificação URL para o espaço de nomes ou a ligação híbrida que confere o **escutar** certo.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se a validação de token falhar, o acesso é negado e o serviço em nuvem fecha o canal de controlo WebSocket com um erro. Caso contrário, não existe nenhuma resposta.

| Estado de WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | O token de segurança expirou, portanto, a política de autorização é violada. |

### <a name="web-socket-connect-protocol"></a>Protocolo de ligação de Web Socket

O protocolo de remetente é efetivamente idêntico à forma como um serviço de escuta é estabelecido.
O objetivo é o máximo de transparência para o ponto-a-ponto WebSocket. O endereço para ligar ao é igual para o serviço de escuta, mas a "ação" é diferente e o token tem uma permissão diferente:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

O _espaço de nomes-address_ é o nome de domínio completamente qualificado do espaço de nomes de reencaminhamento do Azure que aloja a ligação híbrida, normalmente do formulário `{myname}.servicebus.windows.net`.

O pedido pode conter arbitrários cabeçalhos HTTP extra, incluindo definidos na aplicação. Todos os cabeçalhos fornecidos fluir para o serviço de escuta e podem ser encontrados no `connectHeader` objeto do **aceitar** mensagem de controlo.

As opções de parâmetros de cadeia de caracteres de consulta são os seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sim       | Para a função de remetente, o parâmetro tem de ser `sb-hc-action=connect`.
| `{path}`       | Sim       | (veja o parágrafo a seguir)
| `sb-hc-token`  | Sim\*     | O serviço de escuta tem de fornecer um válido, com codificação URL Shared Access tokens do Service Bus para o espaço de nomes ou a ligação híbrida que confere a **enviar** certo.
| `sb-hc-id`     | Não        | Um ID opcional que permite o rastreamento de diagnóstico de ponto-a-ponto e fica disponível para o serviço de escuta durante o handshake de aceitar.

 O `{path}` é o caminho de espaço de nomes com codificação URL da ligação híbrida pré-configurada em que pretende registar este serviço de escuta. O `path` expressão pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta para se comunicar ainda mais. Se a ligação híbrida está registrada no caminho `hyco`, o `path` expressão pode ser `hyco/suffix?param=value&...` seguido os parâmetros de cadeia de caracteres de consulta definidos aqui. Uma expressão completa, em seguida, pode ser o seguinte:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

O `path` expressão é transmitida para o serviço de escuta no endereço de URI na mensagem de controlo "aceitar".

Se a conexão WebSocket falhar devido ao caminho de ligação híbrida não registado, um token inválido ou está em falta ou algum outro erro, os comentários de erro é fornecido com o modelo de comentários de status HTTP 1.1 regular. A descrição do Estado contém um erro ID que pode ser comunicada de controlo para o pessoal de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não Encontrado      | O caminho de ligação híbrida é inválido ou o URL de base tem um formato incorreto.
| 401  | Não autorizado   | O token de segurança está em falta ou tem um formato incorreto ou é inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno | Ocorreu um erro no serviço.

Se a conexão WebSocket é intencionalmente encerrada pelo serviço depois que ele foi inicialmente configurado, o motivo para se o fizer, é comunicado com um código de erro de protocolo WebSocket adequado juntamente com uma mensagem de erro descritivo que também inclui um ID de controlo .

| Estado de WS | Descrição
| --------- | ------------------------------------------------------------------------------- 
| 1000      | O serviço de escuta é encerrado por socket.
| 1001      | O caminho de ligação híbrida foi eliminado ou desativado.
| 1008      | O token de segurança expirou, portanto, a política de autorização é violada.
| 1011      | Ocorreu um erro no serviço.

### <a name="http-request-protocol"></a>Protocolo de pedido HTTP

O protocolo de pedido HTTP permite que os pedidos HTTP arbitrários, exceto as atualizações de protocolo.
Pedidos de HTTP são apontados no endereço de tempo de execução regular da entidade, sem o vpony $hc que é utilizado para ligações híbridas de clientes WebSocket.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

O _espaço de nomes-address_ é o nome de domínio completamente qualificado do espaço de nomes de reencaminhamento do Azure que aloja a ligação híbrida, normalmente do formulário `{myname}.servicebus.windows.net`.

O pedido pode conter arbitrários cabeçalhos HTTP extra, incluindo definidos na aplicação. Fornecido todos os cabeçalhos, exceto aquelas definidas diretamente no RFC7230 (consulte [mensagem de solicitação](#Request message)) fluir para o serviço de escuta e podem ser encontradas no `requestHeader` objeto do **pedido** mensagem.

As opções de parâmetros de cadeia de caracteres de consulta são os seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sim\*     | O serviço de escuta tem de fornecer um válido, com codificação URL Shared Access tokens do Service Bus para o espaço de nomes ou a ligação híbrida que confere a **enviar** certo.

O token também pode ser executado em ambos os `ServiceBusAuthorization` ou `Authorization` cabeçalho de HTTP. O token pode ser omitido se a ligação híbrida estiver configurada para permitir pedidos anónimos.

Uma vez que o serviço de forma eficaz age como um proxy, mesmo que não como um proxy HTTP true, ou adiciona uma `Via` cabeçalho ou annotates existente `Via` cabeçalho em conformidade com [RFC7230, secção 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
O serviço adiciona o nome de anfitrião do espaço de nomes do reencaminhamento para `Via`.

| Código | Mensagem  | Descrição                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | O pedido tiver sido manipulado por, pelo menos, um serviço de escuta.  |
| 202  | Aceite | O pedido foi aceite por, pelo menos, um serviço de escuta. |

Se houver um erro, o serviço pode responder a da seguinte forma. Se a resposta tem origem a partir do serviço ou a partir do serviço de escuta pode ser identificado por meio da presença do `Via` cabeçalho. Se o cabeçalho estiver presente, a resposta é da escuta.

| Código | Erro           | Descrição
| ---- | --------------- |--------- |
| 404  | Não Encontrado       | O caminho de ligação híbrida é inválido ou o URL de base tem um formato incorreto.
| 401  | Não autorizado    | O token de segurança está em falta ou tem um formato incorreto ou é inválido.
| 403  | Proibido       | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro Interno  | Ocorreu um erro no serviço.
| 503  | Gateway incorrecto     | O pedido não poderia ser encaminhado para qualquer serviço de escuta.
| 504  | Tempo Limite do Gateway | O pedido foi encaminhado para um serviço de escuta, mas o serviço de escuta não reconhecer recibo no tempo necessário.

## <a name="next-steps"></a>Passos Seguintes

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)
