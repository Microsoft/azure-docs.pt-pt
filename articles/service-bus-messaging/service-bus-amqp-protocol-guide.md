---
title: AMQP 1.0 no guia de protocolos Azure Service Bus and Event Hubs | Microsoft Docs
description: Guia protocolar para expressões e descrição de AMQP 1.0 em Azure Service Bus and Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2154221ebfe69b659ff83100ed614133e178ccdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624494"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 em Azure Service Bus and Event Hubs guia de protocolo

O Protocolo de Fila de Mensagens Avançadas 1.0 é um protocolo de enquadramento e transferência padronizado para mensagens assíncronos, seguras e fiavelmente transferidas entre duas partes. É o protocolo principal da Azure Service Bus Messaging e da Azure Event Hubs.  

AMQP 1.0 é o resultado de uma ampla colaboração da indústria que reuniu fornecedores de middleware, como a Microsoft e a Red Hat, com muitos utilizadores de middleware de mensagens como JP Morgan Chase a representar a indústria de serviços financeiros. O fórum de normalização técnica para o protocolo amQP e especificações de extensão é o OASIS, e obteve a aprovação formal como padrão internacional como ISO/IEC 19494:2014. 

## <a name="goals"></a>Objetivos

Este artigo resume os conceitos fundamentais da especificação de mensagens AMQP 1.0 ao longo das especificações de extensão desenvolvidas pelo [Comité Técnico da AMQP OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) e explica como a Azure Service Bus implementa e baseia-se nestas especificações.

O objetivo é que qualquer desenvolvedor que utilize qualquer pilha de cliente AMQP 1.0 existente em qualquer plataforma para poder interagir com a Azure Service Bus via AMQP 1.0.

