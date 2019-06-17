---
title: AMQP 1.0 no Guia do protocolo do Azure Service Bus e dos Hubs de eventos | Documentos da Microsoft
description: Guia de protocolo para expressões e a descrição do AMQP 1.0 no Azure Service Bus e dos Hubs de eventos
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c99f4491af8fe3e5f0f0ed7a264995ae3ec5911f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749449"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 no Guia do protocolo do Azure Service Bus e dos Hubs de eventos

1\.0 protocolo avançada de colocação em fila de mensagens é um protocolo de delimitação de quadros e transferência padronizado para a transferência assíncrona, segura e fiável de mensagens entre duas partes. É o principal protocolo de mensagens do Azure Service Bus e Event Hubs do Azure. Ambos os serviços também suportam HTTPS. O protocolo SBMP proprietário, que também é suportado está a ser descontinuado em favor de AMQP.

AMQP 1.0 é o resultado da colaboração da indústria que juntou-se de fornecedores de middleware, como a Microsoft e a Red Hat, com muitos usuários de middleware de mensagens, como JP Morgan perseguição que representa o setor de serviços financeiros. O Fórum de padronização técnico para as especificações de protocolo e a extensão AMQP é OASIS e ele obteve uma aprovação formal como um padrão internacional como ISO/IEC 19494.

## <a name="goals"></a>Objetivos

Este artigo resume os conceitos básicos do AMQP 1.0 mensagens especificação juntamente com um pequeno conjunto de especificações de extensão de rascunho que atualmente estão sendo finalizado no comitê técnico OASIS AMQP resumidamente e explica como o Azure Service Bus implementa e complementa essas especificações.

O objetivo é para qualquer desenvolvedor usando qualquer pilha de cliente AMQP 1.0 existente em qualquer plataforma para ser capaz de interagir com o Azure Service Bus através de AMQP 1.0.

Comuns pilhas AMQP 1.0 para fins gerais, como Apache Proton ou AMQP.NET Lite, já implementam todos os protocolos de núcleo AMQP 1.0. Esses gestos básicos, às vezes, são compactados com uma API de nível superior; Apache Proton oferece até mesmo duas, a API do Messenger imperativo e a API de Reactor reativo.

A seguinte descrição, partimos do pressuposto que a gestão de ligações, sessões e ligações AMQP e a manipulação de transferências de quadro e controlo de fluxo são manipulados pela pilha de respectiva (como o Apache Proton-C) e não requerem muito se qualquer devida atenção cliente-desde programadores de aplicações. Podemos abstratamente supõem a existência de poucos API primitivos, como a capacidade para ligar e criar alguma forma de *remetente* e *recetor* objetos de abstração, que depois de ter alguma forma de `send()` e `receive()` operações, respectivamente.

Ao discutir os recursos avançados do Azure Service Bus, como a procura de mensagem ou gerenciamento de sessões, esses recursos são explicados em termos AMQP, mas também como uma implementação de pseudo em camadas sobre essa suposta abstração de API.

## <a name="what-is-amqp"></a>O que é o AMQP?

AMQP é um protocolo de delimitação de quadros e de transferência. Delimitação de quadros significa que fornece a estrutura para os fluxos de dados binários que fluem em ambas as direções de uma ligação de rede. A estrutura fornece delineamento para blocos distintos de dados, denominado *quadros*, a serem trocados entre as partes ligadas. As capacidades de transferência Certifique-se de que ambas as partes da comunicação podem estabelecer uma compreensão partilhada sobre o quando os quadros devem ser transferidos e quando as transferências deverá ser consideradas concluídas.

Ao contrário das versões de anteriormente expiradas rascunho produzidas pelo grupo de trabalho de AMQP que ainda são utilizada por algumas mediadores de mensagens, protocolo do grupo de trabalho final e padronizado AMQP 1.0 não prescrevem a presença de um mediador de mensagem ou qualquer topologia específica para entidades dentro de um mediador de mensagens.

O protocolo pode ser utilizado para comunicação de ponto-a-ponto simétrica, para interação com os mediadores de mensagens que suportam filas e entidades de publicação/subscrição, como o Azure Service Bus. Também pode ser utilizado para interação com a infraestrutura de mensagens em que os padrões de interação são diferentes das filas regulares, tal como acontece com os Hubs de eventos do Azure. Um Hub de eventos funciona como uma fila quando os eventos são enviados ao mesmo, mas atua mais como um serviço de armazenamento serial, quando forem lidos os eventos de-lo. é um pouco semelhante uma unidade de banda. O cliente escolhe um deslocamento no fluxo de dados disponíveis e, em seguida, é servido todos os eventos de deslocamento para a mais recente disponível.

