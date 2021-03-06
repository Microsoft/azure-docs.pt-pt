---
title: Transferências de mensagem, fechaduras e liquidação de ônibus da Azure Service
description: Este artigo fornece uma visão geral das transferências de mensagens, fechaduras e operações de liquidação da Azure Service Bus.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306777"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagens, bloqueios e acordo

A capacidade central de um corretor de mensagens, como o Service Bus, é aceitar mensagens numa fila ou tópico e mantê-las disponíveis para posterior recuperação. *Enviar* é o termo que é comumente usado para a transferência de uma mensagem para o corretor de mensagens. *Receber* é o termo comumente usado para a transferência de uma mensagem para um cliente que recupera.

Quando um cliente envia uma mensagem, normalmente quer saber se a mensagem foi devidamente transferida e aceite pelo corretor ou se ocorreu algum tipo de erro. Este reconhecimento positivo ou negativo resolve o cliente e o entendimento do corretor sobre o estado de transferência da mensagem. Assim, referiu-se como *acordo.*

Da mesma forma, quando o corretor transfere uma mensagem para um cliente, o corretor e o cliente querem estabelecer uma compreensão de se a mensagem foi processada com sucesso e pode, portanto, ser removida, ou se a entrega ou processamento da mensagem falhou, e assim a mensagem pode ter que ser entregue novamente.

## <a name="settling-send-operations"></a>Liquidação de operações de envio

Utilizando qualquer um dos clientes suportados da Service Bus API, as operações de envio para a Service Bus são sempre explicitamente resolvidas, o que significa que a operação da API aguarda a chegada de um resultado de aceitação da Service Bus e, em seguida, completa a operação de envio.

Se a mensagem for rejeitada pela Service Bus, a rejeição contém um indicador de erro e um texto com um **id de rastreio.** A rejeição também inclui informações sobre se a operação pode ser novamente julgada com qualquer expectativa de sucesso. No cliente, esta informação é transformada numa exceção e levantada ao autor da operação de envio. Se a mensagem tiver sido aceite, a operação termina silenciosamente.

Ao utilizar o protocolo AMQP, que é o protocolo exclusivo para os clientes .NET Standard, Java, JavaScript, Python e Go, e [uma opção para o cliente .NET Framework,](service-bus-amqp-dotnet.md)as transferências e liquidações de mensagens são pipelined e assíncronas. Recomendamos que utilize as variantes API do modelo de programação assíncrona.

Um remetente pode colocar várias mensagens no fio em rápida sucessão sem ter de esperar que cada mensagem seja reconhecida, como seria o caso do protocolo SBMP ou com HTTP 1.1. As operações de envio assíncronos completas à medida que as respetivas mensagens são aceites e armazenadas, em entidades divididas ou quando enviam operação a diferentes entidades sobrepostas. As conclusões também podem ocorrer fora da ordem de envio original.

A estratégia para lidar com o resultado das operações de envio pode ter um impacto imediato e significativo no desempenho da sua aplicação. Os exemplos nesta secção são escritos em C# e aplicam-se aos futuros java, java monos, promessas JavaScript e conceitos equivalentes em outras línguas.

Se a aplicação produzir explosões de mensagens, ilustradas aqui com um loop simples, e se aguardar a conclusão de cada operação de envio antes de enviar a próxima mensagem, formas de API sincronizadas ou assíncronas, o envio de 10 mensagens só completa após 10 viagens de ida e volta sequenciais completas para liquidação.

Com uma distância de latência de ida e volta de 70 milisegundos de ida e volta de um local para o Service Bus e dando apenas 10 ms para a Service Bus aceitar e armazenar cada mensagem, o seguinte loop demora pelo menos 8 segundos, sem contar com o tempo de transferência de carga útil ou potenciais efeitos de congestionamento da rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se a aplicação iniciar as 10 operações de envio assíncrona em sucessão imediata e aguarda a sua respetiva conclusão separadamente, o tempo de ida e volta para essas 10 operações de envio sobrepõe-se. As 10 mensagens são transferidas numa sucessão imediata, potencialmente até partilhando quadros de TCP, e a duração global da transferência depende em grande parte do tempo relacionado com a rede que leva para que as mensagens sejam transferidas para o corretor.