Pilhas amqp 1.0 de uso geral comum, tais como [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) ou [AMQP.NET Lite,](https://github.com/Azure/amqpnetlite)implemente todos os elementos de protocolo AMQP 1.0 de núcleo, como sessões ou links. Esses elementos fundamentais são, por vezes, envoltos numa API de nível superior; Apache Protão oferece ainda dois, a api-de-mensageiro imperativa e a API do reator reativo.

Na discussão seguinte, assumimos que a gestão de ligações, sessões e links amQP e o manuseamento de transferências de quadros e controlo de fluxo são tratados pela respetiva stack (como Apache Proton-C) e não requerem muita atenção específica dos desenvolvedores de aplicações. Assumimos abstratamente a existência de alguns primitivos da API, como a capacidade de se conectar, e de criar alguma forma de objetos de abstração *de remetente* e *recetor,* que depois têm alguma forma `send()` e `receive()` operações, respectivamente.

Ao discutir capacidades avançadas da Azure Service Bus, como a navegação de mensagens ou a gestão de sessões, essas funcionalidades são explicadas em termos AMQP, mas também como uma pseudo-implementação em camadas em cima desta abstração assumida da API.

## <a name="what-is-amqp"></a>O que é AMQP?

AMQP é um protocolo de enquadramento e transferência. Enquadramento significa que fornece estrutura para fluxos binários de dados que fluem em qualquer direção de uma ligação de rede. A estrutura fornece delimitação para blocos distintos de *dados, chamados quadros,* a serem trocados entre as partes ligadas. As capacidades de transferência asseguram que ambas as partes comunicantes possam estabelecer um entendimento partilhado sobre quando os quadros serão transferidos e quando as transferências serão consideradas completas.

Ao contrário das versões de rascunho expiradas anteriormente produzidas pelo grupo de trabalho AMQP que ainda estão a ser utilizadas por alguns corretores de mensagens, o protocolo final do grupo de trabalho e o protocolo AMQP 1.0 padronizado não prescreve a presença de um corretor de mensagens ou de qualquer topologia específica para entidades dentro de um corretor de mensagens.

O protocolo pode ser usado para comunicação par-a-par simétrica, para interação com corretores de mensagens que suportam filas e publicam/subscrevam entidades, como faz a Azure Service Bus. Também pode ser usado para interação com a infraestrutura de mensagens onde os padrões de interação são diferentes das filas regulares, como é o caso dos Azure Event Hubs. Um Event Hub age como uma fila quando os eventos são enviados para ele, mas age mais como um serviço de armazenamento em série quando os eventos são lidos a partir dele; assemelha-se um pouco a uma unidade de fita. O cliente escolhe uma compensação para o fluxo de dados disponível e é então servido todos os eventos desde esse offset até ao mais recente disponível.

O protocolo AMQP 1.0 foi concebido para ser extensível, permitindo especificações adicionais para aumentar as suas capacidades. As três especificações de extensão discutidas neste documento ilustram-no. Para a comunicação sobre a infraestrutura HTTPS/WebSockets existente, configurar as portas nativas amQP TCP pode ser difícil. Uma especificação de ligação define como colocar AMQP em camadas sobre WebSockets. Para interagir com a infraestrutura de mensagens de forma solicitada/resposta para fins de gestão ou para fornecer funcionalidade avançada, a especificação de gestão amQP define os primitivos de interação básica necessários. Para a integração do modelo de autorização federada, a especificação de segurança baseada em sinistros amQP define como associar e renovar fichas de autorização associadas a links.

## <a name="basic-amqp-scenarios"></a>Cenários básicos de AMQP

Esta secção explica o uso básico de AMQP 1.0 com a Azure Service Bus, que inclui a criação de ligações, sessões e links, e a transferência de mensagens de e para entidades de Service Bus, tais como filas, tópicos e subscrições.

A fonte mais autoritária para saber como funciona a AMQP é a [especificação AMQP 1.0,](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)mas a especificação foi escrita para orientar precisamente a implementação e não para ensinar o protocolo. Esta secção centra-se na introdução da terminologia necessária para descrever como o Service Bus utiliza AMQP 1.0. Para uma introdução mais abrangente à AMQP, bem como uma discussão mais alargada sobre AMQP 1.0, você pode rever [este curso de vídeo][this video course].

### <a name="connections-and-sessions"></a>Conexões e sessões

AMQP chama os *recipientes* de programas de comunicação; os que contêm *nós,* que são as entidades comunicantes dentro desses contentores. Uma fila pode ser um nó. AmQP permite multiplexing, para que uma única ligação possa ser usada para muitos caminhos de comunicação entre nós; por exemplo, um cliente de aplicação pode receber simultaneamente de uma fila e enviar para outra fila sobre a mesma ligação de rede.

![Diagrama mostrando Sessões e Ligações entre recipientes.][1]

A ligação à rede está assim ancorada no recipiente. É iniciado pelo contentor na função do cliente fazendo uma ligação de tomada TCP de saída a um recipiente na função recetora, que ouve e aceita ligações TCP de entrada. O aperto de mão de ligação inclui a negociação da versão do protocolo, a declaração ou negociação da utilização da Segurança do Nível de Transporte (TLS/SSL), e um aperto de mão de autenticação/autorização no âmbito de ligação baseado no SASL.

O Azure Service Bus requer sempre a utilização de TLS. Suporta ligações sobre a porta 5671 da TCP, através das quais a ligação TCP é primeiramente sobreposta com TLS antes de entrar no aperto de mão do protocolo AMQP, e também suporta ligações sobre a porta TCP 5672, através das quais o servidor oferece imediatamente uma atualização obrigatória da ligação à TLS utilizando o modelo prescrito pela AMQP. A ligação AMQP WebSockets cria um túnel sobre a porta TCP 443 que é então equivalente às ligações AMQP 5671.

Após a configuração da ligação e tLS, a Service Bus oferece duas opções de mecanismo SASL:

* SASL PLAIN é comumente usado para passar o nome de utilizador e credenciais de senha para um servidor. A Service Bus não tem contas, mas denominadas [regras de Segurança de Acesso Partilhado,](service-bus-sas.md)que conferem direitos e estão associadas a uma chave. O nome de uma regra é usado como nome de utilizador e a chave (como texto codificado base64) é usada como palavra-passe. Os direitos associados à regra escolhida regem as operações permitidas na ligação.
* O SASL ANONYMOUS é utilizado para contornar a autorização SASL quando o cliente quer utilizar o modelo de segurança baseada em sinistros (CBS) que é descrito mais tarde. Com esta opção, uma ligação ao cliente pode ser estabelecida anonimamente por um curto período de tempo durante o qual o cliente só pode interagir com o ponto final da CBS e o aperto de mão CBS deve ser concluído.

Após a ligação de transporte ser estabelecida, os contentores declaram o tamanho máximo do quadro que estão dispostos a manusear, e após um intervalo de tempo inativo, desligar-se-ão unilateralmente se não houver atividade na ligação.

Também declaram quantos canais simultâneos são suportados. Um canal é um caminho de transferência virtual unidirecional, de saída e virtual no topo da ligação. Uma sessão requer um canal de cada um dos recipientes interligados para formar um caminho de comunicação bidis.

As sessões têm um modelo de controlo de fluxo baseado em janelas; quando uma sessão é criada, cada parte declara quantos quadros está disposto a aceitar na sua janela de receção. À medida que as partes trocam quadros, os quadros transferidos preenchem essa janela e as transferências param quando a janela está cheia e até que a janela seja reposta ou expandida utilizando o *fluxo performativo* *(performativo* é o termo AMQP para gestos de nível protocolar trocados entre as duas partes).

Este modelo baseado em janelas é aproximadamente análogo ao conceito TCP de controlo de fluxo baseado em janelas, mas ao nível da sessão dentro da tomada. O conceito do protocolo de permitir múltiplas sessões simultâneas existe para que o tráfego de alta prioridade possa ser ultrapassado pelo tráfego normal acelerado, como numa via expressa de autoestrada.

A Azure Service Bus utiliza atualmente exatamente uma sessão para cada ligação. O tamanho máximo do quadro do Service Bus é de 262.144 bytes (bytes de 256-K) para o Service Bus Standard. São 1.048.576 (1 MB) para Service Bus Premium e Event Hubs. A Service Bus não impõe janelas de estrangulamento ao nível da sessão específicas, mas repõe a janela regularmente como parte do controlo de fluxo de nível de ligação (ver [secção seguinte).](#links)

Ligações, canais e sessões são efémeras. Se a ligação subjacente colapsar, as ligações, o túnel TLS, o contexto de autorização SASL e as sessões devem ser restabelecidas.

### <a name="amqp-outbound-port-requirements"></a>Requisitos de porta de saída amQP

Os clientes que utilizam ligações AMQP sobre TCP exigem que as portas 5671 e 5672 sejam abertas na firewall local. Juntamente com estas portas, poderá ser necessário abrir portas adicionais se a função [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) estiver ativada. `EnableLinkRedirect` é uma nova funcionalidade de mensagens que ajuda a saltar um salto enquanto recebe mensagens, ajudando assim a aumentar a produção. O cliente começaria a comunicar diretamente com o serviço back-end sobre a gama de portas 104XX, como mostrado na imagem seguinte. 

![Lista de portos de destino][4]

Um cliente .NET falharia com uma SocketException ("Foi feita uma tentativa de aceder a uma tomada de uma forma proibida pelas suas permissões de acesso") se estas portas forem bloqueadas pela firewall. A funcionalidade pode ser desativada por definição `EnableAmqpLinkRedirect=false` na cadeia de ligação, o que obriga os clientes a comunicarem com o serviço remoto sobre a porta 5671.


### <a name="links"></a>Ligações

AMQP transfere mensagens por links. Um link é uma via de comunicação criada ao longo de uma sessão que permite transferir mensagens numa direção; a negociação do estado de transferência é sobre o link e bid-direcional entre as partes ligadas.

![Screenshot mostrando uma Sessão com uma ligação de ligação entre dois recipientes.][2]

Os links podem ser criados por qualquer um dos contentores a qualquer momento e ao longo de uma sessão existente, o que torna a AMQP diferente de muitos outros protocolos, incluindo HTTP e MQTT, onde o início das transferências e trajeto de transferência é um privilégio exclusivo da parte que cria a ligação à tomada.

O recipiente de iniciação de ligações pede ao recipiente oposto que aceite um link e escolhe uma função de remetente ou recetor. Portanto, qualquer um dos recipientes pode iniciar a criação de caminhos de comunicação unidirecionais ou bidirecionais, com este último modelado como pares de ligações.

Os links são nomeados e associados com nós. Como declarado no início, os nós são as entidades comunicantes dentro de um contentor.

Em Service Bus, um nó é diretamente equivalente a uma fila, um tópico, uma subscrição ou uma subquesão de carta morta de uma fila ou subscrição. O nome do nó usado na AMQP é, portanto, o nome relativo da entidade dentro do espaço de nomes do Service Bus. Se for nomeada uma `myqueue` fila, é também o nome do nó AMQP. Uma subscrição de tópico segue a convenção http API por ser classificada numa recolha de recursos de "subscrições" e, assim, um **sub** de subscrição sobre um tópico **mytopic** tem o nome de nó AMQP **mytopic/subscrições/sub.**

O cliente de ligação também é obrigado a usar um nome de nó local para criar links; A Service Bus não é prescritiva sobre esses nomes de nó e não os interpreta. As pilhas de clientes AMQP 1.0 geralmente usam um esquema para garantir que estes nomes de nófeméricos são únicos no âmbito do cliente.

### <a name="transfers"></a>Transferências

Uma vez estabelecida uma ligação, as mensagens podem ser transferidas sobre esse link. Em AMQP, uma transferência é executada com um gesto de protocolo explícito (o *performativo de transferência)* que move uma mensagem do remetente para o recetor por um link. Uma transferência fica completa quando está "resolvida", o que significa que ambas as partes estabeleceram um entendimento partilhado sobre o resultado dessa transferência.

![Um diagrama que mostra a transferência de uma mensagem entre o Remetente e o Recetor e a disposição que resulta do mesmo.][3]

No caso mais simples, o remetente pode optar por enviar mensagens "pré-resolvidas", o que significa que o cliente não está interessado no resultado e o recetor não fornece qualquer feedback sobre o resultado da operação. Este modo é suportado pela Service Bus ao nível do protocolo AMQP, mas não exposto em nenhuma das APIs do cliente.

O caso regular é que as mensagens estão a ser enviadas por resolver e o recetor indica então aceitação ou rejeição utilizando a *disposição* performativa. A rejeição ocorre quando o recetor não pode aceitar a mensagem por qualquer motivo, e a mensagem de rejeição contém informações sobre a razão, que é uma estrutura de erro definida pela AMQP. Se as mensagens forem rejeitadas devido a erros internos dentro do Service Bus, o serviço devolve informações extra dentro dessa estrutura que podem ser usadas para fornecer dicas de diagnóstico para apoiar o pessoal se estiver a apresentar pedidos de apoio. Mais detalhes sobre erros mais tarde.

Uma forma especial de rejeição é o estado *libertado,* o que indica que o recetor não tem qualquer objeção técnica à transferência, mas também nenhum interesse em liquidar a transferência. Este caso existe, por exemplo, quando uma mensagem é entregue a um cliente da Service Bus, e o cliente opta por "abandonar" a mensagem porque não consegue realizar o trabalho resultante do processamento da mensagem; a entrega da mensagem em si não é culpa. Uma variação desse estado é o estado *modificado,* que permite alterações na mensagem à medida que é lançada. Este estado não é usado pela Service Bus neste momento.

A especificação AMQP 1.0 define um novo estado de disposição chamado *recebido,* que ajuda especificamente a lidar com a recuperação de ligações. A recuperação de ligações permite reconstituir o estado de um link e quaisquer entregas pendentes em cima de uma nova ligação e sessão, quando a ligação e sessão anteriores foram perdidas.

A Service Bus não suporta a recuperação de ligações; se o cliente perder a ligação ao Service Bus com uma transferência de mensagens pendente, essa transferência de mensagem é perdida, e o cliente deve reconectar-se, restabelecer o link e voltar a tentar a transferência.

Como tal, a Service Bus e a Event Hubs suportam a transferência "pelo menos uma vez" onde o remetente pode ser assegurado pela mensagem ter sido armazenada e aceite, mas não suporta transferências "exatamente uma vez" ao nível da AMQP, onde o sistema tentaria recuperar o link e continuar a negociar o estado de entrega para evitar a duplicação da transferência de mensagens.

Para compensar possíveis envios duplicados, o Service Bus suporta a deteção duplicada como uma característica opcional em filas e tópicos. A deteção duplicada regista os IDs de mensagem de todas as mensagens recebidas durante uma janela de tempo definida pelo utilizador e, em seguida, deixa cair silenciosamente todas as mensagens enviadas com os mesmos IDs de mensagens durante essa mesma janela.

### <a name="flow-control"></a>Controlo de fluxo

Além do modelo de controlo de fluxo de nível de sessão que previamente discutido, cada ligação tem o seu próprio modelo de controlo de fluxo. O controlo de fluxo ao nível da sessão protege o recipiente de ter de manusear demasiados quadros ao mesmo tempo, o controlo de fluxo de nível de ligação encarrega a aplicação de quantas mensagens pretende manusear a partir de um link e quando.

![Screenshot de um registo que mostra Origem, Destino, Porto fonte, Porto de Destino e Nome do Protocolo. Na primeira linha, o Porto de Destino 10401 (0x28 A 1) é delineado em preto.][4]

Num link, as transferências só podem acontecer quando o remetente tem *crédito de ligação* suficiente . O crédito de ligação é um contra-conjunto definido pelo recetor utilizando o *fluxo* performativo, que é telescópio para um link. Quando o remetente é atribuído crédito de ligação, tenta usar esse crédito entregando mensagens. Cada entrega de mensagens diminui o restante crédito de ligação por 1. Quando o crédito de ligação é usado, as entregas param.

Quando o Service Bus está na função recetora, fornece instantaneamente ao remetente um amplo crédito de ligação, para que as mensagens possam ser enviadas imediatamente. À medida que o crédito de ligação é usado, o Service Bus ocasionalmente envia um *fluxo* performativo para o remetente para atualizar o saldo de crédito de ligação.

Na função de remetente, a Service Bus envia mensagens para utilizar qualquer crédito de ligação pendente.

Uma chamada "receber" ao nível da API traduz-se num *fluxo* performativo enviado para a Service Bus pelo cliente, e a Service Bus consome esse crédito, tirando a primeira mensagem disponível e desbloqueada da fila, bloqueando-a e transferindo-a. Se não houver nenhuma mensagem prontamente disponível para entrega, qualquer crédito pendente por qualquer ligação estabelecida com essa entidade em particular permanece registada na ordem de chegada, e as mensagens são bloqueadas e transferidas à medida que ficam disponíveis, para utilizar qualquer crédito pendente.

O bloqueio de uma mensagem é divulgado quando a transferência é liquidada num dos estados terminais *aceites,* *rejeitados* ou *libertados*. A mensagem é removida da Service Bus quando o estado terminal é *aceite.* Permanece em Service Bus e é entregue ao próximo recetor quando a transferência chegar a qualquer um dos outros estados. A Service Bus move automaticamente a mensagem para a fila da cartada da entidade quando atinge a contagem máxima de entrega permitida para a entidade devido a repetidas rejeições ou lançamentos.

Mesmo que as APIs do Service Bus não exponham diretamente essa opção hoje em dia, um cliente protocolo AMQP de nível inferior pode usar o modelo de crédito de ligação para transformar a interação "pull-style" de emitir uma unidade de crédito para cada pedido de receção num modelo de "push-style", emitindo um grande número de créditos de ligação e, em seguida, receber mensagens à medida que ficam disponíveis sem qualquer interação adicional. O push é suportado através das definições de propriedade [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) ou [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) Quando não são zero, o cliente AMQP usa-o como crédito de ligação.

Neste contexto, é importante entender que o relógio para a expiração do bloqueio na mensagem dentro da entidade começa quando a mensagem é tirada da entidade, não quando a mensagem é colocada no fio. Sempre que o cliente indique disponibilidade para receber mensagens através da emissão de crédito de ligação, espera-se, portanto, que esteja a puxar ativamente mensagens através da rede e esteja pronto para lidar com elas. Caso contrário, o bloqueio de mensagem pode ter expirado antes mesmo de a mensagem ser entregue. A utilização do controlo de fluxos de links-crédito deve refletir diretamente a disponibilidade imediata para lidar com as mensagens disponíveis enviadas ao recetor.

Em resumo, as seguintes secções fornecem uma visão geral esquemática do fluxo performativo durante diferentes interações de API. Cada secção descreve uma operação lógica diferente. Algumas dessas interações podem ser "preguiçosas", o que significa que só podem ser realizadas quando necessário. A criação de um remetente de mensagens pode não causar uma interação de rede até que a primeira mensagem seja enviada ou solicitada.

As setas na tabela seguinte mostram a direção de fluxo performativa.

#### <a name="create-message-receiver"></a>Criar recetor de mensagens

| Cliente | Service Bus |
| --- | --- |
| -> anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**recetor,**<br/>nome de origem={entidade},<br/>target={link de cliente ID}<br/>) |Cliente anexa-se à entidade como recetor |
| Respostas do Service Bus anexando o seu fim do link |<- anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**remetente,**<br/>nome de origem={entidade},<br/>target={link de cliente ID}<br/>) |