O protocolo AMQP 1.0 foi concebido para ser extensível, permitindo ainda mais especificações aperfeiçoar seus recursos. As especificações de três extensão discutidas neste documento ilustram isso. Para comunicação sobre a infra-estrutura existente de HTTPS/WebSockets, a configurar as portas TCP de AMQP nativas pode ser difícil. Uma especificação de enlace define como uma camada AMQP sobre WebSockets. Para interagir com a infraestrutura de mensagens de uma forma de solicitação/resposta para fins de gestão ou para fornecer funcionalidade avançada, a especificação de gestão de AMQP define os primitivos de interação básica necessária. Para a integração do modelo de autorização federada, a especificação de segurança afirmações com base em AMQP define como associar e renovar os tokens de autorização associados com ligações.

## <a name="basic-amqp-scenarios"></a>Cenários básicos do AMQP

Esta seção explica a utilização básica de AMQP 1.0 com o Azure Service Bus, que inclui a criação de ligações, sessões e ligações e a transferência de mensagens de e para entidades do Service Bus, tais como filas, tópicos e subscrições.

A origem autoritativa mais para saber mais sobre como funciona o AMQP é a especificação de AMQP 1.0, mas a especificação foi escrita para orientar precisamente a implementação e não a ensinar o protocolo. Esta seção se concentra na introdução ao máximo terminologia conforme necessário para descrever como o Service Bus utiliza o AMQP 1.0. Para uma introdução mais abrangente para AMQP, bem como uma discussão mais abrangente do AMQP 1.0, pode consultar [este curso de vídeo][this video course].

### <a name="connections-and-sessions"></a>Ligações e sessões

AMQP chama os programas de comunicação *contentores*; esses contêm *nós*, quais são as entidades de comunicação dentro desses contentores. Uma fila pode ser tão um nó. AMQP permite multiplexação, para que uma única ligação pode ser utilizada para vários caminhos de comunicação entre nós; Por exemplo, um cliente de aplicação pode receber de uma fila em simultâneo e enviar para outra fila através da mesma ligação de rede.

![][1]

A ligação de rede, portanto, está ancorada no contentor. Ele é iniciado pelo contêiner na função de cliente efetuar uma ligação de socket TCP de saída para um contentor na função de destinatário, o qual a escuta e aceita ligações de TCP de entrada. O handshake de ligação inclui negociar a versão de protocolo, declarando ou negociar o uso de segurança de nível de transporte (TLS/SSL) e um handshake de autenticação/autorização no âmbito da ligação que se baseia em SASL.

O Azure Service Bus requer a utilização de TLS. o tempo todo. Ele suporta ligações através da porta TCP 5671, por meio das quais a conexão TCP é primeiro recoberta com TLS antes da inserção o handshake do protocolo AMQP e também suporta ligações através da porta TCP 5672 por meio das quais o servidor imediatamente oferece uma atualização obrigatória da ligação para TLS com o modelo de prescrito para AMQP. O enlace de AMQP WebSockets cria um túnel através da porta TCP 443 que, em seguida, é equivalente a ligações AMQP 5671.

Depois de configurar a ligação e TLS, o Service Bus oferece duas opções de mecanismo do SASL:

* SASL simples é geralmente utilizado para passar credenciais de nome de utilizador e palavra-passe para um servidor. Barramento de serviço não tem contas, mas nomeado [regras de segurança de acesso partilhado](service-bus-sas.md), que a concessão de direitos e estão associado uma chave. O nome de uma regra é utilizado como o nome de utilizador e a chave (como texto de codificada em base64) é utilizado como a palavra-passe. Os direitos associados à regra escolhida regem as operações permitidas na ligação.
* SASL anônimo é utilizado para ignorar a autorização do SASL quando o cliente quiser usar o modelo de (CBS) de segurança afirmações com base em que é descrito mais à frente. Com esta opção, uma ligação de cliente pode ser estabelecida anonimamente para um curto período de tempo durante o qual o cliente só pode interagir com o ponto de extremidade do CBS e tem de concluir o handshake do CBS.

Após é estabelecida a ligação de transporte, os contentores declarar o tamanho de quadro máximo que estão dispostos a processar e, após um tempo limite de inatividade eles irá unilateral desligar se existir nenhuma atividade na ligação.

Eles também declaram quantos canais simultâneos são suportados. Um canal é um caminho de transferência unidirecional, saída, virtual sobre a ligação. Uma sessão demora um canal de cada um dos contentores interconectados para formar um caminho de comunicação bidirecional.

Sessões têm um modelo de controle de fluxo baseado em janela; Quando é criada uma sessão, cada parte declara o número de quadros que está disposto a aceitar para sua janela de recepção. Como os quadros de exchange de partes, preenchimento de quadros transferidos que janela e as transferências de parar quando a janela está cheio e até que a janela obtém reposição ou expandidos utilizando o *fluxo performative* (*performative* é o AMQP termo de gestos de nível de protocolo trocados entre as duas partes).

