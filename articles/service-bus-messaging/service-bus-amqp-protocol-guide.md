---
title: GUIA DE Protocolo DE Ônibus e Hubs de Ônibus de Serviço Azure [ AmQP 1.0] Microsoft Docs
description: Guia protocolar para expressões e descrição da AMQP 1.0 em Azure Service Bus and Event Hubs
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
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086145"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Guia de protocolo sinuoso amqp 1.0 em Azure Service Bus and Event Hubs

O Protocolo de Fila de Mensagens Avançadas 1.0 é um protocolo de enquadramento e transferência padronizado para assincronia, segurança e transferência de mensagens entre duas partes. É o protocolo primário da Azure Service Bus Messaging e do Azure Event Hubs. Ambos os serviços também suportam HTTPS. O protocolo SBMP proprietário, que também é suportado, está a ser gradualmente eliminado a favor da AMQP.

A AMQP 1.0 é o resultado de uma ampla colaboração da indústria que reuniu fornecedores de middleware, como a Microsoft e a Red Hat, com muitos utilizadores de mensagens de middleware, como o JP Morgan Chase, representando a indústria de serviços financeiros. O fórum técnico de normalização para o protocolo AMQP e as especificações de extensão é o OASIS, e obteve aprovação formal como norma internacional como ISO/IEC 19494.

## <a name="goals"></a>Objetivos

Este artigo resume brevemente os conceitos fundamentais da especificação de mensagens AMQP 1.0, juntamente com um pequeno conjunto de especificações de extensão que estão atualmente a ser finalizados no comité técnico da OASIS AMQP e explica como o Azure Service Bus está a ser finalizado no comité técnico da OASIS AMQP e explica como o Azure Service Bus está a ser finalizado no comité técnico da OASIS AMQP e explica como o Azure Service Bus está a ser finalizado no comité técnico da OASIS AMQP e explica como o Azure Service Bus bus implementa e baseia-se nestas especificações.

O objetivo é que qualquer desenvolvedor que utilize qualquer pilha de clientes AMQP 1.0 existente em qualquer plataforma para poder interagir com o Azure Service Bus via AMQP 1.0.

Pilhas amqp 1.0 comuns de uso geral, tais como Apache Proton ou AMQP.NET Lite, já implementam todos os protocolos core AMQP 1.0. Esses gestos fundamentais são, por vezes, envoltos numa API de nível superior; Apache Proton até oferece dois, o imperativo Messenger API e o reator reativo API.

Na discussão seguinte, assumimos que a gestão das ligações, sessões e ligações AMQP e o manuseamento de transferências de quadros e controlo de fluxos são tratados pela respetiva pilha (como apache Proton-C) e não requerem muita atenção se alguma atenção específica de desenvolvedores de aplicações. Assumimos abstratamente a existência de alguns primitivos da API, como a capacidade de se conectar, e criar alguma forma de *remetente* e objetos de abstração *recetor,* que depois têm alguma forma de `send()` e operações `receive()`, respectivamente.

Ao discutir capacidades avançadas do Azure Service Bus, como a navegação de mensagens ou a gestão de sessões, essas funcionalidades são explicadas em termos AMQP, mas também como uma pseudo-implementação em camadas em cima desta suposta abstração da API.

## <a name="what-is-amqp"></a>O que é AMQP?

AmQP é um protocolo de enquadramento e transferência. O enquadramento significa que fornece estrutura para fluxos binários de dados que fluem em qualquer direção de uma ligação de rede. A estrutura prevê a delimitação de blocos distintos de dados, *chamados quadros,* a trocar entre as partes ligadas. As capacidades de transferência asseguram-se de que ambas as partes comunicadoras podem estabelecer um entendimento partilhado sobre quando os quadros devem ser transferidos e quando as transferências serão consideradas completas.

Ao contrário de versões anteriores expiradas produzidas pelo grupo de trabalho AMQP que ainda estão em uso por alguns corretores de mensagens, o protocolo final do grupo de trabalho e o protocolo AMQP 1.0 padronizado não prescreve a presença de um corretor de mensagens ou de qualquer topologia específica para entidades dentro de um corretor de mensagens.

O protocolo pode ser usado para comunicação par-a-par simétrica, para interação com corretores de mensagens que suportam filas e editores/entidades de subscrição, como faz o Azure Service Bus. Também pode ser usado para interação com infraestrutura de mensagens onde os padrões de interação são diferentes das filas regulares, como é o caso dos Hubs de Eventos Azure. Um Event Hub age como uma fila quando os eventos são enviados para ele, mas age mais como um serviço de armazenamento em série quando os eventos são lidos a partir dele; assemelha-se um pouco a uma unidade de fita adesiva. O cliente escolhe uma compensação no fluxo de dados disponível e é depois servido todos os eventos desde essa compensação até aos mais recentes disponíveis.

O protocolo AMQP 1.0 foi concebido para ser extensível, permitindo que novas especificações melhorem as suas capacidades. As três especificações de extensão discutidas neste documento ilustram-no. Para a comunicação sobre a infraestrutura HTTPS/WebSockets existente, configurar as portas nativas de TCP AMQP pode ser difícil. Uma especificação de ligação define como camadas AMQP sobre WebSockets. Para interagir com a infraestrutura de mensagens de forma de pedido/resposta para fins de gestão ou para fornecer funcionalidade avançada, a especificação de gestão amqp define os primitivos de interação básica necessários. Para a integração de modelos de autorização federada, a especificação de segurança baseada em sinistros AMQP define como associar e renovar fichas de autorização associadas a links.