#### <a name="create-message-sender"></a>Criar remetente de mensagens

| Cliente | Service Bus |
| --- | --- |
| -> anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**remetente,**<br/>ID de ligação ao cliente source={},<br/>nome target={entity}<br/>) |Sem ação |
| Sem ação |<- anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**recetor,**<br/>ID de ligação ao cliente source={},<br/>nome target={entity}<br/>) |

#### <a name="create-message-sender-error"></a>Criar remetente de mensagens (erro)

| Cliente | Service Bus |
| --- | --- |
| -> anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**remetente,**<br/>ID de ligação ao cliente source={},<br/>nome target={entity}<br/>) |Sem ação |
| Sem ação |<- anexação.<br/>nome={link name},<br/>handle={punho numérico},<br/>papel=**recetor,**<br/>fonte=nulo,<br/>target=nulo<br/>)<br/><br/><- desprender-<br/>handle={punho numérico},<br/>fechado=**verdadeiro,**<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Recetor de mensagem de fecho/remetente

| Cliente | Service Bus |
| --- | --- |
| -> desprenderem-se.<br/>handle={punho numérico},<br/>fechado=**verdadeiro**<br/>) |Sem ação |
| Sem ação |<- desprender-<br/>handle={punho numérico},<br/>fechado=**verdadeiro**<br/>) |