Este modelo baseado em janela é aproximadamente análogo ao conceito TCP do controle de fluxo baseado em janela, mas no nível da sessão dentro de socket. Conceito do protocolo de permitir que várias sessões simultâneas existe para que o tráfego de alta prioridade pode corriam anteriores tráfego normal limitado, como num lane rápida de autoestrada.

Atualmente, o Azure Service Bus utiliza exatamente uma sessão para cada ligação. O Service Bus quadro-tamanho máximo é 262.144 bytes (256 K bytes) para o Service Bus Standard e os Hubs de eventos. É 1,048,576 (1 MB) para Premium do Service Bus. Do Service Bus não impõe qualquer janela de limitação ao nível da sessão específica, mas repõe a janela regularmente como parte do controlo de fluxo de nível de ligação (consulte [a próxima seção](#links)).

Ligações, canais e sessões são efêmeras. Se a ligação subjacente fecha, ligações, tem possível restabelecer o túnel TLS, o contexto de autorização do SASL e sessões.

### <a name="links"></a>Ligações

AMQP transfere mensagens através de ligações. Uma ligação é um caminho de comunicação criado ao longo de uma sessão que permite a transferência de mensagens numa direção; a negociação de estado de transferência é sobre a ligação e bidirecionais entre as partes ligadas.

![][2]

Ligações podem ser criadas por qualquer contentor em qualquer altura através de uma sessão existente, o que torna o AMQP diferente de muitos outros protocolos, incluindo HTTP e MQTT, em que a inicialização de transferências e o caminho de transferência é um privilégio exclusivo da parte que cria o ligação de socket.

O contentor de iniciar a ligação pede o contentor oposto para aceitar uma ligação e que ele escolhe uma função do remetente ou do recetor. Por conseguinte, o contentor pode iniciar a criação de unidirecionais ou caminhos de comunicação bidirecional, com a última opção é modelada como pares de links.

Ligações são com o nome e associadas a nós. Conforme mencionado no início, nós são as entidades de comunicação dentro de um contentor.

No Service Bus, um nó é diretamente equivalente a uma fila, um tópico, uma subscrição ou uma subfila de mensagens não entregues de uma fila ou subscrição. O nome do nó utilizado no AMQP, portanto, é o nome relativo da entidade dentro do espaço de nomes do Service Bus. Se uma fila com o nome `myqueue`, que também é o nome do nó AMQP. Uma subscrição de tópico segue a Convenção de API de HTTP, pelo que está a ser ordenadas numa coleção de recursos "subscrições" e, assim, uma subscrição **sub** sobre um tópico **mytopic** tem o nome do nó AMQP  **mytopic/subscrições/sub**.

O cliente da ligação também é necessária para utilizar um nome de nó local para a criação de ligações; Barramento de serviço não é prescritivo sobre esses nomes de nó e não interpretá-los. Pilhas de cliente AMQP 1.0 geralmente, utilizam um esquema para garantir que esses nomes de nó efémeras são exclusivos no âmbito do cliente.

### <a name="transfers"></a>Transferências

Assim que tiver sido estabelecida uma ligação, as mensagens podem ser transferidas através dessa ligação. Em AMQP, uma transferência é executada com um gesto de protocolo explícito (o *transferência* performative) que move uma mensagem do remetente para o recetor através de uma ligação. Uma transferência está concluída quando for "integralmente liquidada", que significa que ambas as partes estabeleceu uma compreensão partilhada do resultado dessa transferência.

![][3]

No caso mais simples, o remetente pode escolher enviar mensagens "previamente liquidadas", que significa que o cliente não está interessado no resultado e o receptor não fornece quaisquer comentários sobre o resultado da operação. Este modo é suportado pelo barramento de serviço ao nível do protocolo AMQP, mas não é exposto em qualquer uma das APIs de cliente.

O caso comum é que as mensagens estão a ser enviadas liquidadas e o receptor, em seguida, indica aceitação ou de rejeição utilizando o *disposição* performative. Rejeição ocorre quando o destinatário não é possível aceitar a mensagem por qualquer motivo, e a mensagem de rejeição contém informações sobre o motivo, o que é uma estrutura de erro definida pelo AMQP. Se as mensagens são rejeitadas devido a erros internos dentro do Service Bus, o serviço devolve informações extras dentro dessa estrutura que pode ser utilizada para fornecer sugestões de diagnóstico para o pessoal de suporte se o preenchimento de pedidos de suporte. Saiba mais detalhes sobre erros mais tarde.

É uma forma especial de rejeição a *lançado* Estado, que indica que o destinatário tem não objection técnica para a transferência, mas também não interesse em se estabelecer a transferência. Nesse caso existe, por exemplo, quando uma mensagem é entregue a um cliente do Service Bus, e o cliente escolhe "abandonar" a mensagem porque ele não é possível executar o trabalho resultante de processamento da mensagem; a entrega de mensagens em si não está com defeito. Uma variação desse Estado é o *modificado* Estado, o que permite que as alterações à mensagem, que forem lançadas. Esse Estado não é utilizado pelo barramento de serviço neste momento.

A especificação de AMQP 1.0 define uma disposição mais Estado chamado *recebido*, especificamente que ajuda a lidar com a recuperação de link. Recuperação de ligação permite reconstituting o estado de uma ligação e quaisquer pendentes entregas por cima de uma nova ligação e a sessão, quando a ligação anterior e a sessão foram perdidas.

Barramento de serviço não suporta a recuperação de link; Se o cliente perder a ligação ao Service Bus com uma mensagem liquidada transferir pendentes, essa transferência de mensagens é perdida e o cliente deve voltar a ligar, restabelecer a ligação e repita a transferência.

Como tal, Service Bus e dos Hubs de eventos ", pelo menos, uma vez" suportam transferências em que o remetente pode ser garantido para a mensagem foi armazenado e aceites, mas não suporte "exatamente uma vez" transferências de ao nível do AMQP, onde o sistema deve tentar recuperar a ligação de fazer e Continue negociar o estado de entrega para evitar a duplicação de transferência de mensagens.

Para compensar a possível duplicado envia, Service Bus suporta a deteção de duplicados como uma funcionalidade opcional sobre as filas e tópicos. Deteção de duplicados regista os IDs de mensagem de todas as mensagens recebidas durante uma janela de tempo definido pelo utilizador, em seguida, silenciosamente descarta todas as mensagens enviadas com os mesmos IDs de mensagem durante essa mesma janela.

### <a name="flow-control"></a>Controlo de fluxo

Além do modelo de controle de fluxo de nível de sessão discutido anteriormente, cada ligação tem seu próprio modelo de fluxo de controle. Controlo de fluxo de nível de sessão protege o contentor da necessidade de lidar com demasiados quadros numa vez, controle de fluxo de nível de ligação, coloque o aplicativo responsável por quantas mensagens que ele deseja para tratar de uma ligação e quando.

![][4]

Num link, as transferências só podem acontecer quando o remetente tem suficiente *ligar o crédito*. Crédito de ligação é um contador definido pelo destinatário com o *fluxo* performative, que tem um âmbito para um link. Quando o remetente é atribuído o crédito de ligação, este tenta utilizar a cópia de segurança que crédito fornecendo mensagens. Cada diminui de entrega de mensagens na ligação restante crédito por 1. Quando o crédito de ligação se esgotar, pare a entregas.

Quando o Service Bus é na função de destinatário, ele instantaneamente fornece o remetente com o crédito de ligação de uma ampla, para que as mensagens podem ser enviadas imediatamente. À medida que é utilizado o crédito de ligação, do Service Bus, ocasionalmente, envia uma *fluxo* performative ao remetente para atualizar o saldo de crédito de ligação.

Na função de remetente, o Service Bus envia mensagens para utilizar até qualquer crédito de ligação pendentes.

Uma chamada de "receber" no nível da API que se traduz numa *fluxo* performative a ser enviados para o barramento de serviço pelo cliente e o Service Bus consome essa crédito, efetuando a primeira mensagem disponível, desbloqueada da fila, bloqueando-, e transferi-los. Se não houver nenhuma mensagem prontamente disponíveis para entrega, qualquer crédito pendente por qualquer ligação estabelecida com que a entidade específica permanece gravada por ordem de chegada e mensagens são bloqueadas e transferidas à medida que ficam disponíveis para utilizar qualquer crédito pendente.

O bloqueio numa mensagem é libertado quando a transferência é incorporada a um dos Estados de terminal *aceite*, *rejeitado*, ou *lançado*. A mensagem é removida do Service Bus, quando o estado terminal for *aceites*. Continua no Service Bus e é entregue ao destinatário seguinte quando a transferência atinge qualquer um dos outros Estados. Do Service Bus move automaticamente a mensagem numa fila de mensagens não entregues a entidade quando alcança a contagem máxima de entrega permitida para a entidade devido a rejeições repetidas ou de versões.

Embora as APIs de barramento de serviço não expõem diretamente essa opção hoje em dia, um cliente de protocolo AMQP de nível inferior pode utilizar o modelo de crédito de ligação para ativar a interação de "pull-style" de emissão de uma unidade de crédito para cada pedido de receção para um modelo de "push-style" por emitir um grande número de créditos de ligação e, em seguida, receber mensagens à medida que ficam disponíveis sem qualquer interação adicional. Push é suportado através da [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) ou [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) configurações de propriedade. Quando estão diferente de zero, o cliente AMQP usa-o como o crédito de ligação.

Neste contexto, é importante compreender que o período de expiração do bloqueio da mensagem dentro da entidade começa quando a mensagem foi obtida da entidade, não quando a mensagem será colocada na conexão. Sempre que o cliente indica a preparação para receber mensagens através da emissão de crédito de ligação, espera-se, por conseguinte, para receber ativamente mensagens através da rede e estar pronto para manipulá-las. Caso contrário, o bloqueio da mensagem pode ter expirado, antes da mensagem ainda é entregue. A utilização do controlo de fluxo de crédito de ligação deve refletir diretamente a preparação de imediato para lidar com mensagens disponíveis expedidas para o recetor.

Em resumo, as secções seguintes fornecem uma visão geral esquemática o fluxo performative durante as interações de API diferentes. Cada secção descreve uma operação lógica diferente. Algumas dessas interações podem ser "lentas," o que significa que eles podem ser executados apenas quando necessário. Criação de um remetente da mensagem não pode causar uma interação de rede até que a primeira mensagem enviada ou solicitada.

As setas na tabela a seguir mostram a direção do fluxo performative.

#### <a name="create-message-receiver"></a>Criar o recetor de mensagem

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>destino = {ID de ligação de cliente}<br/>) |Cliente anexa a entidade recetor |
| A anexar o respetivo final de ligação de respostas de Service Bus |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>destino = {ID de ligação de cliente}<br/>) |