## <a name="basic-amqp-scenarios"></a>Cenários básicos de AMQP

Esta secção explica o uso básico da AMQP 1.0 com o Azure Service Bus, que inclui a criação de ligações, sessões e links, e a transferência de mensagens de e para entidades de Ônibus de serviço, como filas, tópicos e subscrições.

A fonte mais autoritária a saber como funciona a AMQP é a especificação AMQP 1.0, mas a especificação foi escrita para orientar precisamente a implementação e não para ensinar o protocolo. Esta secção centra-se na introdução da terminologia necessária para descrever como o Ônibus de serviço utiliza amQP 1.0. Para uma introdução mais abrangente à AMQP, bem como uma discussão mais alargada sobre amqp 1.0, você pode rever [este curso][this video course]de vídeo .

### <a name="connections-and-sessions"></a>Conexões e sessões

A AMQP chama os *recipientes*dos programas de comunicação; estes contêm *nós,* que são as entidades comunicantes dentro desses contentores. Uma fila pode ser um nó. A AMQP permite o multiplexing, para que uma única ligação possa ser usada para muitos caminhos de comunicação entre nós; por exemplo, um cliente de aplicação pode receber simultaneamente de uma fila e enviar para outra fila sobre a mesma ligação de rede.

![][1]

A ligação de rede está, assim, ancorada no recipiente. É iniciado pelo contentor na função cliente que faz uma ligação de tomada TCP de saída a um recipiente na função recetora, que ouve e aceita ligações TCP de entrada. O aperto de mão de ligação inclui a negociação da versão protocolar, a declaração ou a negociação da utilização da Segurança do Nível de Transporte (TLS/SSL), e um aperto de mão de autenticação/autorização no âmbito de ligação que se baseia no SASL.

O Azure Service Bus requer sempre a utilização de TLS. Suporta ligações sobre a porta TCP 5671, através da qual a ligação TCP é primeiramente sobreposta com TLS antes de entrar no aperto de mão do protocolo AMQP, e também suporta ligações sobre a porta TCP 5672, através da qual o servidor oferece imediatamente uma atualização obrigatória da ligação para TLS utilizando o modelo prescrito pela AMQP. A ligação AMQP WebSockets cria um túnel sobre a porta TCP 443 que é então equivalente às ligações AMQP 5671.

Após a instalação da ligação e do TLS, o Service Bus oferece duas opções de mecanismoS SASL:

* O SASL PLAIN é comumente utilizado para passar credenciais de nome de utilizador e palavra-passe para um servidor. O Service Bus não tem contas, mas [chama-se Regras de Segurança](service-bus-sas.md)de Acesso Partilhado , que conferem direitos e estão associados a uma chave. O nome de uma regra é usado como nome de utilizador e a chave (como texto codificado base64) é usada como palavra-passe. Os direitos associados à regra escolhida regem as operações permitidas na ligação.
* O SASL ANONYMOUS é utilizado para contornar a autorização da SASL quando o cliente quer utilizar o modelo de segurança baseada em sinistros (CBS) que é descrito mais tarde. Com esta opção, uma ligação ao cliente pode ser estabelecida anonimamente por um curto período de tempo durante o qual o cliente só pode interagir com o ponto final da CBS e o aperto de mão da CBS deve ser concluído.

Após a ligação de transporte, os recipientes declaram cada um o tamanho máximo da armação que estão dispostos a manusear e, após um intervalo de tempo inativo, desligar-se-ão unilateralmente se não houver atividade na ligação.

Também declaram quantos canais simultâneos são apoiados. Um canal é uma via de transferência virtual unidirecional, de saída, em cima da ligação. Uma sessão requer um canal de cada um dos recipientes interligados para formar um caminho de comunicação bidirecional.

As sessões têm um modelo de controlo de fluxo baseado em janelas; quando uma sessão é criada, cada partido declara quantas molduras está disposta a aceitar na sua janela de receção. À medida que as partes trocam quadros, os quadros transferidos preenchem essa janela e as transferências param quando a janela está cheia e até que a janela seja redefinida ou expandida utilizando o *fluxo performativo* *(performativo* é o termo AMQP para gestos de nível protocolar trocados entre as duas partes).

Este modelo baseado em janelas é aproximadamente análogo ao conceito de TCP de controlo de fluxo baseado em janelas, mas ao nível da sessão dentro da tomada. O conceito do protocolo de permitir múltiplas sessões simultâneas existe para que o tráfego de alta prioridade possa ser apressado para além do tráfego normal acelerado, como numa via expressa rodoviária.