#### <a name="send-success"></a>Enviar (sucesso)

| Cliente | Service Bus |
| --- | --- |
| -> transferência.<br/>entrega-id={pega numérica},<br/>entrega-tag={alça binária},<br/>liquidado=falso,mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |Sem ação |
| Sem ação |<- disposição.<br/>role=recetor,<br/>first={delivery ID},<br/>ID de entrega last={},<br/>resolvido=**verdadeiro,**<br/>estado=**aceito**<br/>) |

#### <a name="send-error"></a>Enviar (erro)

| Cliente | Service Bus |
| --- | --- |
| -> transferência.<br/>entrega-id={pega numérica},<br/>entrega-tag={alça binária},<br/>liquidado=falso,mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |Sem ação |
| Sem ação |<- disposição.<br/>role=recetor,<br/>first={delivery ID},<br/>ID de entrega last={},<br/>resolvido=**verdadeiro,**<br/>estado=**rejeitado**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Receber

| Cliente | Service Bus |
| --- | --- |
| -> fluxo.<br/>link-crédito=1<br/>) |Sem ação |
| Sem ação |< transferência.<br/>entrega-id={pega numérica},<br/>entrega-tag={alça binária},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| -disposição >.<br/>papel=**recetor,**<br/>first={delivery ID},<br/>ID de entrega last={},<br/>resolvido=**verdadeiro,**<br/>estado=**aceito**<br/>) |Sem ação |