#### <a name="create-message-sender"></a>Criar o remetente da mensagem

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Nenhuma ação |
| Nenhuma ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Criar o remetente da mensagem (erro)

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Nenhuma ação |
| Nenhuma ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Recetor/remetente da mensagem fechar

| Cliente | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Enviar (êxito)

| Cliente | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Enviar (erro)

| Cliente | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nenhuma ação |
| Nenhuma ação |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Receber

| Cliente | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Nenhuma ação |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nenhuma ação |

#### <a name="multi-message-receive"></a>Receber mensagem multi

| Cliente | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Nenhuma ação |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nenhuma ação |< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nenhuma ação |

### <a name="messages"></a>Mensagens

As secções seguintes explicam as propriedades das secções de mensagem padrão do AMQP são utilizadas pelo barramento de serviço e como devem ser mapeadas para o conjunto de API do Service Bus.

Qualquer propriedade que o aplicativo precisa define deve ser mapeada para do AMQP `application-properties` mapa.

#### <a name="header"></a>cabeçalho

| Nome do Campo | Utilização | Nome da API |
| --- | --- | --- |
| durável |- |- |
| prioridade |- |- |
| ttl |Tempo de duração para esta mensagem |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| primeiro acquirer |- |- |
| Contagem de entrega |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Nome do Campo | Utilização | Nome da API |
| --- | --- | --- |
| id da mensagem |Identificador para esta mensagem definida pelo aplicativo, de forma livre. Utilizada para a deteção de duplicados. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| id de utilizador |Identificador de utilizador definida pelo aplicativo, não interpretado pelo Service Bus. |Não está acessível através da API do barramento de serviço. |
| para |Identificador de destino definido pelo aplicativo, não interpretado pelo Service Bus. |[Para](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Identificador de finalidade do mensagem definida pelo aplicativo, não interpretado pelo Service Bus. |[Etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Indicador de caminho de resposta definida pelo aplicativo, não interpretado pelo Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identificador de correlação definida pelo aplicativo, não interpretado pelo Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Definido pelo aplicativo indicador de tipo de conteúdo para o corpo, não interpretado pelo Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Definido pelo aplicativo codificação de conteúdo indicador para o corpo, não interpretado pelo Service Bus. |Não está acessível através da API do barramento de serviço. |
| absolute-expiry-time |Declara em qual absoluto instantâneas a mensagem expira. Ignorada na entrada (cabeçalho TTL é observada), autoritativa de saída. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| hora de criação |Declara nesse momento a mensagem foi criada. Não é utilizada pelo barramento de serviço |Não está acessível através da API do barramento de serviço. |
| group-id |Identificador para um conjunto relacionado de mensagens definidas pelo aplicativo. Utilizado para sessões de barramento de serviço. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |Identificar o número de sequência relativo da mensagem dentro de uma sessão de contador. Ignorados pelo Service Bus. |Não está acessível através da API do barramento de serviço. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Anotações de mensagem

Existem alguns outras service bus propriedades da mensagem, que não fazem parte de propriedades da mensagem AMQP e são transferidas como `MessageAnnotations` na mensagem.

| Chave de mapa de anotação | Utilização | Nome da API |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Declara nesse momento, a mensagem deve aparecer na entidade |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Chave definida pelo aplicativo, que determina qual partição a mensagem deve apresentar. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Definido pelo aplicativo chave de partição valor quando uma transação está a ser utilizado para enviar mensagens através de uma fila de transferência. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Definidas pelo serviço fuso horário UTC que representa o tempo em questão de enfileiramento de mensagens. Ignorada na entrada. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | Definidas pelo serviço número único atribuído a uma mensagem. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Número de sequência de definidas pelo serviço colocados em fila da mensagem. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-optar ativamente por não-bloqueado-até | Definidas pelo serviço. A data e hora até que a mensagem será bloqueada na fila/subscrição. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Definidas pelo serviço. Se a mensagem é recebida da fila de entregues, a origem da mensagem original. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Capacidade de transação

Uma transação agrupa dois ou mais operações em conjunto para um âmbito de execução. Por natureza, tal uma transação tem de garantir que todas as operações que pertencem a um determinado grupo de operações com êxito ou falham em conjunto.
As operações são agrupadas por um identificador `txn-id`.

Para interação transacional, o cliente funciona como um `transaction controller` , que controla as operações que devem ser agrupadas. Serviço do Service Bus funciona como um `transactional resource` e realiza o trabalho conforme solicitado pelo `transaction controller`.

O cliente e o serviço de comunicam através de um `control link` , que é estabelecido pelo cliente. O `declare` e `discharge` as mensagens são enviadas pelo controlador de através da ligação de controlo para alocar e concluir transações, respectivamente (não representam a demarcação de trabalho transacional). O real de envio/receção não é efetuada nesta ligação. Cada operação transacional pedida é explicitamente identificados com o desejado `txn-id` e, portanto, podem ocorrer em qualquer ligação na ligação. Se a ligação de controlo é fechada enquanto existem transações não discharged, que ele criou, em seguida, todas essas transações são imediatamente revertidas e tentativas para realizar mais trabalho transacional neles irão causar falha. As mensagens numa ligação de controlo não tem de ser pré liquidada.

Cada ligação tem de iniciar a sua própria ligação de controlo para poder começar e terminar de transações. O serviço define um destino especial que funciona como um `coordinator`. O cliente/controlador estabelece uma ligação de controlo para este destino. Ligação de controlo está fora do limite de uma entidade, ou seja, a mesma ligação de controlo pode ser utilizada para iniciar e proprietários de transações para várias entidades.

#### <a name="starting-a-transaction"></a>Iniciar uma transação

Para iniciar o trabalho transacional. o controlador tem de obter um `txn-id` do coordenador. Ele faz isso enviando um `declare` mensagem do tipo. Se a declaração for bem-sucedida, o coordenador de responde com um resultado de disposição, o que traz o atribuído `txn-id`.

| Cliente (controlador) | | O Service Bus (coordenador) |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**sender**,<br/>destino =**coordenador**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()** )}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Declared**(<br/>**transações-id**= {ID da transação}<br/>))|

