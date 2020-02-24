---
title: Guia do protocolo Conexões Híbridas de retransmissão do Azure | Microsoft Docs
description: Este artigo descreve as interações do lado do cliente com o Conexões Híbridas retransmissão para conectar clientes em funções de emissor e remetente.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514957"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de Conexões Híbridas de retransmissão do Azure

A retransmissão do Azure é um dos principais pilares de capacidade da plataforma do barramento de serviço do Azure. O novo _conexões híbridas_ recurso de retransmissão é uma evolução segura e de protocolo aberto com base em http e WebSockets. Ele substitui o antigo recurso de _Serviços BizTalk_ , igualmente nomeado, criado em uma base de protocolo proprietário. A integração do Conexões Híbridas ao Azure App Services continuará a funcionar no estado em que se encontram.

O Conexões Híbridas habilita a comunicação de fluxo bidirecional, binária e fluxo de datagrama simples entre dois aplicativos em rede. Uma ou ambas as partes podem residir atrás de NATs ou de firewalls.

Este artigo descreve as interações do lado do cliente com o Conexões Híbridas retransmissão para conectar clientes em funções de emissor e remetente. Ele também descreve como os ouvintes aceitam novas conexões e solicitações.

## <a name="interaction-model"></a>Modelo de interação

A retransmissão de Conexões Híbridas conecta duas partes fornecendo um ponto de reunião na nuvem do Azure que as partes podem descobrir e se conectar da perspectiva da sua própria rede. O ponto de reunião é chamado de "conexão híbrida" nesta e em outras documentações, nas APIs e também na portal do Azure. O ponto de extremidade do serviço de Conexões Híbridas é chamado de "serviço" para o restante deste artigo.

O serviço permite retransmitir conexões de soquete da Web e solicitações e respostas HTTP (S).

O modelo de interação se baseia no nomenclatura estabelecido por muitas outras APIs de rede. Há um ouvinte que indica primeiro a preparação para lidar com as conexões de entrada e, subsequentemente, aceita-as à medida que elas chegam. No outro lado, um cliente se conecta ao ouvinte, esperando que a conexão seja aceita para estabelecer um caminho de comunicação bidirecional. "Conectar", "escutar" e "aceitar" são os mesmos termos que você encontra na maioria das APIs de soquete.

Qualquer modelo de comunicação retransmitido tem qualquer parte fazendo conexões de saída para um ponto de extremidade de serviço. Isso faz com que o "ouvinte" também seja um "cliente" em uso coloquial usado e também pode causar outras sobrecargas de terminologia. A terminologia precisa, portanto, usada para Conexões Híbridas é a seguinte:

Os programas em ambos os lados de uma conexão são chamados de "clientes", pois eles são clientes para o serviço. O cliente que aguarda e aceita conexões é o "ouvinte" ou diz estar na "função de ouvinte". O cliente que inicia uma nova conexão em direção a um ouvinte por meio do serviço é chamado de "remetente" ou está na "função de remetente".

### <a name="listener-interactions"></a>Interações de ouvinte

O ouvinte tem cinco interações com o serviço; todos os detalhes de transmissão são descritos posteriormente neste artigo na seção de referência.

As mensagens de escuta, aceitação e solicitação são recebidas do serviço. As operações de renovação e ping são enviadas pelo ouvinte.

#### <a name="listen-message"></a>Mensagem de escuta

Para indicar a prontidão para o serviço que um ouvinte está pronto para aceitar conexões, ele cria uma conexão WebSocket de saída. O handshake de conexão carrega o nome de uma conexão híbrida configurada no namespace de retransmissão e um token de segurança que confere o direito "escutar" nesse nome.

Quando o WebSocket é aceito pelo serviço, o registro é concluído e o WebSocket estabelecido é mantido ativo como o "canal de controle" para habilitar todas as interações subsequentes. O serviço permite até 25 ouvintes simultâneos para uma conexão híbrida. A cota para AppHooks deve ser determinada.

Por Conexões Híbridas, se houver dois ou mais ouvintes ativos, as conexões de entrada serão balanceadas entre eles em ordem aleatória; a distribuição justa é tentada com o melhor esforço.

#### <a name="accept-message"></a>Aceitar mensagem