#### <a name="multi-message-receive"></a>Receber várias mensagens

| Cliente | Service Bus |
| --- | --- |
| -> fluxo.<br/>link-crédito=3<br/>) |Sem ação |
| Sem ação |< transferência.<br/>entrega-id={pega numérica},<br/>entrega-tag={alça binária},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| Sem ação |< transferência.<br/>entrega-id={alça numérica+1},<br/>entrega-tag={alça binária},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| Sem ação |< transferência.<br/>entrega-id={alça numérica+2},<br/>entrega-tag={alça binária},<br/>liquidado=**falso,**<br/>mais=**falso,**<br/>estado=**nulo,**<br/>currículo=**falso**<br/>) |
| -disposição >.<br/>role=recetor,<br/>first={delivery ID},<br/>última={entrega ID+2},<br/>resolvido=**verdadeiro,**<br/>estado=**aceito**<br/>) |Sem ação |

### <a name="messages"></a>Mensagens

As secções seguintes explicam quais as propriedades das secções padrão de mensagens AMQP são utilizadas pela Service Bus e como mapeiam para o conjunto de API do Autocarro de Serviço.

Qualquer propriedade que a aplicação precise definir deve ser mapeada para o mapa da `application-properties` AMQP.

#### <a name="header"></a>cabeçalho