#### <a name="discharging-a-transaction"></a>Discharging uma transação

O controlador conclui o trabalho transacional, enviando um `discharge` mensagem para o coordenador. O controlador indica que deseja confirmar ou reverter o trabalho transacional, configurando o `fail` sinalizador no corpo do altas. Se o coordenador não consegue concluir as altas, a mensagem será rejeitada com este resultado contendo o `transaction-error`.

> Nota: falhar = true se refere a reversão de uma transação e ativação = false se refere a consolidação.

| Cliente (controlador) | | O Service Bus (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>Trabalho transacional<br/>em outras ligações<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Proprietários (txn-id = 0,<br/>falhar = false)** )}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()** )|

#### <a name="sending-a-message-in-a-transaction"></a>Enviar uma mensagem numa transação

Todo o trabalho transacional é feito com o estado de entrega transacional `transactional-state` que carrega o id de transações. No caso de envio de mensagens, o estado transacional é executado por quadro de transferência da mensagem. 

| Cliente (controlador) | | O Service Bus (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)** )<br/>{payload}| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()** ))|

#### <a name="disposing-a-message-in-a-transaction"></a>Como descartar uma mensagem numa transação

Disposição de mensagem inclui operações como `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Para executar estas operações dentro de uma transação, passe o `transactional-state` da eliminação.

| Cliente (controlador) | | O Service Bus (coordenador) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{payload}|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()** ))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Recursos avançados do Service Bus

Esta seção aborda os recursos avançados do Azure Service Bus baseiam-se em extensões de rascunho para AMQP, atualmente em desenvolvimento no Comitê técnico OASIS para AMQP. Service Bus implementa as versões mais recentes destas rascunhos e adota alterações introduzidas que esses rascunhos alcançar o estado padrão.

> [!NOTE]
> Mensagens do Service Bus operações avançadas são compatíveis por meio de um padrão de solicitação/resposta. Os detalhes destas operações são descritos no artigo [AMQP 1.0 no Service Bus: operações baseados no pedido-resposta](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Gestão de AMQP

A especificação de gestão de AMQP é a primeira das extensões de rascunho discutidos neste artigo. Essa especificação define um conjunto de protocolos em camadas sobre o protocolo AMQP que permitem que as interações de gestão com a infraestrutura de mensagens através de AMQP. A especificação define operações genéricas, como *crie*, *ler*, *atualizar*, e *eliminar* para gerenciar entidades dentro de um infraestrutura de mensagens e um conjunto de operações de consulta.

Todos os esses gestos exigem uma interação de solicitação/resposta entre o cliente e a infraestrutura de mensagens e, portanto, a especificação define como modelar esse padrão de interação por cima do AMQP: o cliente liga-se para a infraestrutura de mensagens, inicia uma sessão e, em seguida, cria um par de links. Num link, o cliente funciona como remetente e no outro atua como receptor, criando assim um par de ligações que podem atuar como um canal bidirecional.

| Operação lógica | Cliente | Service Bus |
| --- | --- | --- |
| Criar o caminho de resposta do pedido |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |Nenhuma ação |
| Criar o caminho de resposta do pedido |Nenhuma ação |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| Criar o caminho de resposta do pedido |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| Criar o caminho de resposta do pedido |Nenhuma ação |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

Ter esse par de ligações no local, a implementação de solicitação/resposta é simples: um pedido é uma mensagem enviada para uma entidade dentro da infraestrutura de mensagens que compreende este padrão. Nessa mensagem de solicitação, o *responder a* campo na *propriedades* secção está definida como o *destino* identificador para a ligação em que fornecer a resposta. A entidade de processamento processa a solicitação e, em seguida, fornece a resposta através da ligação cujo *destino* identificador corresponde a indicado *responder a* identificador.

O padrão é claro que requer que o contentor de cliente e o identificador de gerados pelo cliente de mensagens em fila para o destino de resposta são exclusivos em todos os clientes e, por motivos de segurança, também é difícil prever.

As trocas de mensagens utilizadas para o protocolo de gestão e para todos os outros protocolos que utilizam o mesmo padrão ocorrem ao nível da aplicação; não defina novas gestos de nível de protocolo AMQP. Que é intencional, para que as aplicações podem tirar vantagem imediata das seguintes extensões com pilhas de AMQP 1.0 em conformidade.

Do Service Bus não implementa atualmente qualquer um dos principais recursos da especificação de gestão, mas o padrão de solicitação/resposta definido pela especificação de gestão é fundamental para a funcionalidade de segurança afirmações com base em e para praticamente todos os avançados recursos abordados nas seções a seguir:

### <a name="claims-based-authorization"></a>Autorização baseada em afirmações

O rascunho de especificação de AMQP autorização afirmações com base em (CBS) traz o padrão de solicitação/resposta de especificação de gestão e descreve um modelo de generalizado para saber como utilizar tokens de segurança federada com o AMQP.

O modelo de segurança padrão de AMQP debatido na introdução é baseado em SASL e integra-se com o handshake de ligação AMQP. Utilizar o SASL tem a vantagem de que ele fornece um modelo extensível para o qual um conjunto de mecanismos foram definidas de que todos os protocolos formalmente depende do SASL podem se beneficiar. Entre esses mecanismos são "Simples" para a transferência de nomes de utilizador e palavras-passe, "Externa" para ligar a segurança ao nível da TLS, "Anónimo" para expressar a ausência de autenticação/autorização explícita e uma ampla variedade de mecanismos adicionais que permitir a passagem de credenciais de autenticação e/ou autorização ou tokens.

Integração do SASL do AMQP tem duas desvantagens:

* Todas as credenciais e tokens passam para a ligação. Uma infraestrutura de mensagens poderá fornecer controle de acesso diferenciada numa base por entidade; Por exemplo, permitindo que o portador de um token enviar para fila A, mas não para a fila B. Com o contexto de autorização ancorado na ligação, não é possível utilizar uma ligação única e ainda utilizar tokens de acesso diferentes para respostas e a fila B.
* Tokens de acesso, normalmente, só são válidos por um período limitado. Este validade pedir ao utilizador que periodicamente obter novamente a tokens e fornece uma oportunidade para o emissor de tokens para recusar a emitir um novo token, se tem alterado as permissões de acesso do utilizador. As ligações AMQP podem durar por longos períodos de tempo. O modelo do SASL apenas fornece a oportunidade de se configurar um token ao tempo de ligação, o que significa que a infraestrutura de mensagens ou tem de desligar o cliente quando o token expira ou ele precisa aceitar o risco de permitir a comunicação contínua com um cliente que do direitos de acesso podem ter byl odvolán até lá.

A especificação de AMQP CBS, implementada pelo Service Bus, permite que uma solução elegante para dois desses problemas: Ele permite que um cliente para associar os tokens de acesso a cada nó e para atualizar esses tokens antes de expirarem, sem interromper o fluxo de mensagens.

CBS define um nó virtual de gestão, denominado *$cbs*, para ser fornecido pela infraestrutura de mensagens. O nó de gestão aceita tokens em nome de todos os outros nós na infraestrutura de mensagens.

O gesto de protocolo é uma troca de solicitação/resposta, conforme definido pela especificação de gestão. Que significa que o cliente estabelece um par de ligações com o *$cbs* nó e, em seguida, passa uma solicitação na ligação de saída e, em seguida, aguarda pela resposta na ligação de entrada.

A mensagem de solicitação tem as seguintes propriedades da aplicação:

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| Operação |Não |string |**put-token** |
| type |Não |string |O tipo de token colocá-lo. |
| name |Não |string |O "público" às quais se aplica o token. |
| expiração |Sim |timestamp |A hora de expiração do token. |

O *nome* propriedade identifica a entidade com a qual o token deve ser associado. No Service Bus é o caminho para a fila ou tópico/subscrição. O *tipo* propriedade identifica o tipo de token:

| Tipo de token | Descrição do token | Tipo de corpo | Notas |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |Valor de AMQP (cadeia) |Ainda não está disponível. |
| amqp:swt |Simple Web tokens (SWT) |Valor de AMQP (cadeia) |Só é suportado para tokens SWT emitidos pelo AAD/ACS |
| servicebus.windows.net:sastoken |Token SAS do Service Bus |Valor de AMQP (cadeia) |- |

Tokens a concessão de direitos. Do Service Bus tem conhecimento três direitos fundamentais: "Enviar" permite enviar, permite "Escutar" receber e permite manipular entidades de "Gerir". Tokens SWT vystavitel AAD/ACS explicitamente incluem esses direitos como afirmações. Tokens SAS de barramento de serviço Consulte regras configuradas no espaço de nomes ou entidade, e essas regras estão configuradas com direitos. O token de assinatura com a chave associada essa regra, portanto, faz com que o token express os respetivos direitos. O token associado à utilização uma entidade *token de put* permite que o cliente conectado para interagir com a entidade pelos direitos de token. Uma ligação em que o cliente assume a *remetente* a função requer "Enviar" diretamente; utilizando o *recetor* a função requer "Listen" à direita.

A mensagem de resposta tem segue *propriedades da aplicação* valores

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| status-code |Não |int |Código de resposta HTTP **[RFC2616]** . |
| status-description |Sim |string |Descrição do Estado. |

O cliente pode chamar *token de put* para qualquer entidade na infraestrutura de mensagens e repetidamente. Os tokens são um âmbito para o cliente atual e ancorados na ligação atual, que significa que o servidor remove quaisquer tokens retidos quando a conexão caiu.

A implementação atual do Service Bus permite apenas CBS em conjunto com o método SASL "Anónimo". Uma ligação de SSL/TLS sempre tem de existir antes do handshake do SASL.

O mecanismo de anônimo, portanto, têm de ser suportado pelo cliente AMQP 1.0 escolhido. Acesso anónimo significa que o handshake de ligação inicial, incluindo a criação da sessão inicial ocorre sem o Service Bus saber quem está a criar a ligação.

Assim que a ligação e a sessão for estabelecida, anexando as ligações para o *$cbs* nó e o envio a *put token* apenas são permitidas operações de pedidos. Um token válido tem de ser definido com êxito com um *token de put* pedido para o nó alguma entidade dentro de 20 segundos depois da ligação for estabelecida, caso contrário, a conexão é unilateral cancelada pelo Service Bus.

O cliente é, em seguida, responsável por manter o controle de expiração do token. Quando um token expira, do Service Bus ignora imediatamente todas as ligações na ligação para a entidade correspondente. Para evitar a ocorrência do problema, o cliente pode substituir o token para o nó com uma nova em qualquer altura através de virtual *$cbs* nó de gestão com o mesmo *put token* gesto e, sem introdução forma da carga de tráfego que fluxos em ligações diferentes.

### <a name="send-via-functionality"></a>Funcionalidade de envio-via

[Através de envio / transferência remetente](service-bus-transactions.md#transfers-and-send-via) é uma funcionalidade que permite que o serviço de barramento para a frente de uma determinada mensagem para uma entidade de destino por meio de outra entidade. Esta funcionalidade é utilizada para executar operações em entidades numa única transação.

Com esta funcionalidade, criar um remetente e estabelecer a ligação para o `via-entity`. Ao estabelecer a ligação, as informações adicionais são passadas para estabelecer o destino VERDADEIRO de mensagens/transferências nesta ligação. Depois da anexação tiver sido concluída com êxito, todas as mensagens enviadas nesta ligação automaticamente são reencaminhadas para o *entidade de destino* através de *via entity*. 

> Nota: Autenticação precisa ser realizada para ambos *por meio de entidade* e *entidade de destino* antes de estabelecer esta ligação.

| Cliente | | Service Bus |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target= **{via-entity}** ,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>Propriedades = [(do mapa<br/>com.microsoft:transfer-destination-address=<br/>{entidade de destino})] ) |

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o AMQP, visite as seguintes ligações:

* [Descrição geral do AMQP no Service Bus]
* [Suporte de AMQP 1.0 para o Service Bus filas e tópicos particionados]
* [AMQP no Service Bus para o Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Descrição geral do AMQP no Service Bus]: service-bus-amqp-overview.md
[Suporte de AMQP 1.0 para o Service Bus filas e tópicos particionados]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no Service Bus para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