O Azure Service Bus utiliza atualmente exatamente uma sessão para cada ligação. O tamanho máximo do ônibus de serviço é de 262.144 bytes (256-K bytes) para Service Bus Standard e Event Hubs. É 1.048.576 (1 MB) para Service Bus Premium. O Ônibus de serviço não impõe nenhuma janela de estrangulamento ao nível da sessão, mas repõe a janela regularmente como parte do controlo de fluxo de nível de ligação (ver [secção seguinte).](#links)

Ligações, canais e sessões são efémeros. Se a ligação subjacente colapsar, as ligações, o túnel TLS, o contexto de autorização SASL e as sessões devem ser restabelecidas.

### <a name="amqp-outbound-port-requirements"></a>Requisitos da porta de saída AMQP

Os clientes que utilizam ligações AMQP sobre tCP exigem que as portas 5671 e 5672 sejam abertas na firewall local. Juntamente com estas portas, pode ser necessário abrir portas adicionais se a funcionalidade [EnableLinkRedirect](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) estiver ativada. `EnableLinkRedirect` é uma nova funcionalidade de mensagens que ajuda a saltar um salto enquanto recebe mensagens, ajudando assim a aumentar a entrada. O cliente começaria a comunicar diretamente com o serviço back-end sobre a gama portuária 104XX, como mostra a seguinte imagem. 

![Lista de portos de destino][4]

Um cliente .NET falharia com uma SocketException ("Foi feita uma tentativa de acesso a uma tomada de uma forma proibida pelas suas permissões de acesso") se estas portas estiverem bloqueadas pela firewall. A funcionalidade pode ser desativada colocando `EnableAmqpLinkRedirect=false` na cadeia de ligação, o que obriga os clientes a comunicar com o serviço remoto sobre a porta 5671.


### <a name="links"></a>Ligações

A AMQP transfere mensagens sobre links. Um link é um caminho de comunicação criado ao longo de uma sessão que permite transferir mensagens numa direção; a negociação do estado de transferência é sobre a ligação e bidireciona entre as partes ligadas.

![][2]

As ligações podem ser criadas por qualquer um dos contentores a qualquer momento e durante uma sessão existente, o que torna a AMQP diferente de muitos outros protocolos, incluindo HTTP e MQTT, onde o início de transferências e via de transferência é um privilégio exclusivo da parte que cria o ligação à tomada.

O recipiente de início de ligação pede ao recipiente oposto que aceite um link e escolhe um papel de remetente ou recetor. Portanto, qualquer um dos recipientes pode iniciar a criação de caminhos de comunicação unidirecionais ou bidirecionais, com este último modelado como pares de ligações.

Os links são nomeados e associados a nódosos. Como afirmado no início, os nós são as entidades comunicantes dentro de um contentor.

No Ônibus de Serviço, um nó é diretamente equivalente a uma fila, um tópico, uma subscrição ou uma subfila de uma fila ou subscrição. O nome do nó utilizado na AMQP é, portanto, o nome relativo da entidade dentro do espaço de nome do Bus de Serviço. Se uma fila for chamada `myqueue`, é também o seu nome de nó AMQP. Uma subscrição de tópico segue a convenção HTTP API sendo classificada numa coleção de recursos de "subscrições" e, assim, um **sub** de subscrição sobre um tópico **tem** o nome de nó AMQP **mytopic/subscrições/sub**.

O cliente de ligação também é obrigado a usar um nome de nó local para criar links; O Service Bus não é prescritivo sobre esses nomes de nó e não os interpreta. As pilhas de clientes AMQP 1.0 geralmente usam um esquema para garantir que estes nomes de nó efémeros são únicos no âmbito do cliente.

### <a name="transfers"></a>Transferências

Uma vez estabelecido um link, as mensagens podem ser transferidas para fora desse link. Na AMQP, uma transferência é executada com um gesto de protocolo explícito (o performativo de *transferência)* que move uma mensagem do remetente para o recetor sobre um link. Uma transferência fica completa quando está "liquidada", o que significa que ambas as partes estabeleceram um entendimento partilhado sobre o resultado dessa transferência.

![][3]

No caso mais simples, o remetente pode optar por enviar mensagens "pré-resolvidas", o que significa que o cliente não está interessado no resultado e o recetor não fornece qualquer feedback sobre o resultado da operação. Este modo é suportado pela Service Bus ao nível do protocolo AMQP, mas não exposto em nenhuma das APIs do cliente.

O caso regular é que as mensagens estão a ser enviadas de forma instável, e o recetor indica então aceitação ou rejeição usando a *disposição* performativa. A rejeição ocorre quando o recetor não pode aceitar a mensagem por qualquer motivo, e a mensagem de rejeição contém informações sobre a razão, que é uma estrutura de erro definida pela AMQP. Se as mensagens forem rejeitadas devido a erros internos no interior do Service Bus, o serviço devolve informações extra dentro dessa estrutura que podem ser usadas para fornecer dicas de diagnóstico para apoiar o pessoal se estiver a apresentar pedidos de apoio. Aprende-se mais detalhes sobre erros mais tarde.

Uma forma especial de rejeição é o estado *libertado,* o que indica que o recetor não tem qualquer objeção técnica à transferência, mas também não tem interesse em liquidar a transferência. Esse caso existe, por exemplo, quando uma mensagem é entregue a um cliente do Service Bus, e o cliente opta por "abandonar" a mensagem porque não consegue realizar o trabalho resultante do processamento da mensagem; a entrega da mensagem em si não é culpa. Uma variação desse estado é o estado *modificado,* que permite alterações na mensagem à medida que é lançada. Este estado não é usado pela Service Bus neste momento.

A especificação AMQP 1.0 define um estado de disposição adicional chamado *recebido,* que ajuda especificamente a lidar com a recuperação de ligações. A recuperação de ligações permite reconstituir o estado de um link e quaisquer entregas pendentes em cima de uma nova ligação e sessão, quando a ligação e a sessão anteriores foram perdidas.

O Ônibus de Serviço não suporta a recuperação de ligações; se o cliente perder a ligação ao Service Bus com uma transferência de mensagem não resolvida pendente, essa transferência de mensagem perde-se, e o cliente deve voltar a ligar, restabelecer o link e voltar a tentar a transferência.

Como tal, os Service Bus e Event Hubs suportam a transferência "pelo menos uma vez" onde o remetente pode ser assegurado para a mensagem ter sido armazenada e aceite, mas não suportam transferências "exatamente uma vez" ao nível da AMQP, onde o sistema tentará recuperar o link e continuar a negociar o estado de entrega para evitar a duplicação da transferência de mensagens.

Para compensar possíveis envios duplicados, o Service Bus suporta a deteção duplicada como uma funcionalidade opcional em filas e tópicos. A deteção duplicada regista os IDs de mensagem de todas as mensagens de entrada durante uma janela de tempo definida pelo utilizador e, em seguida, silenciosamente deixa cair todas as mensagens enviadas com os mesmos IDs de mensagem durante a mesma janela.

### <a name="flow-control"></a>Controlo de fluxo

Além do modelo de controlo de fluxo ao nível da sessão que anteriormente foi discutido, cada ligação tem o seu próprio modelo de controlo de fluxo. O controlo de fluxo de nível de sessão protege o recipiente de ter de manusear demasiados quadros ao mesmo tempo, o controlo de fluxo de nível de ligação coloca a aplicação encarregada de quantas mensagens quer lidar a partir de uma ligação e quando.

![][4]

Num link, as transferências só podem acontecer quando o remetente tem crédito de *ligação*suficiente . O crédito de ligação é um contador definido pelo recetor utilizando o executativo do *fluxo,* que é alargado a um link. Quando o remetente é atribuído crédito de ligação, tenta usar esse crédito entregando mensagens. Cada remessa decreta o crédito de ligação restante por 1. Quando o crédito de ligação é esgotado, as entregas param.

Quando o Service Bus está na função de recetor, fornece instantaneamente ao remetente um amplo crédito de ligação, para que as mensagens possam ser enviadas imediatamente. À medida que o crédito de ligação é utilizado, o Service Bus envia ocasionalmente um *fluxo* performativo ao remetente para atualizar o saldo de crédito de ligação.

Na função de remetente, a Service Bus envia mensagens para usar qualquer crédito de ligação pendente.

Uma chamada "receber" ao nível da API traduz-se num *fluxo* performativo enviado para o Service Bus pelo cliente, e o Service Bus consome esse crédito, tomando a primeira mensagem disponível e desbloqueada da fila, bloqueando-a e transferindo-a. Se não houver mensagem prontamente disponível para entrega, qualquer crédito pendente por qualquer ligação estabelecida com essa entidade em particular permanece registada por ordem de chegada, e as mensagens são bloqueadas e transferidas à medida que estiverem disponíveis, para utilizar qualquer crédito pendente.

O bloqueio de uma mensagem é divulgado quando a transferência é liquidada num dos estados terminais *aceites,* *rejeitados*ou *libertados.* A mensagem é removida do Ônibus de serviço quando o estado terminal é *aceite*. Permanece no Service Bus e é entregue ao próximo recetor quando a transferência chegar a qualquer um dos outros estados. O Service Bus move automaticamente a mensagem para a fila da carta morta da entidade quando atinge a contagem máxima de entrega permitida para a entidade devido a repetidas rejeições ou lançamentos.

Mesmo que as APIs de ônibus de serviço não exponham diretamente tal opção hoje em dia, um cliente de protocolo AMQP de nível inferior pode usar o modelo de link-credit para transformar a interação "estilo pull" de emitir uma unidade de crédito para cada pedido receber em um modelo "push-style" por emitindo um grande número de créditos de ligação e, em seguida, receber mensagens à medida que ficam disponíveis sem qualquer interação adicional. O push é suportado através da [MessageingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) ou [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) definições de propriedade. Quando não são zero, o cliente AMQP usa-o como crédito de ligação.

Neste contexto, é importante entender que o relógio para a expiração do bloqueio da mensagem dentro da entidade começa quando a mensagem é retirada da entidade, e não quando a mensagem é colocada no fio. Sempre que o cliente indique disponibilidade para receber mensagens através da emissão de crédito de ligação, espera-se, portanto, que esteja ativamente a puxar mensagens através da rede e esteja pronto para as lidar. Caso contrário, o bloqueio da mensagem pode ter expirado antes mesmo de a mensagem ser entregue. A utilização do controlo do fluxo de crédito de ligação deve refletir diretamente a disponibilidade imediata para lidar com as mensagens disponíveis enviadas para o recetor.

Em resumo, as seguintes secções fornecem uma visão geral esquemática do fluxo performativo durante diferentes interações de API. Cada secção descreve uma operação lógica diferente. Algumas dessas interações podem ser "preguiçosas", o que significa que só podem ser realizadas quando necessário. A criação de um remetente de mensagens pode não causar uma interação de rede até que a primeira mensagem seja enviada ou solicitada.

As setas na tabela a seguir mostram a direção de fluxo performativo.

#### <a name="create-message-receiver"></a>Criar recetor de mensagens

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**recetor,**<br/>source={entity name},<br/>target={id de ligação ao cliente}<br/>) |Cliente anexa a entidade como recetor |
| Respostas do Ônibus de serviço anexando o seu fim do link |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**remetente,**<br/>source={entity name},<br/>target={id de ligação ao cliente}<br/>) |