| Nome do Campo | Utilização | Nome da API |
| --- | --- | --- |
| durável |- |- |
| prioridade |- |- |
| ttl |Hora de viver para esta mensagem |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| primeiro adquirente |- |- |
| contagem de entrega |- |[Entrega](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>propriedades

| Nome do Campo | Utilização | Nome da API |
| --- | --- | --- |
| mensagem id |Identificador de formulário livre definido para aplicação para esta mensagem. Usado para deteção duplicada. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identificador de utilizador definido por aplicação, não interpretado pela Service Bus. |Não acessível através da API de autocarro de serviço. |
| para |Identificador de destino definido por aplicação, não interpretado pela Service Bus. |[Para](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Identificador de finalidade de mensagem definido pela aplicação, não interpretado pela Service Bus. |[Etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| resposta a |Indicador de linha de resposta definido por aplicação, não interpretado pela Service Bus. |[RespostaTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identificador de correlação definido por aplicação, não interpretado pela Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tipo de conteúdo |Indicador de tipo de conteúdo definido para aplicação para o corpo, não interpretado pela Service Bus. |[ConteúdoType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| codificação de conteúdos |Indicador de codificação de conteúdo definido para a aplicação para o corpo, não interpretado pela Service Bus. |Não acessível através da API de autocarro de serviço. |
| prazo de absoluta expiração |Declara em que instante a mensagem expira. Ignorado na entrada (observa-se o cabeçalho TTL), autoritário na saída. |[ExpiraAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tempo de criação |Declara altura em que a mensagem foi criada. Não usado por Service Bus |Não acessível através da API de autocarro de serviço. |
| id grupo |Identificador definido por aplicação para um conjunto de mensagens relacionadas. Usado para sessões de ônibus de serviço. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| grupo-sequência |Contador identificando o número de sequência relativa da mensagem dentro de uma sessão. Ignorado por Service Bus. |Não acessível através da API de autocarro de serviço. |
| resposta-a-grupo id |- |[RespostaToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Anotações de mensagens

Existem poucas outras propriedades de mensagem de ônibus de serviço, que não fazem parte das propriedades de mensagens AMQP, e são transmitidas como `MessageAnnotations` na mensagem.

| Chave do mapa de anotação | Utilização | Nome da API |
| --- | --- | --- |
| x-opt-programado-enqueue-time | Declara em que momento a mensagem deve aparecer na entidade |[Horário DeEnqueue Hora do Tempo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | Chave definida pela aplicação que dita em que partição a mensagem deve pousar. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partição-chave | Valor chave de partição definido pela aplicação quando uma transação deve ser usada para enviar mensagens através de uma fila de transferência. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enquesed-time | Tempo UTC definido pelo serviço que representa o tempo real de mensagem. Ignorado na entrada. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-se-número de sequência | Número único definido pelo serviço atribuído a uma mensagem. | [SequênciaNumer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Número de sequência encostado definido pelo serviço. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-bloqueado-até | Definido pelo serviço. A data e a hora até à qual a mensagem será bloqueada na fila/subscrição. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-fonte | Definido pelo serviço. Se a mensagem for recebida da fila da carta morta, a fonte da mensagem original. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Capacidade de transação

Uma transação agrupa duas ou mais operações num âmbito de execução. Por natureza, tal transação deve assegurar que todas as operações pertencentes a um determinado grupo de operações tenham êxito ou falhem conjuntamente.
As operações são agrupadas por um `txn-id` identificador.

Para a interação transacional, o cliente atua como um `transaction controller` , que controla as operações que devem ser agrupadas. A Service Bus Service atua como um `transactional resource` e executa o trabalho como solicitado pelo `transaction controller` .

O cliente e o serviço comunicam através de um `control link` , que é estabelecido pelo cliente. As `declare` `discharge` mensagens e mensagens são enviadas pelo controlador sobre a ligação de controlo para alocar e completar transações respectivamente (não representam a demarcação do trabalho transacional). O envio/receção real não é realizado neste link. Cada operação transacional solicitada é explicitamente identificada com o desejado `txn-id` e, portanto, pode ocorrer em qualquer ligação na Ligação. Se a ligação de controlo for encerrada enquanto existirem transações não descarregadas que criou, todas essas transações são imediatamente revoadas e as tentativas de efetuar mais trabalhos transacionais sobre elas conduzirão a falhas. As mensagens na ligação de controlo não devem ser pré-resolvidas.

Cada ligação tem de iniciar a sua própria ligação de controlo para poder iniciar e terminar transações. O serviço define um alvo especial que funciona como `coordinator` um . O cliente/controlador estabelece uma ligação de controlo com este alvo. A ligação de controlo está fora do limite de uma entidade, ou seja, a mesma ligação de controlo pode ser usada para iniciar e descarregar transações para várias entidades.

#### <a name="starting-a-transaction"></a>Iniciar uma transação

Para começar o trabalho transacional. o controlador deve obter um `txn-id` do coordenador. Fá-lo enviando uma `declare` mensagem tipo. Se a declaração for bem sucedida, o coordenador responde com um resultado de disposição, que carrega o designado `txn-id` .

| Cliente (Controlador) | Direção | Autocarro de serviço (Coordenador) |
| :--- | :---: | :--- |
| anexar;<br/>nome={link name},<br/>... ,<br/>papel=**remetente,**<br/>alvo=**Coordenador**<br/>) | ------> |  |
|  | <------ | anexar;<br/>nome={link name},<br/>... ,<br/>target=Coordenador()<br/>) |
| transferência.<br/>entrega-id=0, ...)<br/>{ AmqpValue **(Declarar)}**| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>estado=**Declarado**(<br/>**txn-id**={iD transação}<br/>))|

#### <a name="discharging-a-transaction"></a>Descarregar uma transação

O controlador conclui o trabalho transacional enviando uma `discharge` mensagem ao coordenador. O controlador indica que deseja comprometer ou reverter o trabalho transacional colocando a `fail` bandeira no corpo de descarga. Se o coordenador não conseguir completar a quitação, a mensagem é rejeitada com este resultado que transporta o `transaction-error` .

> Nota: fail=true refere-se a Reversão de uma transação, e fail=false refere-se a Commit.

| Cliente (Controlador) | Direção | Autocarro de serviço (Coordenador) |
| :--- | :---: | :--- |
| transferência.<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declare)}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>estado=Declarado(<br/>txn-id={iD transação}<br/>))|
| | . . . <br/>Trabalho transacional<br/>em outros links<br/> . . . |
| transferência.<br/>entrega-id=57, ...)<br/>{ AmqpValue (<br/>**Descarga (txn-id=0, <br/> fail=false)**)}| ------> |  |
| | <------ | disposição. <br/> primeiro=57, último=57, <br/>estado=**Aceito()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Enviar uma mensagem numa transação

Todo o trabalho transacional é feito com o estado de entrega transacional `transactional-state` que transporta o txn-id. No caso de envio de mensagens, o estado transacional é transportado pelo quadro de transferência da mensagem. 

| Cliente (Controlador) | Direção | Autocarro de serviço (Coordenador) |
| :--- | :---: | :--- |
| transferência.<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declare)}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>estado=Declarado(<br/>txn-id={iD transação}<br/>))|
| transferência.<br/>pega=1,<br/>entrega-id=1, <br/>**estado= <br/> Estado transacional( <br/> txn-id=0)**)<br/>{ carga útil }| ------> |  |
| | <------ | disposição. <br/> primeiro=1, último=1, <br/>estado=**Estado Transacional(txn-id=0, <br/> <br/> resultado=Aceito()**|

#### <a name="disposing-a-message-in-a-transaction"></a>Eliminação de uma mensagem numa transação

A disposição da mensagem inclui operações como `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Para realizar estas operações dentro de uma transação, passe a `transactional-state` com a disposição.

| Cliente (Controlador) | Direção | Autocarro de serviço (Coordenador) |
| :--- | :---: | :--- |
| transferência.<br/>entrega-id=0, ...)<br/>{ AmqpValue (Declare)}| ------> |  |
|  | <------ | disposição. <br/> primeiro=0, último=0, <br/>estado=Declarado(<br/>txn-id={iD transação}<br/>))|
| | <------ |transferência.<br/>pega=2,<br/>entrega-id=11, <br/>estado=nulo)<br/>{ carga útil }|  
| disposição. <br/> primeiro=11, último=11, <br/>estado=**Estado Transacional(txn-id=0, <br/> <br/> resultado=Aceito()**| ------> |


## <a name="advanced-service-bus-capabilities"></a>Capacidades avançadas de ônibus de serviço

Esta secção abrange as capacidades avançadas da Azure Service Bus que se baseiam em projetos de extensões à AMQP, atualmente em desenvolvimento no Comité Técnico do OASIS para a AMQP. A Service Bus implementa as versões mais recentes destes projetos e adota alterações introduzidas à medida que estes projetos atingem o estatuto padrão.

> [!NOTE]
> As operações avançadas de mensagens de autocarro de serviço são suportadas através de um padrão de pedido/resposta. Os detalhes destas operações são descritos no artigo [AMQP 1.0 em Service Bus: operações baseadas em pedidos de resposta.](service-bus-amqp-request-response.md)
> 
> 

### <a name="amqp-management"></a>Gestão amqp

O caderno de encargos é o primeiro dos projetos de extensão discutidos neste artigo. Esta especificação define um conjunto de protocolos em camadas em cima do protocolo AMQP que permitem interações de gestão com a infraestrutura de mensagens sobre AMQP. A especificação define operações genéricas como *criar,* *ler,* *atualizar* e *excluir* para entidades gestoras dentro de uma infraestrutura de mensagens e um conjunto de operações de consulta.

Todos estes gestos requerem uma interação pedido/resposta entre o cliente e a infraestrutura de mensagens, pelo que a especificação define como modelar esse padrão de interação em cima da AMQP: o cliente conecta-se à infraestrutura de mensagens, inicia uma sessão e, em seguida, cria um par de links. Num link, o cliente atua como remetente e, por outro, atua como recetor, criando assim um par de links que podem funcionar como um canal biducional.

| Operação Lógica | Cliente | Service Bus |
| --- | --- | --- |
| Criar caminho de resposta de pedido |-> anexação.<br/>nome={*nome de ligação*},<br/>cabo={*pega numérica*},<br/>papel=**remetente,**<br/>fonte=**nula,**<br/>target="myentity/$management"<br/>) |Sem ação |
| Criar caminho de resposta de pedido |Sem ação |\<-- attach(<br/>nome={*nome de ligação*},<br/>cabo={*pega numérica*},<br/>papel=**recetor,**<br/>fonte=nulo,<br/>target="myentity"<br/>) |
| Criar caminho de resposta de pedido |-> anexação.<br/>nome={*nome de ligação*},<br/>cabo={*pega numérica*},<br/>papel=**recetor,**<br/>fonte="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Criar caminho de resposta de pedido |Sem ação |\<-- attach(<br/>nome={*nome de ligação*},<br/>cabo={*pega numérica*},<br/>papel=**remetente,**<br/>fonte="myentity",<br/>target="myclient$id"<br/>) |

Tendo esse par de links no lugar, a implementação do pedido/resposta é simples: um pedido é uma mensagem enviada a uma entidade dentro da infraestrutura de mensagens que compreende este padrão. Nessa mensagem de pedido, o campo *de resposta-a-terreno* na secção *de propriedades* é definido para o *identificador-alvo* para o link para a entrega da resposta. A entidade de manuseamento processa o pedido e, em seguida, entrega a resposta sobre o link cujo *identificador-alvo* corresponde ao identificador *de resposta* indicado.

O padrão obviamente requer que o contentor cliente e o identificador gerado pelo cliente para o destino de resposta sejam únicos em todos os clientes e, por razões de segurança, também difíceis de prever.

As trocas de mensagens utilizadas para o protocolo de gestão e para todos os outros protocolos que utilizam o mesmo padrão acontecem ao nível da aplicação; não definem novos gestos ao nível do protocolo AMQP. Isso é intencional, para que as aplicações possam tirar o máximo partido destas extensões com pilhas AMQP 1.0 compatíveis.

A Service Bus não implementa atualmente nenhuma das características fundamentais da especificação de gestão, mas o padrão de pedido/resposta definido pela especificação de gestão é fundamental para a funcionalidade de segurança baseada em sinistros e para quase todas as capacidades avançadas discutidas nas seguintes secções:

### <a name="claims-based-authorization"></a>Autorização baseada em sinistros

O projeto de especificação de autorização baseada em sinistros amQP (CBS) baseia-se no pedido/padrão de resposta de especificação de gestão e descreve um modelo generalizado para como usar fichas de segurança federadas com AMQP.

O modelo de segurança padrão da AMQP discutido na introdução baseia-se no SASL e integra-se com o aperto de mão de conexão AMQP. A utilização do SASL tem a vantagem de fornecer um modelo extensível para o qual foi definido um conjunto de mecanismos a partir do qual qualquer protocolo que se apoie formalmente no SASL pode beneficiar. Entre esses mecanismos estão "PLAIN" para transferência de nomes de utilizador e palavras-passe, "EXTERNAL" para se ligar à segurança ao nível do TLS, "ANONYMOUS" para expressar a ausência de autenticação/autorização explícita, e uma ampla variedade de mecanismos adicionais que permitem a autenticação e/ou credenciais de autorização ou fichas.

A integração SASL da AMQP tem dois inconvenientes:

* Todas as credenciais e fichas são miradas à ligação. Uma infraestrutura de mensagens pode querer fornecer um controlo de acesso diferenciado por entidade; por exemplo, permitindo que o portador de um símbolo envie para a fila A, mas não para a fila B. Com o contexto de autorização ancorado na ligação, não é possível utilizar uma única ligação e ainda utilizar diferentes fichas de acesso para a fila A e fila B.
* As fichas de acesso são normalmente válidas apenas por um tempo limitado. Esta validade requer que o utilizador readquira periodicamente fichas e oferece uma oportunidade ao emitente simbólico de recusar a emissão de um novo token se as permissões de acesso do utilizador tiverem mudado. As ligações AMQP podem durar por longos períodos de tempo. O modelo SASL apenas oferece a oportunidade de definir um símbolo na hora da ligação, o que significa que a infraestrutura de mensagens ou tem que desligar o cliente quando o token expira ou precisa aceitar o risco de permitir a comunicação contínua com um cliente que os direitos de acesso podem ter sido revogados interinamente.

A especificação AMQP CBS, implementada pela Service Bus, permite uma solução elegante para ambos os problemas: Permite que um cliente associe fichas de acesso a cada nó, e atualizar esses tokens antes de expirarem, sem interromper o fluxo de mensagens.

A CBS define um nó de gestão virtual, denominado *$cbs,* a ser fornecido pela infraestrutura de mensagens. O nó de gestão aceita fichas em nome de quaisquer outros nós na infraestrutura de mensagens.

O gesto do protocolo é uma troca de pedido/resposta, tal como definida pela especificação de gestão. Isto significa que o cliente estabelece um par de ligações com o nó *$cbs* e, em seguida, passa um pedido no link de saída, e depois aguarda a resposta no link de entrada.

A mensagem de pedido tem as seguintes propriedades de aplicação:

| Chave | Opcional | Tipo de Valor | Conteúdo de valor |
| --- | --- | --- | --- |
| operation |No |string |**put-token** |
| tipo |No |string |O tipo de símbolo que está a ser colocado. |
| name |No |string |O "público" a que o símbolo se aplica. |
| expiração |Yes |carimbo de data/hora |O prazo de validade do token. |

O *nome* da propriedade identifica a entidade com a qual o símbolo deve ser associado. No Service Bus é o caminho para a fila, ou tópico/subscrição. A propriedade *tipo* identifica o tipo de símbolo:

| Tipo token | Descrição simbólica | Tipo de corpo | Notas |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |Valor AMQP (cadeia) |Ainda não está disponível. |
| amqp:swt |Simples token web (SWT) |Valor AMQP (cadeia) |Suportado apenas para fichas SWT emitidas pela AAD/ACS |
| servicebus.windows.net:sastoken |Ficha de ônibus de serviço SAS |Valor AMQP (cadeia) |- |

Tokens conferem direitos. A Service Bus conhece três direitos fundamentais: "Enviar" permite o envio, "Ouvir" permite receber e "Gerir" permite manipular entidades. As fichas SWT emitidas pela AAD/ACS incluem explicitamente esses direitos como reclamações. Os tokens do Service Bus SAS referem-se a regras configuradas no espaço ou entidade de nomes, e essas regras estão configuradas com direitos. A assinatura do token com a chave associada a essa regra faz com que o símbolo expresse os respetivos direitos. O símbolo associado a uma entidade que usa *o put-token* permite ao cliente conectado interagir com a entidade de acordo com os direitos simbólicos. Um link onde o cliente assume o papel *de remetente* requer o direito "Enviar"; assumir a função *recetor requer* o direito "Ouvir".

A mensagem de resposta tem os seguintes valores *de propriedades de aplicação*

| Chave | Opcional | Tipo de Valor | Conteúdo de valor |
| --- | --- | --- | --- |
| código de estado |No |int |Código de resposta HTTP **[RFC2616]**. |
| descrição do estado |Yes |string |Descrição do estado. |

O cliente pode ligar *para o put-token* repetidamente e para qualquer entidade na infraestrutura de mensagens. Os tokens são telescópios para o cliente atual e ancorados na ligação atual, o que significa que o servidor deixa cair quaisquer fichas retidas quando a ligação cai.

A atual implementação do Service Bus só permite a CBS em conjunto com o método SASL "ANONYMOUS". Uma ligação SSL/TLS deve existir sempre antes do aperto de mão SASL.

O mecanismo ANONYMOUS deve, portanto, ser apoiado pelo cliente amqp 1.0 escolhido. O acesso anónimo significa que o aperto de mão de ligação inicial, incluindo a criação da sessão inicial, acontece sem que o Service Bus saiba quem está a criar a ligação.

Uma vez estabelecida a ligação e a sessão, a fixação dos links ao nó *$cbs* e o envio do pedido *de ficha* de ficha são as únicas operações permitidas. Um token válido deve ser definido com sucesso usando um pedido *de símbolo* para algum nó de entidade no prazo de 20 segundos após a ligação ter sido estabelecida, caso contrário a ligação é unilateralmente abandonada pela Service Bus.

O cliente é posteriormente responsável por acompanhar a expiração simbólica. Quando um token expira, a Service Bus deixa rapidamente cair todos os links da ligação à respetiva entidade. Para evitar problemas, o cliente pode substituir o símbolo do nó por um novo a qualquer momento através do nó de gestão de *$cbs* virtual pelo mesmo gesto *de put-token,* e sem ficar no caminho do tráfego de carga útil que flui em diferentes ligações.

### <a name="send-via-functionality"></a>Funcionalidade de envio

[O remetente de envio/transfer](service-bus-transactions.md#transfers-and-send-via) é uma funcionalidade que permite que o autocarro de serviço envie uma mensagem dada a uma entidade de destino através de outra entidade. Esta funcionalidade é utilizada para realizar operações entre entidades numa única transação.

Com esta funcionalidade, cria-se um remetente e estabelece-se a ligação com o `via-entity` . Ao estabelecer o link, são passadas informações adicionais para estabelecer o verdadeiro destino das mensagens/transferências neste link. Uma vez que o anexo tenha sido bem sucedido, todas as mensagens enviadas neste link são automaticamente reencaminhadas para a *entidade de destino* através de *via-entidade*. 

> Nota: A autenticação tem de ser realizada tanto para *a entidade via-entidade* como para *a entidade de destino* antes de estabelecer este link.

| Cliente | Direção | Service Bus |
| :--- | :---: | :--- |
| anexar;<br/>nome={link name},<br/>role=remetente,<br/>ID de ligação ao cliente source={},<br/>alvo=**{via-entidade}**,<br/>**propriedades=mapa <br/> [(com.microsoft:transfer-destination-address= <br/> {destination-entity} ))** | ------> | |
| | <------ | anexar;<br/>nome={link name},<br/>role=recetor,<br/>ID de ligação ao cliente source={},<br/>target={via-entidade},<br/>propriedades=mapa [.<br/>com.microsoft:transfer-destination-address=<br/>{entidade de destino} )] ) |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre AMQP, visite os seguintes links:

* [Visão geral do AMQP do autocarro de serviço]
* [Suporte AMQP 1.0 para filas e tópicos divididos do Service Bus]
* [AMQP em ônibus de serviço para windows server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Visão geral do AMQP do autocarro de serviço]: service-bus-amqp-overview.md
[Suporte AMQP 1.0 para filas e tópicos divididos do Service Bus]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