Quando um remetente abre uma nova conexão no serviço, o serviço escolhe e notifica um dos ouvintes ativos na conexão híbrida. Essa notificação é enviada para o ouvinte pelo canal de controle aberto como uma mensagem JSON. A mensagem contém a URL do ponto de extremidade WebSocket ao qual o ouvinte deve se conectar para aceitar a conexão.

A URL pode e deve ser usada diretamente pelo ouvinte sem nenhum trabalho extra.
As informações codificadas só são válidas por um curto período de tempo, essencialmente por enquanto o remetente está disposto a esperar que a conexão seja estabelecida de ponta a ponta. O máximo a ser assumido é de 30 segundos. A URL só pode ser usada para uma tentativa de conexão bem-sucedida. Assim que a conexão WebSocket com a URL de reunião for estabelecida, todas as atividades adicionais nesse WebSocket serão retransmitidas de e para o remetente. Isso acontece sem qualquer intervenção ou interpretação pelo serviço.

### <a name="request-message"></a>Mensagem de solicitação

Além das conexões WebSocket, o ouvinte também pode receber quadros de solicitação HTTP de um remetente, se esse recurso estiver explicitamente habilitado na conexão híbrida.

Os ouvintes que se anexam ao Conexões Híbridas com suporte a HTTP devem lidar com o gesto de `request`. Um ouvinte que não manipula `request` e, portanto, causa erros de tempo limite repetidos durante a conexão pode estar na lista de bloqueios pelo serviço no futuro.

Os metadados do cabeçalho do quadro HTTP são convertidos em JSON para manipulação mais simples pela estrutura do ouvinte, também porque as bibliotecas de análise de cabeçalho HTTP são mais raras que os analisadores JSON. Os metadados HTTP que são relevantes apenas para a relação entre o remetente e o gateway HTTP de retransmissão, incluindo informações de autorização, não são encaminhados. Os corpos de solicitação HTTP são transferidos de forma transparente como quadros de WebSocket binários.

O ouvinte pode responder a solicitações HTTP usando um gesto de resposta equivalente.

O fluxo de solicitação/resposta usa o canal de controle por padrão, mas pode ser "atualizado" para um WebSocket de reunião distinta sempre que necessário. Conexões WebSocket distintas aumentam a taxa de transferência para cada conversa do cliente, mas sobrecarregam o ouvinte com mais conexões que precisam ser tratadas, o que pode não ser capaz de obter clientes leves.