Fazendo os mesmos pressupostos que para o ciclo anterior, o tempo total de execução sobreposto para o ciclo seguinte pode ficar bem abaixo de um segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

É importante notar que todos os modelos de programação assíncronos usam alguma forma de fila de trabalho escondida baseada na memória que mantém as operações pendentes. Quando a API de envio retorna, a tarefa de envio é a fila de trabalho, mas o gesto do protocolo só começa quando é a vez da tarefa ser executada. Para um código que tende a empurrar explosões de mensagens e onde a fiabilidade é uma preocupação, deve-se ter cuidado para que não sejam colocadas muitas mensagens "em voo" de uma só vez, porque todas as mensagens enviadas tiram memória até serem factualmente colocadas no fio.

Os semáforos, como mostrado no seguinte corte de código em C#, são objetos de sincronização que permitem esse estrangulamento ao nível da aplicação quando necessário. Esta utilização de um semáforo permite, no máximo, 10 mensagens estarem em voo de uma só vez. Uma das 10 fechaduras de semáforos disponíveis é tirada antes do envio e é libertada à medida que o envio termina. A 11ª passagem pelo loop aguarda até que pelo menos um dos envios anteriores tenha terminado, e depois disponibiliza a sua fechadura:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

As candidaturas **nunca** devem iniciar uma operação de envio assíncronos de forma "fogo e esquecimento" sem recuperar o resultado da operação. Ao fazê-lo, pode carregar a fila de tarefas interna e invisível até à exaustão da memória e impedir que a aplicação detete erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP de baixo nível, a Service Bus também aceita transferências "pré-liquidadas". Uma transferência pré-liquidada é uma operação de fogo e esquecimento para a qual o resultado, de qualquer forma, não é comunicado ao cliente e a mensagem é considerada resolvida quando enviada. A falta de feedback para o cliente também significa que não existem dados acciíveis disponíveis para diagnósticos, o que significa que este modo não se qualifica para ajuda através do suporte Azure.

## <a name="settling-receive-operations"></a>Liquidação de operações de receção

Para as operações de receção, os clientes da Service Bus API permitem dois modos explícitos diferentes: *Receber e Eliminar* e *Espreitar.*

### <a name="receiveanddelete"></a>ReceberAndDelete

O modo **de receção e eliminação** diz ao corretor para considerar todas as mensagens que envia ao cliente recetor como liquidada quando enviada. Isto significa que a mensagem é considerada consumida assim que o corretor a colocar no fio. Se a transferência da mensagem falhar, a mensagem perde-se.

O lado positivo deste modo é que o recetor não precisa de tomar mais medidas sobre a mensagem e também não é abrandado à espera do resultado da liquidação. Se os dados contidos nas mensagens individuais tiverem baixo valor e/ou tiverem apenas significado por um curto período de tempo, este modo é uma escolha razoável.

### <a name="peeklock"></a>PeekLock