#### <a name="create-message-sender"></a>Criar remetente de mensagens

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**remetente,**<br/>source={client link ID},<br/>target={entity name}<br/>) |Sem ação |
| Sem ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**recetor,**<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Criar remetente de mensagem (erro)

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**remetente,**<br/>source={client link ID},<br/>target={entity name}<br/>) |Sem ação |
| Sem ação |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**recetor,**<br/>fonte=nulo,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>fechado=**verdadeiro,**<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Recetor/remetente de mensagens próximas

| Cliente | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>fechado=**verdadeiro**<br/>) |Sem ação |
| Sem ação |<-- detach(<br/>handle={numeric handle},<br/>fechado=**verdadeiro**<br/>) |

#### <a name="send-success"></a>Enviar (sucesso)

| Cliente | Service Bus |
| --- | --- |
| -> transferência.<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>liquidado= falso,,mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |Sem ação |
| Sem ação |E disposição.<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>liquidado=**verdadeiro,**<br/>estado=**aceite**<br/>) |

#### <a name="send-error"></a>Enviar (erro)

| Cliente | Service Bus |
| --- | --- |
| -> transferência.<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>liquidado= falso,,mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |Sem ação |
| Sem ação |E disposição.<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>liquidado=**verdadeiro,**<br/>estado=**rejeitado**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Receber