No canal de controle, os corpos de solicitação e resposta são limitados a no máximo 64 kB de tamanho. Os metadados do cabeçalho HTTP são limitados a um total de 32 kB. Se a solicitação ou a resposta exceder esse limite, o ouvinte deverá ser atualizado para um WebSocket de reunião usando um gesto equivalente para manipular a [aceitação](#accept-message).

Para solicitações, o serviço decide se as solicitações devem ser roteadas no canal de controle. Isso inclui, mas pode não estar limitado a casos em que uma solicitação exceda 64 kB (cabeçalhos mais corpo), ou se a solicitação for enviada com [codificação de transferência "em bloco"](https://tools.ietf.org/html/rfc7230#section-4.1) e o serviço tiver razão para esperar que a solicitação exceda 64 KB ou a leitura da solicitação não seja instantânea. Se o serviço optar por entregar a solicitação pela reunião, ele só passará o endereço de reunião para o ouvinte.
Em seguida, o ouvinte deve estabelecer o WebSocket de reunião e o serviço imediatamente entrega a solicitação completa, incluindo corpos sobre o WebSocket de reunião. A resposta também deve usar o WebSocket de reunião.

Para solicitações que chegam pelo canal de controle, o ouvinte decide se deve responder pelo canal de controle ou por meio de reunião. O serviço deve incluir um endereço de reunião com cada solicitação roteada pelo canal de controle. Esse endereço só é válido para atualização da solicitação atual.

Se o ouvinte optar por atualizar, ele se conectará e entregará imediatamente a resposta sobre o soquete de reunião estabelecido.

Depois que o WebSocket de reunião tiver sido estabelecido, o ouvinte deverá mantê-lo para manipulação adicional de solicitações e respostas do mesmo cliente. O serviço manterá o WebSocket enquanto a conexão de soquete HTTPS com o remetente persistir e roteará todas as solicitações subsequentes desse remetente sobre o WebSocket mantido. Se o ouvinte optar por descartar o WebSocket de reunião de seu lado, o serviço também removerá a conexão com o remetente, independentemente de uma solicitação subsequente já estar em andamento.

#### <a name="renew-operation"></a>Renovar operação

O token de segurança que deve ser usado para registrar o ouvinte e manter o canal de controle pode expirar enquanto o ouvinte está ativo. A expiração do token não afeta as conexões contínuas, mas faz com que o canal de controle seja Descartado pelo serviço em ou logo após o momento da expiração. A operação "renovar" é uma mensagem JSON que o ouvinte pode enviar para substituir o token associado ao canal de controle, para que o canal de controle possa ser mantido por períodos estendidos.

#### <a name="ping-operation"></a>Operação de ping

Se o canal de controle permanecer ocioso por um longo tempo, intermediários no caminho, como balanceadores de carga ou NATs, poderão descartar a conexão TCP. A operação de "ping" evita isso enviando uma pequena quantidade de dados no canal que lembra a todos na rota de rede que a conexão deve estar ativa e também serve como um teste "ao vivo" para o ouvinte. Se o ping falhar, o canal de controle deverá ser considerado inutilizável e o ouvinte deverá se reconectar.

### <a name="sender-interaction"></a>Interação do remetente

O remetente tem duas interações com o serviço: ele conecta um soquete da Web ou envia solicitações via HTTPS. As solicitações não podem ser enviadas por meio de um soquete da Web da função de remetente.

#### <a name="connect-operation"></a>Operação de conexão

A operação "conectar" abre um WebSocket no serviço, fornecendo o nome da conexão híbrida e (opcionalmente, mas exigido por padrão) um token de segurança que faz referência à permissão "enviar" na cadeia de caracteres de consulta. Em seguida, o serviço interage com o ouvinte da maneira descrita anteriormente e o ouvinte cria uma conexão de reunião que é unida a esse WebSocket. Depois que o WebSocket tiver sido aceito, todas as outras interações nesse WebSocket serão com um ouvinte conectado.

#### <a name="request-operation"></a>Operação de solicitação

Para Conexões Híbridas para o qual o recurso foi habilitado, o remetente pode enviar solicitações HTTP amplamente irrestritas para os ouvintes.

Exceto para um token de acesso de retransmissão que é inserido na cadeia de caracteres de consulta ou em um cabeçalho HTTP da solicitação, a retransmissão é totalmente transparente para todas as operações HTTP no endereço de retransmissão e todos os sufixos do caminho do endereço de retransmissão, deixando o ouvinte totalmente no controle de autorização de ponta a ponta e até mesmo recursos de extensão HTTP como [CORS](https://www.w3.org/TR/cors/).

A autorização do remetente com o ponto de extremidade de retransmissão é ativada por padrão, mas é opcional. O proprietário da conexão híbrida pode optar por permitir remetentes anônimos. O serviço interceptará, inspecionará e removerá as informações de autorização da seguinte maneira:

1. Se a cadeia de caracteres de consulta contiver uma expressão `sb-hc-token`, a expressão sempre será removida da cadeia de caracteres de consulta. Ele será avaliado se a autorização de retransmissão estiver ativada.
2. Se os cabeçalhos de solicitação contiverem um cabeçalho `ServiceBusAuthorization`, a expressão de cabeçalho sempre será removida da coleção de cabeçalho.
   Ele será avaliado se a autorização de retransmissão estiver ativada.
3. Somente se a autorização de retransmissão estiver ativada e se os cabeçalhos de solicitação contiverem um cabeçalho `Authorization` e nenhuma das expressões anteriores estiver presente, o cabeçalho será avaliado e removido. Caso contrário, o `Authorization`sempre será passado no estado em que se encontra.

Se não houver um ouvinte ativo, o serviço retornará um código de erro 502 de "gateway inadequado". Se o serviço não parecer manipular a solicitação, o serviço retornará um 504 "tempo limite do gateway" após 60 segundos.

### <a name="interaction-summary"></a>Resumo da interação

O resultado desse modelo de interação é que o cliente remetente sai do handshake com um WebSocket "limpo", que está conectado a um ouvinte e que não precisa de nenhum preâmbulo ou preparação adicional. Esse modelo permite praticamente qualquer implementação de cliente WebSocket existente para aproveitar prontamente o serviço de Conexões Híbridas fornecendo uma URL construída corretamente em sua camada de cliente WebSocket.

O WebSocket de conexão de reunião que o ouvinte obtém por meio da interação de aceitação também é limpo e pode ser entregue a qualquer implementação de servidor WebSocket existente com alguma abstração extra mínima que diferencie entre as operações "Accept" no os ouvintes de rede local de sua estrutura e as operações de "aceitação" remota Conexões Híbridas.

O modelo de solicitação/resposta HTTP fornece ao remetente uma área de superfície de protocolo HTTP totalmente irrestrita com uma camada de autorização opcional. O ouvinte Obtém uma seção de cabeçalho de solicitação HTTP pré-configurada que pode ser reativada para uma solicitação HTTP downstream ou manipulada como está, com quadros binários que realizam corpos HTTP. As respostas usam o mesmo formato. Interações com menos de 64 KB de corpo de solicitação e resposta podem ser manipuladas em um único soquete da Web que é compartilhado para todos os remetentes. As solicitações e respostas maiores podem ser manipuladas usando o modelo de reunião.

## <a name="protocol-reference"></a>Referência de protocolo

Esta seção descreve os detalhes das interações de protocolo descritas anteriormente.

Todas as conexões WebSocket são feitas na porta 443 como uma atualização do HTTPS 1,1, que é normalmente abstraida por alguma API ou estrutura de WebSocket. A descrição aqui é mantida em uma implementação neutra, sem sugerir uma estrutura específica.

### <a name="listener-protocol"></a>Protocolo de escuta

O protocolo de escuta consiste em dois gestos de conexão e três operações de mensagem.

#### <a name="listener-control-channel-connection"></a>Conexão de canal de controle de ouvinte

O canal de controle é aberto com a criação de uma conexão WebSocket para:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

O `namespace-address` é o nome de domínio totalmente qualificado do namespace de retransmissão do Azure que hospeda a conexão híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

As opções de parâmetro de cadeia de caracteres de consulta são as seguintes.

| Parâmetro        | Obrigatório | Descrição
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sim      | Para a função de ouvinte, o parâmetro deve ser **SB-HC-Action = escutar**
| `{path}`         | Sim      | O caminho do namespace codificado por URL da conexão híbrida pré-configurada na qual registrar esse ouvinte. Essa expressão é anexada à parte do caminho de `$hc/` fixo.
| `sb-hc-token`    | Sim\*    | O ouvinte deve fornecer um token de acesso compartilhado de barramento de serviço com codificação de URL válido para o namespace ou conexão híbrida que confere o direito de **escuta** .
| `sb-hc-id`       | Não       | Essa ID opcional fornecida pelo cliente habilita o rastreamento de diagnóstico de ponta a ponta.

Se a conexão WebSocket falhar porque o caminho de conexão híbrida não está sendo registrado, ou um token inválido ou ausente ou algum outro erro, os comentários de erro são fornecidos usando o modelo de comentários de status HTTP 1,1 regular. A descrição do status contém uma ID de rastreamento de erro que pode ser comunicada à equipe de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não foi encontrado      | O caminho da conexão híbrida é inválido ou a URL base está malformada.
| 401  | Não autorizado   | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho para esta ação.
| 500  | Erro interno | Algo deu errado no serviço.

Se a conexão WebSocket for desligada intencionalmente pelo serviço depois que ela tiver sido inicialmente configurada, o motivo para isso é comunicado usando um código de erro de protocolo WebSocket apropriado junto com uma mensagem de erro descritiva que também inclui uma ID de rastreamento. O serviço não desligará o canal de controle sem encontrar uma condição de erro. Qualquer desligamento normal é controlado pelo cliente.

| Status do WS | Descrição
| --------- | -------------------------------------------------------------------------------
| 1001      | O caminho de conexão híbrida foi excluído ou desabilitado.
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada.
| 1011      | Algo deu errado no serviço.

#### <a name="accept-handshake"></a>Aceitar handshake

A notificação "Accept" é enviada pelo serviço para o ouvinte pelo canal de controle estabelecido anteriormente como uma mensagem JSON em um quadro de texto WebSocket. Não há nenhuma resposta para esta mensagem.

A mensagem contém um objeto JSON chamado "Accept", que define as seguintes propriedades neste momento:

* **endereço** – a cadeia de caracteres de URL a ser usada para estabelecer o WebSocket para o serviço para aceitar uma conexão de entrada.
* **ID** – o identificador exclusivo para esta conexão. Se a ID tiver sido fornecida pelo cliente remetente, será o valor fornecido pelo remetente, caso contrário, será um valor gerado pelo sistema.
* **connectHeaders** – todos os cabeçalhos HTTP que foram fornecidos ao ponto de extremidade de retransmissão pelo remetente, que também inclui os cabeçalhos s-WebSocket-Protocol e s-WebSocket-Extensions.

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

A URL de endereço fornecida na mensagem JSON é usada pelo ouvinte para estabelecer o WebSocket para aceitar ou rejeitar o soquete do remetente.

##### <a name="accepting-the-socket"></a>Aceitando o soquete

Para aceitar, o ouvinte estabelece uma conexão WebSocket com o endereço fornecido.

Se a mensagem "Accept" transportar um cabeçalho `Sec-WebSocket-Protocol`, espera-se que o ouvinte aceite apenas o WebSocket se ele der suporte a esse protocolo. Além disso, ele define o cabeçalho conforme o WebSocket é estabelecido.

O mesmo se aplica ao cabeçalho `Sec-WebSocket-Extensions`. Se a estrutura oferecer suporte a uma extensão, ela deverá definir o cabeçalho como a resposta do lado do servidor do handshake de `Sec-WebSocket-Extensions` necessário para a extensão.

A URL deve ser usada no estado em que se encontra para estabelecer o soquete de aceitação, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro deve ser `sb-hc-action=accept`
| `{path}`       | Sim      | (consulte o parágrafo a seguir)
| `sb-hc-id`     | Não       | Consulte a descrição anterior da **ID**.

`{path}` é o caminho do namespace com codificação de URL da conexão híbrida pré-configurada na qual registrar esse ouvinte. Essa expressão é anexada à parte do caminho de `$hc/` fixo.

A expressão `path` pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta que segue o nome registrado após uma barra de separação.
Isso permite que o cliente remetente passe argumentos de expedição para o ouvinte de aceitação quando não é possível incluir cabeçalhos HTTP. A expectativa é que a estrutura do ouvinte analise a parte do caminho fixo e o nome registrado do caminho e faça o restante, possivelmente sem nenhum argumento de cadeia de caracteres de consulta prefixado por `sb-`, disponível para o aplicativo para decidir se deseja aceitar a conexão.

Para obter mais informações, consulte a seguinte seção "protocolo do remetente".

Se houver um erro, o serviço poderá responder da seguinte maneira:

| Código | Erro          | Descrição
| ---- | -------------- | -----------------------------------
| 403  | Proibido      | O URL não é válido.
| 500  | Erro interno | Algo deu errado no serviço

 Depois que a conexão é estabelecida, o servidor desliga o WebSocket quando o WebSocket do remetente é desligado ou com o seguinte status:

| Status do WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a conexão.                                    |
| 1001      | O caminho de conexão híbrida foi excluído ou desabilitado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |
| 1011      | Algo deu errado no serviço.                                            |

##### <a name="rejecting-the-socket"></a>Rejeitando o soquete

 Rejeitar o Soquete depois de inspecionar a mensagem de `accept` requer um handshake semelhante para que o código de status e a descrição do status que se comunicam com o motivo da rejeição possam fluir de volta para o remetente.

 A escolha de design de protocolo aqui é usar um handshake WebSocket (que é projetado para terminar em um estado de erro definido) para que as implementações do ouvinte do cliente possam continuar a depender de um cliente WebSocket e não precisem empregar um cliente HTTP simples e extra.

 Para rejeitar o soquete, o cliente usa o URI de endereço da mensagem de `accept` e acrescenta dois parâmetros de cadeia de caracteres de consulta a ele, da seguinte maneira:

| Param                   | Obrigatório | Descrição                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Sim      | Código de status HTTP numérico.                |
| sb-hc-statusDescription | Sim      | Motivo legível por humanos para a rejeição. |

O URI resultante é usado para estabelecer uma conexão WebSocket.

Ao concluir corretamente, esse handshake falha intencionalmente com um código de erro HTTP 410, pois nenhum WebSocket foi estabelecido. Se algo der errado, os códigos a seguir descrevem o erro:

| Código | Erro          | Descrição                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Proibido      | O URL não é válido.                |
| 500  | Erro interno | Algo deu errado no serviço. |

#### <a name="request-message"></a>Mensagem de solicitação

A mensagem de `request` é enviada pelo serviço para o ouvinte pelo canal de controle. A mesma mensagem também é enviada pelo WebSocket de reunião depois de estabelecida.

O `request` consiste em duas partes: um cabeçalho e quadros de corpo binários.
Se não houver corpo, os quadros do corpo serão omitidos. O indicador de se um corpo está presente é a propriedade booliana `body` na mensagem de solicitação.

Para uma solicitação com um corpo de solicitação, a estrutura pode ter a seguinte aparência:

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

O ouvinte deve lidar com o recebimento da divisão do corpo da solicitação em vários quadros binários (consulte [fragmentos do WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
A solicitação termina quando um quadro binário com o sinalizador de FIN definido foi recebido.

Para uma solicitação sem corpo, há apenas um quadro de texto.

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

* Cadeia de caracteres de URI de **endereço** . Este é o endereço de reunião a ser usado para esta solicitação. Se a solicitação de entrada for maior que 64 kB, o restante dessa mensagem será deixado vazio e o cliente deverá iniciar um handshake de reunião equivalente à `accept` operação descrita abaixo. Em seguida, o serviço colocará o `request` completo no soquete da Web estabelecido. Se for esperado que a resposta exceda 64 kB, o ouvinte também deverá iniciar um handshake de reunião e, em seguida, transferir a resposta sobre o soquete da Web estabelecido.
* **ID** – cadeia de caracteres. O identificador exclusivo para esta solicitação.
* **requestHeaders** – esse objeto contém todos os cabeçalhos HTTP que foram fornecidos ao ponto de extremidade pelo remetente, com exceção das informações de autorização, conforme explicado [acima](#request-operation), e os cabeçalhos que se referem estritamente à conexão com o gateway. Especificamente, todos os cabeçalhos definidos ou reservados em [RFC7230](https://tools.ietf.org/html/rfc7230), exceto `Via`, são removidos e não encaminhados:

  * `Connection` (RFC7230, seção 6,1)
  * `Content-Length` (RFC7230, seção 3.3.2)
  * `Host` (RFC7230, seção 5,4)
  * `TE` (RFC7230, seção 4,3)
  * `Trailer` (RFC7230, seção 4,4)
  * `Transfer-Encoding` (RFC7230, seção 3.3.1)
  * `Upgrade` (RFC7230, seção 6,7)
  * `Close` (RFC7230, seção 8,1)

* **requestTarget** – cadeia de caracteres. Essa propriedade contém o ["destino da solicitação" (RFC7230, seção 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) da solicitação. Isso inclui a parte da cadeia de caracteres de consulta, que é removida de todos os `sb-hc-` parâmetros prefixados.
* Cadeia de caracteres de **método** . Esse é o método da solicitação, por [RFC7231, seção 4](https://tools.ietf.org/html/rfc7231#section-4). O método `CONNECT` não deve ser usado.
* **Body** – booliano. Indica se um ou mais quadros de corpo binário são mostrados a seguir.

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

##### <a name="responding-to-requests"></a>Respondendo a solicitações

O receptor deve responder. A falha repetida de responder às solicitações enquanto mantém a conexão pode fazer com que o ouvinte fique na lista de bloqueios.

As respostas podem ser enviadas em qualquer ordem, mas cada solicitação deve ser respondida em até 60 segundos ou a entrega será relatada como tendo falhado. O prazo final de 60 segundos é contado até que o quadro de `response` tenha sido recebido pelo serviço. Uma resposta em andamento com vários quadros binários não pode ficar ociosa por mais de 60 segundos ou terminada.

Se a solicitação for recebida no canal de controle, a resposta deverá ser enviada no canal de controle de onde a solicitação foi recebida ou deve ser enviada por um canal de reunião.

A resposta é um objeto JSON chamado "Response". As regras para lidar com o conteúdo do corpo são exatamente semelhantes às `request` mensagem e com base na propriedade `body`.

* **requestId** – cadeia de caracteres. Necessário. O valor da propriedade de `id` da mensagem de `request` que está sendo respondida.
* **StatusCode** – número. Necessário. um código de status HTTP numérico que indica o resultado da notificação. Todos os códigos de status de [RFC7231, seção 6](https://tools.ietf.org/html/rfc7231#section-6) são permitidos, exceto por [502 "gateway inadequado"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e [504 "tempo limite de gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -cadeia de caracteres. Adicional. Status HTTP-frase do motivo do código por [RFC7230, seção 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – cabeçalhos HTTP a serem definidos em uma resposta http externa.
  Assim como no `request`, os cabeçalhos definidos RFC7230 não devem ser usados.
* **Body** – booliano. Indica se os quadros de corpo binário seguem (m).

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

##### <a name="responding-via-rendezvous"></a>Respondendo por meio da reunião

Para respostas que excedem 64 kB, a resposta deve ser entregue em um soquete de reunião. Além disso, se a solicitação exceder 64 kB e a `request` contiver apenas o campo endereço, um soquete de reunião deverá ser estabelecido para obter o `request`. Depois que um soquete de reunião tiver sido estabelecido, as respostas para o respectivo cliente e as solicitações subsequentes desse respectivo cliente deverão ser entregues pelo soquete de reunião enquanto ele persistir.

A URL de `address` no `request` deve ser usada no estado em que se encontra para estabelecer o soquete de reunião, mas contém os seguintes parâmetros:

| Parâmetro      | Obrigatório | Descrição
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sim      | Para aceitar um soquete, o parâmetro deve ser `sb-hc-action=request`

Se houver um erro, o serviço poderá responder da seguinte maneira:

| Código | Erro           | Descrição
| ---- | --------------- | -----------------------------------
| 400  | Solicitação inválida | Ação não reconhecida ou URL inválida.
| 403  | Proibido       | A URL expirou.
| 500  | Erro interno  | Algo deu errado no serviço

 Depois que a conexão é estabelecida, o servidor desliga o WebSocket quando o soquete HTTP do cliente é desligado ou com o seguinte status:

| Status do WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | O cliente remetente desliga a conexão.                                    |
| 1001      | O caminho de conexão híbrida foi excluído ou desabilitado.                        |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |
| 1011      | Algo deu errado no serviço.                                            |


#### <a name="listener-token-renewal"></a>Renovação de token de ouvinte

Quando o token do ouvinte está prestes a expirar, ele pode substituí-lo enviando uma mensagem de quadro de texto ao serviço por meio do canal de controle estabelecido. A mensagem contém um objeto JSON chamado `renewToken`, que define a seguinte propriedade neste momento:

* **token** – um token de acesso compartilhado de barramento de serviço de codificação de URL válido para o namespace ou conexão híbrida que confere o direito de **escuta** .

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se a validação do token falhar, o acesso será negado e o serviço de nuvem fechará o WebSocket do canal de controle com um erro. Caso contrário, não haverá resposta.

| Status do WS | Descrição                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada. |

### <a name="web-socket-connect-protocol"></a>Protocolo de conexão de soquete da Web

O protocolo do remetente é efetivamente idêntico ao modo como um ouvinte é estabelecido.
A meta é a transparência máxima do WebSocket de ponta a ponta. O endereço a ser conectado é o mesmo do ouvinte, mas a "ação" difere e o token precisa de uma permissão diferente:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

O _namespace-address_ é o nome de domínio totalmente qualificado do namespace de retransmissão do Azure que hospeda a conexão híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

A solicitação pode conter cabeçalhos HTTP adicionais arbitrários, incluindo aqueles definidos pelo aplicativo. Todos os cabeçalhos fornecidos fluem para o ouvinte e podem ser encontrados no objeto `connectHeader` da mensagem de controle **Accept** .

As opções de parâmetro de cadeia de caracteres de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sim       | Para a função de remetente, o parâmetro deve ser `sb-hc-action=connect`.
| `{path}`       | Sim       | (consulte o parágrafo a seguir)
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um token de acesso compartilhado de barramento de serviço com codificação de URL válido para o namespace ou conexão híbrida que confere o direito de **envio** .
| `sb-hc-id`     | Não        | Uma ID opcional que habilita o rastreamento de diagnóstico de ponta a ponta e é disponibilizada para o ouvinte durante o handshake de aceitação.

 O `{path}` é o caminho do namespace de URL codificada da conexão híbrida pré-configurada na qual registrar esse ouvinte. A expressão `path` pode ser estendida com um sufixo e uma expressão de cadeia de caracteres de consulta para se comunicar mais. Se a conexão híbrida estiver registrada no caminho `hyco`, a `path` expressão poderá ser `hyco/suffix?param=value&...` seguida pelos parâmetros de cadeia de caracteres de consulta definidos aqui. Uma expressão completa pode ser a seguinte:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

A expressão de `path` é passada para o ouvinte no URI de endereço contido na mensagem de controle "Accept".

Se a conexão WebSocket falhar devido ao caminho de conexão híbrida não estar registrado, um token inválido ou ausente ou algum outro erro, os comentários de erro serão fornecidos usando o modelo de comentários de status HTTP 1,1 regular. A descrição do status contém uma ID de rastreamento de erro que pode ser comunicada à equipe de suporte do Azure:

| Código | Erro          | Descrição
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Não foi encontrado      | O caminho da conexão híbrida é inválido ou a URL base está malformada.
| 401  | Não autorizado   | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido      | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro interno | Algo deu errado no serviço.

Se a conexão WebSocket for desligada intencionalmente pelo serviço depois que ela tiver sido inicialmente configurada, o motivo para isso é comunicado usando um código de erro de protocolo WebSocket apropriado junto com uma mensagem de erro descritiva que também inclui uma ID de rastreamento .

| Status do WS | Descrição
| --------- | ------------------------------------------------------------------------------- 
| 1000      | O ouvinte desligou o soquete.
| 1001      | O caminho de conexão híbrida foi excluído ou desabilitado.
| 1008      | O token de segurança expirou, portanto, a política de autorização foi violada.
| 1011      | Algo deu errado no serviço.

### <a name="http-request-protocol"></a>Protocolo de solicitação HTTP

O protocolo HTTP Request permite solicitações HTTP arbitrárias, exceto atualizações de protocolo.
As solicitações HTTP são apontadas para o endereço de tempo de execução regular da entidade, sem o $hc infixo que é usado para clientes do WebSocket de conexões híbridas.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

O _namespace-address_ é o nome de domínio totalmente qualificado do namespace de retransmissão do Azure que hospeda a conexão híbrida, normalmente no formato `{myname}.servicebus.windows.net`.

A solicitação pode conter cabeçalhos HTTP adicionais arbitrários, incluindo aqueles definidos pelo aplicativo. Todos os cabeçalhos fornecidos, exceto aqueles diretamente definidos no fluxo RFC7230 (consulte a [mensagem de solicitação](#Request message)) para o ouvinte e podem ser encontrados no objeto `requestHeader` da mensagem de **solicitação** .

As opções de parâmetro de cadeia de caracteres de consulta são as seguintes:

| Param          | Necessário? | Descrição
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sim\*     | O ouvinte deve fornecer um token de acesso compartilhado de barramento de serviço com codificação de URL válido para o namespace ou conexão híbrida que confere o direito de **envio** .

O token também pode ser executado no cabeçalho HTTP `ServiceBusAuthorization` ou `Authorization`. O token poderá ser omitido se a conexão híbrida estiver configurada para permitir solicitações anônimas.

Como o serviço age efetivamente como um proxy, mesmo que não seja como um proxy HTTP verdadeiro, ele adiciona um cabeçalho `Via` ou anota o cabeçalho de `Via` existente compatível com [RFC7230, seção 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
O serviço adiciona o nome de host do namespace de retransmissão para `Via`.

| Código | Mensagem  | Descrição                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A solicitação foi manipulada por pelo menos um ouvinte.  |
| 202  | Aceite | A solicitação foi aceita por pelo menos um ouvinte. |

Se houver um erro, o serviço poderá responder da seguinte maneira. Se a resposta é proveniente do serviço ou do ouvinte pode ser identificada por meio da presença do cabeçalho de `Via`. Se o cabeçalho estiver presente, a resposta será do ouvinte.

| Código | Erro           | Descrição
| ---- | --------------- |--------- |
| 404  | Não foi encontrado       | O caminho da conexão híbrida é inválido ou a URL base está malformada.
| 401  | Não autorizado    | O token de segurança está ausente ou malformado ou inválido.
| 403  | Proibido       | O token de segurança não é válido para este caminho e para esta ação.
| 500  | Erro interno  | Algo deu errado no serviço.
| 503  | Gateway incorrecto     | Não foi possível rotear a solicitação para nenhum ouvinte.
| 504  | Tempo limite do gateway | A solicitação foi roteada para um ouvinte, mas o ouvinte não confirmou o recebimento no tempo necessário.

## <a name="next-steps"></a>Passos seguintes

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Nó](relay-hybrid-connections-node-get-started.md)
