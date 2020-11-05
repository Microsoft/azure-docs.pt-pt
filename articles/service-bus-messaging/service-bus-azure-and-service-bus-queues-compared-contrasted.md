---
title: Comparar filas do Armazenamento do Azure e filas do Service Bus
description: Analisa diferenças e semelhanças entre dois tipos de filas oferecidas pelo Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 5c65cf5ef2d572417ea70d0e0259cf2c03ab590e
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379575"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Filas de armazenamento e filas de autocarros de serviço - comparadas e contrastadas
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidas pela Microsoft Azure: filas de armazenamento e filas de Service Bus. Ao utilizar esta informação, pode tomar uma decisão mais informada sobre qual a solução que melhor satisfaz as suas necessidades.

## <a name="introduction"></a>Introdução
O Azure suporta dois tipos de mecanismos de fila: **filas de armazenamento** e filas de autocarros de **serviço.**

**As filas de armazenamento** fazem parte da infraestrutura de [armazenamento Azure.](https://azure.microsoft.com/services/storage/) Permitem-lhe armazenar um grande número de mensagens. Acede a mensagens de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos. Para mais informações, consulte [as filas de armazenamento Azure.](../storage/queues/storage-queues-introduction.md)

As filas de **autocarros** de serviço fazem parte de uma infraestrutura de [mensagens Azure](https://azure.microsoft.com/services/service-bus/) mais ampla que suporta filas, publicação/subscrição e padrões de integração mais avançados. São projetados para integrar aplicações ou componentes de aplicação que podem abranger vários protocolos de comunicação, contratos de dados, domínios de confiança ou ambientes de rede. Para obter mais informações sobre as filas/tópicos/subscrições do Service Bus, consulte as [filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)do Service Bus.


## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia
As filas de armazenamento e as filas de Service Bus têm um conjunto de funcionalidades ligeiramente diferente. Pode escolher um ou ambos, dependendo das necessidades da sua solução particular.

Ao determinar qual a tecnologia de fila que se adequa ao propósito de uma determinada solução, os arquitetos e desenvolvedores de soluções devem considerar estas recomendações. 

### <a name="consider-using-storage-queues"></a>Considere usar as filas de armazenamento
Como arquiteto/desenvolvedor de soluções, **deve considerar a utilização de filas de armazenamento** quando:

* A sua aplicação deve armazenar mais de 80 gigabytes de mensagens numa fila.
* A sua aplicação quer acompanhar o progresso para processar uma mensagem na fila. É útil se o trabalhador processar uma mensagem falhar. Outro trabalhador pode então utilizar essa informação para continuar de onde o trabalhador anterior deixou de lado.
* Necessita de registos laterais do servidor de todas as transações executadas contra as suas filas.

### <a name="consider-using-service-bus-queues"></a>Considere usar as filas de autocarros de serviço
Como arquiteto/desenvolvedor de soluções, **deve considerar usar as filas de Service Bus** quando:

* A sua solução precisa de receber mensagens sem ter de fazer sondagens. Com a Service Bus, pode alcançá-lo utilizando uma operação de receção de sondagens de longa duração utilizando os protocolos baseados em TCP que a Service Bus suporta.
* A sua solução requer que a fila forneça uma entrega garantida de primeiro em primeiro lugar (FIFO).
* A sua solução precisa de suportar a deteção automática de duplicados.
* Pretende que a sua aplicação processe mensagens como streams paralelos de longa duração (as mensagens estão associadas a um stream utilizando a propriedade [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) na mensagem). Neste modelo, cada nó na aplicação consumista compete por streams, ao contrário de mensagens. Quando um fluxo é dado a um nó consumista, o nó pode examinar o estado do fluxo de aplicação usando transações.
* A sua solução requer comportamento transacional e atomicidade ao enviar ou receber várias mensagens de uma fila.
* A sua aplicação trata mensagens que podem ultrapassar os 64 KB, mas que provavelmente não se aproximarão do limite de 256 KB.
* Você lida com a obrigação de fornecer um modelo de acesso baseado em papel às filas, e diferentes direitos/permissões para remetentes e recetores. Para obter mais informações, veja os seguintes artigos:
    - [Autenticar com identidades geridas](service-bus-managed-service-identity.md)
    - [Autenticar a partir de uma aplicação](authenticate-application.md)
* O tamanho da sua fila não vai crescer mais do que 80 GB.
* Você quer usar o protocolo de mensagens baseado em padrões AMQP 1.0. Para obter mais informações sobre a AMQP, consulte [a Visão Geral amQP do Service Bus AMQP.](service-bus-amqp-overview.md)
* Você prevê uma eventual migração da comunicação ponto-a-ponto baseada na fila para um padrão de mensagens de publicação- subscrição. Este padrão permite a integração de recetores adicionais (assinantes). Cada recetor recebe cópias independentes de algumas ou de todas as mensagens enviadas para a fila. 
* A sua solução de mensagens precisa de suportar a garantia de entrega "At-Most Once" sem a necessidade de construir os componentes adicionais da infraestrutura.
* A sua solução precisa de publicar e consumir lotes de mensagens.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Compare as filas de armazenamento e as filas de autocarros de serviço
As tabelas nas seguintes secções fornecem um agrupamento lógico de funcionalidades de fila. Permitem comparar, num ápice, as capacidades disponíveis tanto nas filas de Armazenamento Azure como nas filas de Service Bus.

## <a name="foundational-capabilities"></a>Capacidades fundamentais
Esta secção compara algumas das capacidades fundamentais de fila fornecidas pelas filas de armazenamento e filas de Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Garantia de encomenda |**Não** <br/><br>Para mais informações, consulte a primeira nota na secção [Informação Adicional.](#additional-information)</br> | **Sim - First-In-First-Out (FIFO)**<br/><br>(através da utilização de sessões de [mensagens)](message-sessions.md) |
| Garantia de entrega |**Pelo menos uma vez** |**Pelo menos uma vez** (utilizando o modo de receção PeekLock. É o padrão) <br/><br/>**At-Most-Once** (utilizando o modo de receção ReceiveAndDelete) <br/> <br/> Saiba mais sobre vários [modos Receber](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Suporte à operação atómica |**Não** |**Sim**<br/><br/> |
| Receber comportamento |**Não bloqueio**<br/><br/>(completa imediatamente se não for encontrada nova mensagem) |**Bloqueio com ou sem intervalo**<br/><br/>(oferece sondagens longas, ou a ["técnica do cometa"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Não bloqueio**<br/><br/>(através da utilização apenas da API gerida .NET) |
| API estilo push |**Não** |**Sim**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) and [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) .NET API. |
| Modo de receção |**Peek & Lease** |**Peek & Lock**<br/><br/>**Receber & Eliminar** |
| Modo de acesso exclusivo |**Baseado em arrendamento** |**Baseado em bloqueio** |
| Duração do arrendamento/bloqueio |**30 segundos (padrão)**<br/><br/>**7 dias (máximo)** (Pode renovar ou lançar um contrato de mensagem utilizando a [API de Atualização.)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) |**60 segundos (padrão)**<br/><br/>Pode renovar um bloqueio de mensagem utilizando a API [Renovação.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Precisão de arrendamento/bloqueio |**Nível de mensagem**<br/><br/>Cada mensagem pode ter um valor de tempo limite diferente, que pode então atualizar conforme necessário durante o processamento da mensagem, utilizando a [API de API de APIT de Atualização.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) |**Nível de fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas pode renovar o bloqueio utilizando a API [renovação.)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Receber em lotado |**Sim**<br/><br/>(especificar explicitamente a contagem de mensagens ao recuperar mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(permitir implicitamente uma propriedade pré-compra ou explicitamente através da utilização de transações) |
| Envio lotado |**Não** |**Sim**<br/><br/>(através da utilização de transações ou de lotação do lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* As mensagens nas filas de armazenamento são normalmente primeiras a sair, mas às vezes podem estar fora de serviço. Por exemplo, quando a duração do tempo de visibilidade de uma mensagem expira porque uma aplicação do cliente se despenhou durante o processamento de uma mensagem. Quando o tempo limite de visibilidade expira, a mensagem torna-se visível novamente na fila para que outro trabalhador a desaqueça. Nessa altura, a mensagem recém-visível pode ser colocada na fila para ser desativada novamente.
* O padrão FIFO garantido nas filas de Service Bus requer o uso de sessões de mensagens. Se a aplicação falhar enquanto está a processar uma mensagem recebida no modo **Desapeise &,** da próxima vez que um recetor de fila aceitar uma sessão de mensagens, começará com a mensagem falhada após o período de tempo de vida da mensagem (TTL) expirar.
* As filas de armazenamento são concebidas para suportar cenários de fila padrão, tais como os seguintes:
    - Dissociar componentes de aplicação para aumentar a escalabilidade e tolerância para falhas
    - Nivelamento de carga
    - Fluxos de trabalho de processo de construção.
* As inconsistências no que diz respeito ao tratamento de mensagens no contexto das sessões de Service Bus podem ser evitadas utilizando o estado da sessão para armazenar o estado da aplicação em relação ao progresso do tratamento da sequência de mensagens da sessão, e utilizando transações em torno de acertar mensagens recebidas e atualizar o estado da sessão. Este tipo de característica de consistência é por vezes rotulado *exatamente uma vez que o processamento* em outros produtos do fornecedor. Quaisquer falhas de transação irão obviamente fazer com que as mensagens sejam reenrimidas e é por isso que o termo não é exatamente adequado.
* As filas de armazenamento fornecem um modelo de programação uniforme e consistente em filas, mesas e BLOBs – tanto para programadores como para equipas de operações.
* As filas de autocarros de serviço fornecem suporte para transações locais no contexto de uma única fila.
* O modo **Receber e Eliminar** suportado pela Service Bus fornece a capacidade de reduzir a contagem de operação de mensagens (e custos associados) em troca de uma garantia de entrega reduzida.
* As filas de armazenamento proporcionam arrendamentos com a capacidade de estender os arrendamentos para mensagens. Esta funcionalidade permite que os processos de trabalho mantenham arrendamentos curtos em mensagens. Assim, se um trabalhador falhar, a mensagem pode ser rapidamente processada novamente por outro trabalhador. Além disso, um trabalhador pode estender o arrendamento numa mensagem se precisar processá-lo por mais tempo do que o tempo atual de arrendamento.
* As filas de armazenamento oferecem um tempo limite de visibilidade que pode definir sobre a mensagem ou a descodência de uma mensagem. Além disso, pode atualizar uma mensagem com diferentes valores de locação no tempo de execução e atualizar diferentes valores entre mensagens na mesma fila. Os intervalos de bloqueio do serviço são definidos nos metadados da fila. No entanto, pode renovar o bloqueio chamando o método [Renovar.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)
* O tempo máximo para uma operação de receção de bloqueio nas filas do Service Bus é de 24 dias. No entanto, os intervalos baseados em REST têm um valor máximo de 55 segundos.
* O lote do lado do cliente fornecido pela Service Bus permite que um cliente de fila deslome várias mensagens numa única operação de envio. O loteamento só está disponível para operações de envio assíncronos.
* Funcionalidades como o teto de 200-TB das filas de armazenamento (mais quando virtualiza contas) e filas ilimitadas fazem dele uma plataforma ideal para os fornecedores de SaaS.
* As filas de armazenamento fornecem um mecanismo flexível e performante de controlo de acesso delegado.

## <a name="advanced-capabilities"></a>Capacidades avançadas
Esta secção compara as capacidades avançadas fornecidas pelas filas de armazenamento e filas de Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Letras automáticas mortas |**Não** |**Sim** |
| Aumento do valor do tempo de vida da fila |**Sim**<br/><br/>(através da atualização in-place do tempo limite de visibilidade) |**Sim**<br/><br/>(fornecido através de uma função API dedicada) |
| Suporte de mensagem venenosa |**Sim** |**Sim** |
| Atualização no local |**Sim** |**Sim** |
| Registo de transações do lado do servidor |**Sim** |**Não** |
| Métricas de armazenamento |**Sim**<br/><br/>**As métricas minúsculas** fornecem métricas em tempo real para disponibilidade, TPS, contagem de chamadas API, contagens de erros e muito mais. Estão todos em tempo real, agregados por minuto e relatados a poucos minutos do que aconteceu na produção. Para obter mais informações, consulte [Sobre as Métricas de Análise de Armazenamento.](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics) |**Sim**<br/><br/>(consultas a granel chamando [GetQueues)](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) |
| Gestão de estados |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Ative](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled,](/dotnet/api/microsoft.servicebus.messaging.entitystatus) [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Mensagem de autoforwarding |**Não** |**Sim** |
| Função de fila de purga |**Sim** |**Não** |
| Grupos de mensagens |**Não** |**Sim**<br/><br/>(através da utilização de sessões de mensagens) |
| Estado de aplicação por grupo de mensagens |**Não** |**Sim** |
| Deteção de duplicados |**Não** |**Sim**<br/><br/>(configurável do lado do remetente) |
| Grupos de mensagens de navegação |**Não** |**Sim** |
| Buscar sessões de mensagens por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de fila permitem que uma mensagem seja agendada para entrega posteriormente.
* A auto-viação da fila permite que milhares de filas de passageiros adiscam as suas mensagens para uma única fila, a partir da qual a aplicação recetora consome a mensagem. Pode utilizar este mecanismo para obter segurança, fluxo de controlo e isolar o armazenamento entre cada editor de mensagens.
* As filas de armazenamento fornecem suporte para atualizar o conteúdo da mensagem. Pode utilizar esta funcionalidade para insistido na informação do estado e atualizações incrementais de progresso na mensagem para que possa ser processada a partir do último ponto de verificação conhecido, em vez de começar do zero. Com as filas do Service Bus, pode ativar o mesmo cenário através da utilização de sessões de mensagens. As sessões permitem-lhe guardar e recuperar o estado de processamento da aplicação (utilizando [o SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [o GetState).](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)
* As filas de autocarros de serviço [suportam a inscrição morta.](service-bus-dead-letter-queues.md) Pode ser útil para isolar mensagens que satisfaçam os seguintes critérios:
    - As mensagens não podem ser processadas com sucesso pela aplicação recetora 
    - As mensagens não podem chegar ao seu destino por causa de uma propriedade de tempo de vida (TTL) expirada. O valor TTL especifica quanto tempo uma mensagem permanece na fila. Com o Service Bus, a mensagem será transferida para uma fila especial chamada $DeadLetterQueue quando o período TTL expirar.
* Para encontrar mensagens "venenosas" nas filas de armazenamento, ao descoducionar uma mensagem, a aplicação examina a propriedade [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) da mensagem. Se **o DequeueCount** for maior do que um determinado limiar, a aplicação move a mensagem para uma fila de "letra morta" definida pela aplicação.
* As filas de armazenamento permitem obter um registo detalhado de todas as transações executadas contra a fila e métricas agregadas. Ambas as opções são úteis para depurar e entender como a sua aplicação utiliza as filas de armazenamento. Também são úteis para afinar o desempenho da sua aplicação e reduzir os custos de utilização de filas.
* As sessões de mensagens suportadas pelo Service Bus permitem que mensagens pertencentes a um grupo lógico sejam associadas a um recetor. Cria uma afinidade em forma de sessão entre mensagens e respetivos recetores. Pode ativar esta funcionalidade avançada no Service Bus definindo a propriedade [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) numa mensagem. Os recetores podem então ouvir um ID de sessão específico e receber mensagens que partilhem o identificador de sessão especificado.
* A funcionalidade de deteção de duplicação das filas do Service Bus remove automaticamente as mensagens duplicadas enviadas para uma fila ou tópico, com base no valor da propriedade [MessageId.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)

## <a name="capacity-and-quotas"></a>Capacidade e quotas
Esta secção compara as filas de armazenamento e as filas de Service Bus na perspetiva da [capacidade e](service-bus-quotas.md) das quotas que podem ser aplicadas.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a uma [única capacidade de conta de armazenamento)](../storage/common/storage-introduction.md#queue-storage) |**1 GB a 80 GB**<br/><br/>(definido após a criação de uma fila e [facilitando a partilha](service-bus-partitioning.md) – ver a secção "Informação Adicional") |
| Tamanho máximo da mensagem |**64 KB**<br/><br/>(48 KB ao utilizar a codificação **Base64)**<br/><br/>O Azure suporta grandes mensagens combinando filas e bolhas – altura em que pode encadear até 200 GB para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo o cabeçalho e o corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende do [nível de serviço.](service-bus-premium-messaging.md) |
| Mensagem máxima TTL |**Infinite** (versão api 2017-07-27 ou posterior) |**TimeSpan.Max** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por espaço de nome de serviço) |
| Número máximo de clientes simultâneos |**Ilimitado** |**Ilimitado**<br/><br/>(100 limites de ligação simultâneos apenas se aplicam à comunicação baseada no protocolo TCP) |

### <a name="additional-information"></a>Informações adicionais
* O Service Bus impõe limites de tamanho de fila. O tamanho máximo da fila é especificado ao criar uma fila. Pode ser entre 1 GB e 80 GB. Se o tamanho da fila atingir este limite, as mensagens adicionais de entrada serão rejeitadas e o chamador receberá uma exceção. Para obter mais informações sobre quotas em Service Bus, consulte [Service Bus Quotas](service-bus-quotas.md).
* A partilha não é suportada no [nível Premium.](service-bus-premium-messaging.md) No nível de mensagens Standard, pode criar filas e tópicos de Service Bus em tamanhos 1 (padrão), 2, 3, 4 ou 5-GB. Com a partição ativada, a Service Bus cria 16 cópias (16 divisórias) da entidade, cada uma do mesmo tamanho especificada. Como tal, se criar uma fila de 5 GB de tamanho, com 16 divisórias o tamanho máximo da fila torna-se (5 * 16) = 80 GB.  Pode ver o tamanho máximo da sua fila ou tópico dividido no [portal Azure.][Azure portal]
* Com as filas de armazenamento, se o conteúdo da mensagem não for seguro para XML, então deve ser **codificado base64.** Se codificar a mensagem **base64,** a carga útil do utilizador pode chegar a 48 KB, em vez de 64 KB.
* Com as filas do Service Bus, cada mensagem armazenada numa fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo da mensagem suportado pelo nível de serviço.
* Quando os clientes comunicam com as filas do Service Bus sobre o protocolo TCP, o número máximo de ligações simultâneas a uma única fila de autocarros de serviço é limitado a 100. Este número é partilhado entre remetentes e recetores. Se este contingente for atingido, os pedidos de ligações adicionais serão rejeitados e uma exceção será recebida pelo código de chamada. Este limite não é imposto aos clientes que se ligam às filas usando API baseada em REST.
* Se necessitar de mais de 10.000 filas num único espaço de nomes do Service Bus, pode contactar a equipa de apoio da Azure e solicitar um aumento. Para escalar além de 10.000 filas com o Service Bus, também pode criar espaços de nome adicionais utilizando o [portal Azure.][Azure portal]

## <a name="management-and-operations"></a>Gestão e operações
Esta secção compara as funcionalidades de gestão fornecidas pelas filas de armazenamento e filas de Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Protocolo de gestão |**DESCANSE EM HTTP/HTTPS** |**DESCANSE EM HTTPS** |
| Protocolo de tempo de execução |**DESCANSE EM HTTP/HTTPS** |**DESCANSE EM HTTPS**<br/><br/>**Norma AMQP 1.0 (TCP com TLS)** |
| API .NET |**Sim**<br/><br/>(.NET Storage Client API) |**Sim**<br/><br/>(.NET Service Bus API) |
| C++ Nativo |**Sim** |**Sim** |
| API de Java |**Sim** |**Sim** |
| PHP API |**Sim** |**Sim** |
| API do Node.js |**Sim** |**Sim** |
| Suporte de metadados arbitrários |**Sim** |**Não** |
| Regras de nomeação de filas |**Até 63 caracteres de comprimento**<br/><br/>(As letras em nome da fila devem ser minúsculas.) |**Até 260 caracteres de comprimento**<br/><br/>(Os caminhos e nomes da fila são insensíveis ao caso.) |
| Obtenha a função de comprimento da fila |**Sim**<br/><br/>(Valor aproximado se as mensagens expirarem para além do TTL sem serem apagadas.) |**Sim**<br/><br/>(Valor exato, ponto no tempo.) |
| Função de espreitar |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* As filas de armazenamento fornecem suporte para atributos arbitrários que podem ser aplicados à descrição da fila, sob a forma de pares de nome/valor.
* Ambas as tecnologias de fila oferecem a capacidade de espreitar uma mensagem sem ter que bloqueá-la, o que pode ser útil ao implementar uma ferramenta de explorador/navegador de fila.
* As APIs de mensagens intermediadas Service Bus .NET utilizam ligações TCP completas duplex para um melhor desempenho quando comparadas com REST sobre HTTP, e suportam o protocolo padrão AMQP 1.0.
* Os nomes das filas de armazenamento podem ter 3-63 caracteres de comprimento, podem conter letras minúsculas, números e hífenes. Para obter mais informações, consulte [As Filas de Nomeação e os Metadados.](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)
* Os nomes da fila do ônibus de serviço podem ter até 260 caracteres e têm regras de nomeação menos restritivas. Os nomes da fila do autocarro de serviço podem conter letras, números, períodos, hífens e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta secção discute as funcionalidades de autenticação e autorização suportadas pelas filas de armazenamento e pelas filas de Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Autenticação |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado através de fichas SAS. |SAS |
| Federação de Fornecedores de Identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Todos os pedidos a qualquer uma das tecnologias de fila devem ser autenticados. As filas públicas com acesso anónimo não são apoiadas. Utilizando [SAS,](service-bus-sas.md)pode abordar este cenário publicando um SAS apenas para escrever, SAS apenas de leitura ou até mesmo um SAS de acesso completo.
* O regime de autenticação fornecido pelas filas de armazenamento envolve a utilização de uma chave simétrica. Esta chave é um Código de Autenticação de Mensagens (HMAC) baseado em haxixe, calculado com o algoritmo SHA-256 e codificado como uma cadeia **Base64.** Para obter mais informações sobre o respetivo protocolo, consulte [autenticação para os Serviços de Armazenamento Azure.](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services) As filas do Service Bus suportam um modelo semelhante utilizando chaves simétricas. Para mais informações, consulte [a autenticação da assinatura de acesso partilhado com o Service Bus.](service-bus-sas.md)

## <a name="conclusion"></a>Conclusão
Ao obter uma compreensão mais profunda das duas tecnologias, você pode tomar uma decisão mais informada sobre qual a tecnologia de fila a usar, e quando. A decisão de quando utilizar filas de armazenamento ou filas de autocarros de serviço depende claramente de uma série de fatores. Estes fatores podem depender fortemente das necessidades individuais da sua aplicação e da sua arquitetura. 

Pode preferir escolher as filas de armazenamento por razões como as seguintes:

- Se a sua aplicação já utiliza as capacidades centrais do Microsoft Azure 
- Se necessitar de comunicação básica e mensagens entre serviços 
- Precisa de filas que possam ser maiores do que 80 GB de tamanho

As filas de ônibus de serviço fornecem uma série de funcionalidades avançadas, tais como as seguintes. Portanto, podem ser uma escolha preferida se estiver a construir uma aplicação híbrida ou se a sua aplicação de outra forma necessitar destas funcionalidades.

- [Sessões](message-sessions.md)
- [Transações](service-bus-transactions.md)
- [Deteção de duplicados](duplicate-detection.md)
- [Letras automáticas](service-bus-dead-letter-queues.md)
- [Capacidades de publicação e subscrição duradouras](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos fornecem mais orientação e informações sobre a utilização de filas de armazenamento ou filas de autocarros de serviço.

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Boas práticas para melhorias de desempenho usando mensagens intermediadas do Service Bus](service-bus-performance-improvements.md)
* [Apresentando filas e tópicos no Azure Service Bus (blog post)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Guia do Construtor para o ônibus de serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Utilização do Serviço de Filas em Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