| Cliente | Service Bus |
| --- | --- |
| -> fluxo.<br/>link-credit=1<br/>) |Sem ação |
| Sem ação |< transferência.<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| -> disposição.<br/>role=**recetor,**<br/>first={delivery ID},<br/>last={delivery ID},<br/>liquidado=**verdadeiro,**<br/>estado=**aceite**<br/>) |Sem ação |

#### <a name="multi-message-receive"></a>Receção de mensagens múltiplas

| Cliente | Service Bus |
| --- | --- |
| -> fluxo.<br/>link-credit=3<br/>) |Sem ação |
| Sem ação |< transferência.<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| Sem ação |< transferência.<br/>delivery-id={handle numeric+1},<br/>delivery-tag={binary handle},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| Sem ação |< transferência.<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| -> disposição.<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>liquidado=**verdadeiro,**<br/>estado=**aceite**<br/>) |Sem ação |

### <a name="messages"></a>Mensagens

As seguintes secções explicam quais as propriedades das secções de mensagens AMQP padrão são usadas pelo Service Bus e como mapeiam para o conjunto de API do ônibus de serviço.

Qualquer propriedade que a aplicação precise de definir deve ser mapeada para o mapa de `application-properties` da AMQP.

#### <a name="header"></a>cabeçalho

| Nome do Campo | Utilização | Nome API |
| --- | --- | --- |
| durable |- |- |
| prioridade |- |- |
| ttl |Hora de viver para esta mensagem |[TimetoLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| delivery-count |- |[Contagem de entregas](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>propriedades

| Nome do Campo | Utilização | Nome API |
| --- | --- | --- |
| mensagem-id |Identificador de formulário livre definido para aplicação para esta mensagem. Usado para deteção de duplicados. |[MensagemId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identificador de utilizador definido por aplicação, não interpretado pela Service Bus. |Não acessível através da API de ônibus de serviço. |
| para |Identificador de destino definido por aplicação, não interpretado pela Service Bus. |[Para](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Identificador de finalde mensagem definido pela aplicação, não interpretado pela Service Bus. |[Etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| resposta-a |Indicador de rota de resposta definido pela aplicação, não interpretado pela Service Bus. |[Resposta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlação-id |Identificador de correlação definido pela aplicação, não interpretado pela Service Bus. |[Correlalálada](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tipo de conteúdo |Indicador de tipo de conteúdo definido pela aplicação para o corpo, não interpretado pela Service Bus. |[Tipo de conteúdo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Indicador de codificação de conteúdo definido pela aplicação para o corpo, não interpretado pela Service Bus. |Não acessível através da API de ônibus de serviço. |
| absolute-expiry-time |Declara em que instante absoluta a mensagem expira. Ignorado na entrada (cabeçalho TTL é observado), autoritário na saída. |[ExpiraatUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tempo de criação |Declara nessa altura que a mensagem foi criada. Não usado pela Service Bus |Não acessível através da API de ônibus de serviço. |
| grupo-id |Identificador definido pela aplicação para um conjunto de mensagens relacionados. Usado para sessões de ônibus de serviço. |[SessãoId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |Contador identificando o número relativo da sequência da mensagem dentro de uma sessão. Ignorado pelo Autocarro de Serviço. |Não acessível através da API de ônibus de serviço. |
| reply-to-group-id |- |[RespostaToSessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Anotações de mensagens

Existem poucas outras propriedades de mensagens de ônibus de serviço, que não fazem parte das propriedades da mensagem AMQP, e são transmitidas como `MessageAnnotations` na mensagem.

| Chave do mapa de anotação | Utilização | Nome API |
| --- | --- | --- |
| x-opt-schedule-enqueue-time | Declara em que altura a mensagem deve aparecer na entidade |[AgendadoEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Chave definida pela aplicação que dita em que divisória a mensagem deve aterrar. | [Chave da Partilha](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Valor de chave de divisória definido pela aplicação quando uma transação deve ser usada para enviar mensagens através de uma fila de transferências. | [ViapartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Tempo UTC definido pelo serviço que representa o tempo real de enquecência da mensagem. Ignorado na entrada. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | Número único definido pelo serviço atribuído a uma mensagem. | [Número de sequência](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Número de sequência enqueuada definido pelo serviço da mensagem. | [Número enqueuedSequence](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-bloqueado até | Definido pelo serviço. A data e a hora até à qual a mensagem será bloqueada na fila/subscrição. | [LockUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-fonte | Definido de serviço. Se a mensagem for recebida da fila da carta morta, a fonte da mensagem original. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Capacidade de transação

Uma transação agrupa duas ou mais operações em conjunto num âmbito de execução. Por natureza, essa transação deve assegurar que todas as operações pertencentes a um determinado grupo de operações tenham êxito ou falhem conjuntamente.
As operações são agrunadas por um identificador `txn-id`.

Para interação transacional, o cliente atua como um `transaction controller` , que controla as operações que devem ser agrunadas. O Serviço de Autocarros de Serviço funciona como um `transactional resource` e realiza o trabalho conforme solicitado pelo `transaction controller`.

O cliente e o serviço comunicam ao longo de um `control link`, que é estabelecido pelo cliente. As mensagens `declare` e `discharge` são enviadas pelo controlador sobre o link de controlo para alocar e concluir transações respectivamente (não representam a demarcação do trabalho transacional). O envio/receção real não é realizado neste link. Cada operação transacional solicitada é explicitamente identificada com o `txn-id` desejado e, portanto, pode ocorrer em qualquer ligação na Ligação. Se a ligação de controlo for fechada enquanto existem transações não descarregadas que criou, todas essas transações serão imediatamente reroladas e as tentativas de realizar mais trabalhos transacionais sobre elas conduzirão a falhas. As mensagens no link de controlo não devem ser pré-resolvidas.

Todas as ligações têm de iniciar a sua própria ligação de controlo para poderem iniciar e terminar as transações. O serviço define um alvo especial que funciona como um `coordinator`. O cliente/controlador estabelece uma ligação de controlo a este alvo. A ligação de controlo está fora do limite de uma entidade, ou seja, a mesma ligação de controlo pode ser usada para iniciar e descarregar transações para várias entidades.

#### <a name="starting-a-transaction"></a>Iniciar uma transação

Para começar o trabalho transacional. o controlador deve obter um `txn-id` do coordenador. Fá-lo enviando uma mensagem tipo `declare`. Se a declaração for bem sucedida, o coordenador responde com um resultado de disposição, que transporta a `txn-id`atribuída .

| Cliente (Controlador) | | Ônibus de serviço (Coordenador) |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**remetente,**<br/>alvo=**Coordenador**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordenador()<br/>) |
| transfer(<br/>entrega-id=0, ...)<br/>{ AmqpValue **(Declarar)}**| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>estado=**Declarado**(<br/>**txn-id**={id de transação}<br/>))|

#### <a name="discharging-a-transaction"></a>Descarregar uma transação

O controlador conclui o trabalho transacional enviando uma mensagem `discharge` ao coordenador. O controlador indica que pretende comprometer ou reverter o trabalho transacional, colocando a bandeira `fail` no organismo de descarga. Se o coordenador não conseguir completar a quitação, a mensagem é rejeitada com este resultado que transporta a `transaction-error`.

> Nota: fail=true refere-se a Reversão de uma transação, e fail=falso refere-se a Commit.

| Cliente (Controlador) | | Ônibus de serviço (Coordenador) |
| --- | --- | --- |
| transfer(<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declarar()}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>state=Declared(<br/>txn-id={id de transação}<br/>))|
| | . . . <br/>Trabalho transacional<br/>em outros links<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue ( AmqpValue).<br/>**Quitação (txn-id=0,<br/>fail=falso)** )}| ------> |  |
| | <------ | disposição. <br/> first=57, last=57, <br/>estado=**Aceite()** )|

#### <a name="sending-a-message-in-a-transaction"></a>Envio de uma mensagem numa transação

Todo o trabalho transacional é feito com o estado de entrega transacional `transactional-state` que transporta o txn-id. No caso de envio de mensagens, o estado transacional é transportado pelo quadro de transferência da mensagem. 

| Cliente (Controlador) | | Ônibus de serviço (Coordenador) |
| --- | --- | --- |
| transfer(<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declarar()}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>state=Declared(<br/>txn-id={id de transação}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**estado=<br/>TransactionalState(<br/>txn-id=0)** )<br/>{ carga útil }| ------> |  |
| | <------ | disposição. <br/> first=1, last=1, <br/>estado=**TransactionalState(<br/>txn-id=0,<br/>resultado=Aceite()**|

#### <a name="disposing-a-message-in-a-transaction"></a>Eliminação de uma mensagem numa transação

A disposição da mensagem inclui operações como `Complete` / `Abandon` / `DeadLetter` `Defer` / . Para efetuar estas operações no âmbito de uma transação, passe a `transactional-state` com a disposição.

| Cliente (Controlador) | | Ônibus de serviço (Coordenador) |
| --- | --- | --- |
| transfer(<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declarar()}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>state=Declared(<br/>txn-id={id de transação}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ carga útil }|  
| disposição. <br/> first=11, last=11, <br/>estado=**TransactionalState(<br/>txn-id=0,<br/>resultado=Aceite()**| ------> |


## <a name="advanced-service-bus-capabilities"></a>Capacidades avançadas de ônibus de serviço

Esta secção abrange capacidades avançadas do Azure Service Bus que se baseiam em projetos de extensões à AMQP, atualmente em desenvolvimento no Comité Técnico da AmQP da OASIS. A Service Bus implementa as versões mais recentes destes projetos e adota alterações introduzidas à medida que esses projetos atingem o estatuto padrão.

> [!NOTE]
> As operações avançadas de mensagens de ônibus de serviço são suportadas através de um padrão de pedido/resposta. Os detalhes destas operações são descritos no artigo [AMQP 1.0 no Service Bus: operações baseadas em pedidos de resposta.](service-bus-amqp-request-response.md)
> 
> 

### <a name="amqp-management"></a>Gestão AMQP

A especificação de gestão da AMQP é a primeira do projeto de extensões discutida neste artigo. Esta especificação define um conjunto de protocolos em camadas em cima do protocolo AMQP que permitem interações de gestão com a infraestrutura de mensagens sobre amqp. A especificação define operações genéricas como *criar,* *ler,* *atualizar*e *excluir* para gerir entidades dentro de uma infraestrutura de mensagens e um conjunto de operações de consulta.

Todos esses gestos requerem uma interação de pedido/resposta entre o cliente e a infraestrutura de mensagens, e por isso a especificação define como modelar esse padrão de interação em cima da AMQP: o cliente conecta-se à infraestrutura de mensagens, inicia uma sessão e, em seguida, cria um par de links. Num link, o cliente atua como remetente e, por outro, atua como recetor, criando assim um par de links que podem funcionar como um canal bidirecional.

| Operação Lógica | Cliente | Service Bus |
| --- | --- | --- |
| Criar o Caminho de Resposta ao Pedido |--> attach(<br/>name={*link name*},<br/>handle={*cabo numérico*},<br/>role=**remetente,**<br/>fonte=**nulo,**<br/>target=”myentity/$management”<br/>) |Sem ação |
| Criar o Caminho de Resposta ao Pedido |Sem ação |\<- anexar.<br/>name={*link name*},<br/>handle={*cabo numérico*},<br/>role=**recetor,**<br/>fonte=nulo,<br/>target="myentity"<br/>) |
| Criar o Caminho de Resposta ao Pedido |--> attach(<br/>name={*link name*},<br/>handle={*cabo numérico*},<br/>role=**recetor,**<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| Criar o Caminho de Resposta ao Pedido |Sem ação |\<- anexar.<br/>name={*link name*},<br/>handle={*cabo numérico*},<br/>role=**remetente,**<br/>fonte="myentity",<br/>target=”myclient$id”<br/>) |

Tendo esse par de links em vigor, a implementação de pedido/resposta é simples: um pedido é uma mensagem enviada a uma entidade dentro da infraestrutura de mensagens que compreende este padrão. Nessa mensagem de pedido, o campo *de resposta ao* campo na secção de *propriedades* está definido para o *identificador-alvo* para o link em que deve dar a resposta. A entidade de manuseamento processa o pedido e, em seguida, entrega a resposta sobre o link cujo *identificador-alvo* corresponde ao identificador de *resposta* indicado.

O padrão requer, obviamente, que o contentor do cliente e o identificador gerado pelo cliente para o destino de resposta sejam únicos em todos os clientes e, por razões de segurança, também são difíceis de prever.

As trocas de mensagens utilizadas para o protocolo de gestão e para todos os outros protocolos que utilizam o mesmo padrão acontecem ao nível da aplicação; não definem novos gestos de protocolo AMQP. Isso é intencional, para que as aplicações possam tirar partido imediato destas extensões com pilhas AMQP 1.0 compatíveis.

A Service Bus não implementa atualmente nenhuma das principais características da especificação de gestão, mas o padrão de pedido/resposta definido pela especificação de gestão é fundamental para a funcionalidade de segurança baseada em sinistros e para quase todas as capacidades avançadas discutidas nas seguintes secções:

### <a name="claims-based-authorization"></a>Autorização baseada em sinistros

O projeto de especificação amqp claims-based-authorization (CBS) baseia-se no padrão de pedido/resposta da especificação de gestão e descreve um modelo generalizado para como usar fichas de segurança federadas com AMQP.

O modelo de segurança padrão da AMQP discutido na introdução baseia-se no SASL e integra-se com o aperto de mão de ligação AMQP. A utilização do SASL tem a vantagem de fornecer um modelo extensível para o qual foi definido um conjunto de mecanismos a partir do qual qualquer protocolo que se apoie formalmente no SASL pode beneficiar. Entre esses mecanismos estão "PLAIN" para transferência de nomes de utilizadores e palavras-passe, "EXTERNAL" para se ligar à segurança ao nível do TLS, "ANONYMOUS" para expressar a ausência de autenticação/autorização explícita, e uma ampla variedade de mecanismos adicionais que permitem a autenticação de passagem e/ou credenciais de autorização ou fichas.

A integração SASL da AMQP tem duas desvantagens:

* Todas as credenciais e fichas são reparadas à ligação. Uma infraestrutura de mensagens pode querer fornecer um controlo de acesso diferenciado numa base por entidade; por exemplo, permitindo que o portador de um símbolo envie para a fila A, mas não para fila B. Com o contexto de autorização ancorado na ligação, não é possível usar uma única ligação e ainda usar diferentes fichas de acesso para fila A e fila B.
* As fichas de acesso são normalmente válidas apenas por um tempo limitado. Esta validade requer que o utilizador readquira periodicamente fichas e proporciona ao emitente simbólico que se recuse a emitir um novo sinal se as permissões de acesso do utilizador tiverem mudado. As ligações AMQP podem durar longos períodos de tempo. O modelo SASL apenas oferece a oportunidade de definir um símbolo no tempo de ligação, o que significa que a infraestrutura de mensagens tem que desligar o cliente quando o token expirar ou precisa aceitar o risco de permitir a comunicação contínua com um cliente que é Os direitos de acesso podem ter sido revogados intercalares.

A especificação AMQP CBS, implementada pela Service Bus, permite uma salção de saúde elegante para ambas as questões: Permite que um cliente associe fichas de acesso a cada nó, e atualize esses tokens antes de expirarem, sem interromper o fluxo de mensagens.

A CBS define um nó de gestão virtual, chamado *$cbs,* a ser fornecido pela infraestrutura de mensagens. O nó de gestão aceita tokens em nome de quaisquer outros nódosos na infraestrutura de mensagens.

O gesto protocole é uma troca de pedidos/resposta supor a especificação de gestão. Isto significa que o cliente estabelece um par de ligações com o nó *$cbs* e depois passa um pedido no link de saída, e depois aguarda a resposta no link de entrada.

A mensagem de pedido tem as seguintes propriedades de aplicação:

| Chave | Opcional | Tipo de valor | Conteúdos de Valor |
| --- | --- | --- | --- |
| operation |Não |string |**put-token** |
| tipo |Não |string |O tipo de ficha que está a ser colocada. |
| nome |Não |string |O "público" ao qual o símbolo se aplica. |
| Expiração |Sim |carimbo de data/hora |O tempo de validade do símbolo. |

O *nome* imóvel identifica a entidade com a qual o símbolo deve ser associado. No Ônibus de Serviço é o caminho para a fila, ou tópico/subscrição. A propriedade *do tipo* identifica o tipo de símbolo:

| Tipo token | Descrição do token | Tipo de corpo | Notas |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |Valor AMQP (cadeia) |Ainda não está disponível. |
| amqp:swt |Token Web Simples (SWT) |Valor AMQP (cadeia) |Apenas suportado para fichas SWT emitidas pela AAD/ACS |
| servicebus.windows.net:sastoken |Bus de serviço SAS Token |Valor AMQP (cadeia) |- |

Tokens conferem direitos. O Service Bus conhece três direitos fundamentais: "Enviar" permite o envio, "Ouvir" permite receber e "Gerir" permite manipular entidades. Os tokens SWT emitidos pela AAD/ACS incluem explicitamente esses direitos como reclamações. Os tokens Service Bus SAS referem-se a regras configuradas no espaço de nome ou entidade, e essas regras estão configuradas com direitos. Assinar o símbolo com a chave associada a essa regra faz com que o símbolo expresse os respetivos direitos. O símbolo associado a uma entidade que usa *put-token* permite ao cliente conectado interagir com a entidade de acordo com os direitos simbólicos. Um link em que o cliente assume a função de *remetente* requer o direito "Enviar"; assumir a função de *recetor* requer o direito "Ouvir".

A mensagem de resposta tem os seguintes valores *de propriedades de aplicação*

| Chave | Opcional | Tipo de valor | Conteúdos de Valor |
| --- | --- | --- | --- |
| status-código |Não |int |Código de resposta HTTP **[RFC2616]** . |
| status-description |Sim |string |Descrição do estado. |

O cliente pode ligar repetidamente para *put-token* e para qualquer entidade na infraestrutura de mensagens. As fichas são reparadas ao cliente atual e ancoradas na ligação atual, o que significa que o servidor deixa cair quaisquer fichas retidas quando a ligação cai.

A atual implementação do Ônibus de Serviço só permite a CBS em conjunto com o método SASL "ANONYMOUS". Uma ligação SSL/TLS deve existir sempre antes do aperto de mão SASL.

O mecanismo ANONYMOUS deve, portanto, ser suportado pelo cliente AMQP 1.0 escolhido. O acesso anónimo significa que o aperto de mão inicial de ligação, incluindo a criação da sessão inicial, acontece sem que o Service Bus saiba quem está a criar a ligação.

Uma vez estabelecida a ligação e a sessão, anexar as ligações ao nó *$cbs* e enviar o pedido de *"put-token"* são as únicas operações permitidas. Um token válido deve ser definido com sucesso usando um pedido de ficha *de* colocação para algum nó de entidade no prazo de 20 segundos após a ligação ter sido estabelecida, caso contrário a ligação é abandonada unilateralmente pela Service Bus.

O cliente é posteriormente responsável por acompanhar a expiração do token. Quando um token expira, o Service Bus rapidamente deixa cair todos os links na ligação à respetiva entidade. Para evitar problemas, o cliente pode substituir o símbolo para o nó por um novo a qualquer momento através do nó *de gestão de $cbs* virtual com o mesmo gesto de *put-token,* e sem se meter no caminho do tráfego de carga útil que flui em diferentes links.

### <a name="send-via-functionality"></a>Funcionalidade de envio via

[Send-via/Transfer sender](service-bus-transactions.md#transfers-and-send-via) é uma funcionalidade que permite que o ônibus de serviço reencaminha uma determinada mensagem para uma entidade de destino através de outra entidade. Esta funcionalidade é utilizada para realizar operações entre entidades numa única transação.

Com esta funcionalidade, cria-se um remetente e estabelece a ligação à `via-entity`. Ao estabelecer o link, são passadas informações adicionais para estabelecer o verdadeiro destino das mensagens/transferências neste link. Uma vez que o anexo tenha sido bem sucedido, todas as mensagens enviadas neste link são automaticamente reencaminhadas para a *entidade de destino* através de *via-entidade*. 

> Nota: A autenticação tem de ser realizada tanto para *a entidade via-entidade* como para a *entidade de destino* antes de estabelecer este link.

| Cliente | | Service Bus |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target= **{via-entidade}**<br/>**propriedades=mapa [(((<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] )** | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>propriedades=mapa [.<br/>com.microsoft:transfer-destination-address=<br/>{entidade de destino} )] ) |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a AMQP, visite os seguintes links:

* [Visão geral do ônibus de serviço AMQP]
* [Suporte AMQP 1.0 para filas e tópicos de partição de ônibus de serviço]
* [AMQP em ônibus de serviço para servidor windows]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Visão geral do ônibus de serviço AMQP]: service-bus-amqp-overview.md
[Suporte AMQP 1.0 para filas e tópicos de partição de ônibus de serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP em ônibus de serviço para servidor windows]: https://msdn.microsoft.com/library/dn574799.aspx