O modo **de bloqueio de espreitar** diz ao corretor que o cliente recetor quer liquidar as mensagens recebidas explicitamente. A mensagem é disponibilizada para que o recetor processe, mantendo-se sob um bloqueio exclusivo no serviço para que outros recetores concorrentes não possam vê-lo. A duração do bloqueio é inicialmente definida ao nível da fila ou subscrição e pode ser prolongada pelo cliente que possui o bloqueio. Para mais detalhes sobre a renovação de fechaduras, consulte a secção [Renovar as fechaduras](#renew-locks) neste artigo. 

Quando uma mensagem é bloqueada, outros clientes que recebem da mesma fila ou subscrição podem pegar nas fechaduras e recuperar as próximas mensagens disponíveis que não estão sob bloqueio ativo. Quando o bloqueio de uma mensagem é explicitamente libertado ou quando o bloqueio expira, a mensagem volta a aparecer na parte frontal ou próxima da ordem de recuperação para a reentrega.

Quando a mensagem é repetidamente libertada pelos recetores ou deixam o bloqueio decorrer por um número definido de vezes (Contagem de[entregas máximas),](service-bus-dead-letter-queues.md#maximum-delivery-count)a mensagem é automaticamente removida da fila ou subscrição e colocada na fila de letras mortas associada.

O cliente recetor inicia a liquidação de uma mensagem recebida com um reconhecimento positivo quando chama a `Complete` API para a mensagem. Indica ao corretor que a mensagem foi processada com sucesso e que a mensagem é removida da fila ou subscrição. O corretor responde à intenção de liquidação do recetor com uma resposta que indica se a liquidação poderia ser realizada.

Quando o cliente recetor não processa uma mensagem mas quer que a mensagem seja retransmissada, pode explicitamente pedir que a mensagem seja libertada e desbloqueada instantaneamente, ligando para a `Abandon` API para a mensagem ou não pode fazer nada e deixar o bloqueio decorrer.

Se um cliente recetor não processa uma mensagem e sabe que reviver a mensagem e voltar a tentar a operação não vai ajudar, pode rejeitar a mensagem, que a move para a fila da letra morta, ligando para a `DeadLetter` API na mensagem, o que também permite definir uma propriedade personalizada, incluindo um código de razão que pode ser recuperado com a mensagem da fila da letra morta.

Um caso especial de liquidação é diferimento, que é discutido num [artigo separado.](message-deferral.md)

O `Complete` , `Deadletter` ou `RenewLock` operações podem falhar devido a problemas de rede, se o bloqueio de retenção tiver expirado, ou se houver outras condições do lado do serviço que impeçam a liquidação. Num dos últimos casos, o serviço envia um reconhecimento negativo de que surge como uma exceção nos clientes da API. Se a razão for uma ligação de rede quebrada, o bloqueio é deixado cair, uma vez que o Service Bus não suporta a recuperação das ligações AMQP existentes numa ligação diferente.

Se `Complete` falhar, o que ocorre tipicamente no final do tratamento de mensagens e em alguns casos após minutos de trabalho de processamento, o pedido recetor pode decidir se preserva o estado da obra e ignora a mesma mensagem quando é entregue uma segunda vez, ou se joga fora o resultado do trabalho e as retrações à medida que a mensagem é realojada.

O mecanismo típico para identificar entregas de mensagens duplicadas é verificar o id de mensagens, que pode e deve ser definido pelo remetente a um valor único, possivelmente alinhado com um identificador do processo de origem. Um agendador de emprego provavelmente colocaria a identificação de mensagem no identificador do trabalho que está a tentar atribuir a um trabalhador com o trabalhador dado, e o trabalhador ignoraria a segunda ocorrência da atribuição de emprego se esse trabalho já estivesse feito.

> [!IMPORTANT]
> É importante notar que o bloqueio que o PeekLock adquire na mensagem é volátil e pode perder-se nas seguintes condições
>   * Atualização de serviço
>   * Atualização do SO
>   * Alterar propriedades na entidade (Fila, Tópico, Subscrição) enquanto segura o bloqueio.
>
> Quando o bloqueio for perdido, o Azure Service Bus gerará um LockLostException que será apresentado no código de aplicação do cliente. Neste caso, a lógica de relemisso padrão do cliente deve automaticamente entrar em vigor e voltar a tentar a operação.

## <a name="renew-locks"></a>Renovar fechaduras
O valor predefinido para a duração do bloqueio é **de 30 segundos**. Pode especificar um valor diferente para a duração do bloqueio no nível de fila ou subscrição. O cliente que possui o bloqueio pode renovar o bloqueio de mensagem utilizando métodos no objeto recetor. Em vez disso, pode utilizar a função automática de renovação do bloqueio, onde pode especificar a duração do tempo para a qual pretende continuar a renovar o bloqueio. 

## <a name="next-steps"></a>Passos seguintes
- Um caso especial de acordo é diferimento. Consulte o [adiamento da Mensagem](message-deferral.md) para mais detalhes. 
- Para aprender sobre letras [mortas, consulte as filas de letras mortas.](service-bus-dead-letter-queues.md)
- Para saber mais sobre as mensagens do Service Bus em geral, consulte [as filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)