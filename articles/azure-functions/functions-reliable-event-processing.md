---
title: Processamento fiável de eventos da Azure Functions
description: Evite faltar mensagens do Hub de Eventos nas Funções Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75561872"
---
# <a name="azure-functions-reliable-event-processing"></a>Processamento fiável de eventos da Azure Functions

O processamento de eventos é um dos cenários mais comuns associados à arquitetura sem servidores. Este artigo descreve como criar um processador de mensagens fiável com funções Azure para evitar perder mensagens.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Desafios dos fluxos de eventos em sistemas distribuídos

Considere um sistema que envia eventos a uma taxa constante de 100 eventos por segundo. A este ritmo, em minutos várias funções paralelas podem consumir os 100 eventos de entrada a cada segundo.

No entanto, qualquer uma das seguintes condições menos ótimas é possível:

- E se a editora do evento enviar um evento corrupto?
- E se a sua instância funções encontrar exceções não tratadas?
- E se um sistema a jusante ficar offline?

Como lida com estas situações preservando a entrada da sua aplicação?

Com filas, mensagens fiáveis vêm naturalmente. Quando emparelhada com um gatilho functions, a função cria um bloqueio na mensagem da fila. Se o processamento falhar, o bloqueio é libertado para permitir que outra instância retente o processamento. O processamento continua até que a mensagem seja avaliada com sucesso, ou é adicionada a uma fila de veneno.

Mesmo que uma única mensagem de fila possa permanecer num ciclo de retry, outras execuções paralelas continuam a impedir as mensagens restantes. O resultado é que a produção global permanece em grande parte inalterada por uma má mensagem. No entanto, as filas de armazenamento não garantem a encomenda e não estão otimizadas para as elevadas exigências de produção exigidas pelos Hubs de Evento.

Em contraste, o Azure Event Hubs não inclui um conceito de bloqueio. Para permitir funcionalidades como alta produção, vários grupos de consumidores e capacidade de repetição, os eventos do Event Hubs comportam-se mais como um leitor de vídeo. Os eventos são lidos a partir de um único ponto no fluxo por partição. A partir do ponteiro pode ler para a frente ou para trás a partir desse local, mas você tem que escolher mover o ponteiro para os eventos processarem.

Quando ocorrem erros num fluxo, se decidir manter o ponteiro no mesmo local, o processamento do evento é bloqueado até que o ponteiro seja avançado. Por outras palavras, se o ponteiro for parado para lidar com problemas de processamento de um único evento, os eventos não processados começam a acumular-se.

A Azure Functions evita impasses avançando o ponteiro do fluxo, independentemente do sucesso ou fracasso. Uma vez que o ponteiro continua a avançar, as suas funções precisam de lidar adequadamente com falhas.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Como funciona o Azure consome eventos de Hubs de Eventos

As Funções Azure consomem eventos do Event Hub enquanto pedalam pelos seguintes passos:

1. Um ponteiro é criado e persistido no Armazenamento Azure para cada partição do centro de eventos.
2. Quando novas mensagens são recebidas (num lote por padrão), o anfitrião tenta desencadear a função com o lote de mensagens.
3. Se a função completar a execução (com ou sem exceção) o ponteiro avança e um ponto de verificação é guardado na conta de armazenamento.
4. Se as condições impedirem a execução da função de ser concluída, o hospedeiro não progredirá no ponteiro. Se o ponteiro não for avançado, então os controlos posteriores acabam por processar as mesmas mensagens.
5. Repita os passos 2-4

Este comportamento revela alguns pontos importantes:

- *Exceções não tratadas podem fazer com que perca mensagens.* Execuções que resultem numa exceção continuarão a progredir no ponteiro.
- *As funções garantem pelo menos uma vez a entrega.* O seu código e sistemas dependentes podem ter de ter em conta o facto de [que a mesma mensagem pode ser recebida duas vezes](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Processamento de exceções

Regra geral, todas as funções devem incluir um [bloco de tentativa/captura](./functions-bindings-error-pages.md) ao mais alto nível de código. Especificamente, todas as funções que consomem eventos do Event Hubs devem ter um `catch` bloco. Dessa forma, quando se levanta uma exceção, o bloco de captura suprime o erro antes que o ponteiro progrida.

### <a name="retry-mechanisms-and-policies"></a>Mecanismos e políticas de retry

Algumas exceções são de natureza transitória e não reaparecem quando uma operação é tentada novamente momentos depois. É por isso que o primeiro passo é sempre voltar a tentar a operação. Você poderia escrever regras de processamento de retry, mas são tão comuns que várias ferramentas estão disponíveis. A utilização destas bibliotecas permite-lhe definir políticas robustas de retry, que também podem ajudar a preservar a ordem de processamento.

Introduzir bibliotecas de tratamento de falhas nas suas funções permite-lhe definir políticas básicas e avançadas de retry. Por exemplo, poderia implementar uma política que seguisse um fluxo de trabalho ilustrado pelas seguintes regras:

- Tente inserir uma mensagem três vezes (potencialmente com um atraso entre repetições).
- Se o resultado eventual de todas as tentativas for uma falha, adicione uma mensagem a uma fila para que o processamento possa continuar no fluxo.
- As mensagens corruptas ou não processadas são tratadas mais tarde.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) é um exemplo de uma biblioteca de resiliência e manipulação transitória de falhas para aplicações C#.

Ao trabalhar com bibliotecas de classe C# pré-cumpridas, [os filtros](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) de exceção permitem-lhe executar código sempre que ocorre uma exceção não tratada.

Amostras que demonstram como usar filtros de exceção estão disponíveis no repo [Azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Erros de não exceção

Algumas questões surgem mesmo quando um erro não está presente. Por exemplo, considere uma falha que ocorre no meio de uma execução. Neste caso, se uma função não completar a execução, o ponteiro de compensação nunca é progredido. Se o ponteiro não avançar, qualquer instância que corra depois de uma execução falhada continua a ler as mesmas mensagens. Esta situação fornece uma garantia "pelo menos uma vez".

A garantia de que cada mensagem é processada pelo menos uma vez implica que algumas mensagens podem ser processadas mais de uma vez. As suas aplicações de função devem estar cientes desta possibilidade e devem ser construídas em torno [dos princípios da idempotency](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Parar e reiniciar a execução

Embora alguns erros possam ser aceitáveis, e se a sua aplicação experimentar falhas significativas? É melhor parar de desencadear eventos até que o sistema atinja um estado saudável. Ter a oportunidade de parar o processamento é muitas vezes alcançado com um padrão de disjuntor. O padrão de disjuntor permite que a sua aplicação "quebre o circuito" do processo do evento e retome mais tarde.

São necessárias duas peças para implementar um disjuntor num processo de evento:

- Estado partilhado em todas as instâncias para acompanhar e monitorizar a saúde do circuito
- Processo principal que pode gerir o estado do circuito (aberto ou fechado)

Os detalhes da implementação podem variar, mas para partilhar o estado entre as instâncias é necessário um mecanismo de armazenamento. Pode optar por armazenar o estado em Azure Storage, uma cache Redis ou qualquer outra conta acessível por uma coleção de funções.

[As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) ou [entidades duráveis](./durable/durable-functions-overview.md) são um ajuste natural para gerir o fluxo de trabalho e o estado do circuito. Outros serviços podem funcionar tão bem, mas aplicações lógicas são usadas para este exemplo. Utilizando aplicações lógicas, pode parar e reiniciar a execução de uma função, dando-lhe o controlo necessário para implementar o padrão de disjuntor.

### <a name="define-a-failure-threshold-across-instances"></a>Defina um limiar de falha entre instâncias

Para responder a vários casos de processamento de eventos simultaneamente, é necessário um estado externo partilhado para monitorizar a saúde do circuito.

Uma regra que pode supor implementar pode impor isso:

- Se houver mais de 100 falhas em 30 segundos em todas as instâncias, então quebre o circuito e pare de acionar novas mensagens.

Os detalhes de implementação variarão dadas as suas necessidades, mas em geral pode criar um sistema que:

1. Falhas de registo numa conta de armazenamento (Armazenamento Azure, Redis, etc.)
1. Quando estiver registada nova falha, inspecione a contagem de rolos para ver se o limiar é cumprido (por exemplo, mais de 100 nos últimos 30 segundos).
1. Se o limiar for atingido, emita um evento para a Rede de Eventos Azure, dizendo ao sistema para quebrar o circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Gerir o estado do circuito com apps azure logic

A seguinte descrição destaca uma forma de criar uma App Azure Logic para travar uma aplicação Functions do processamento.

As Aplicações Lógicas Azure vêm com conectores incorporados a diferentes serviços, apresenta orquestrações imponentes, e é uma escolha natural para gerir o estado do circuito. Depois de detetar que o circuito precisa de quebrar, pode construir uma aplicação lógica para implementar o seguinte fluxo de trabalho:

1. Desencadear um fluxo de trabalho da Rede de Eventos e parar a função Azure (com o conector de recursos Azure)
1. Envie um e-mail de notificação que inclui uma opção para reiniciar o fluxo de trabalho

O destinatário do e-mail pode investigar a saúde do circuito e, quando apropriado, reiniciar o circuito através de um link no e-mail de notificação. À medida que o fluxo de trabalho reinicia a função, as mensagens são processadas a partir do último ponto de verificação do Event Hub.

Utilizando esta abordagem, não se perdem mensagens, todas as mensagens são processadas por ordem, e pode quebrar o circuito o tempo necessário.

## <a name="resources"></a>Recursos

- [Amostras fiáveis de processamento de eventos](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Disjuntor de funções duráveis azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

- [Manipulação de erros das Funções Azure](./functions-bindings-error-pages.md)
- [Automatizar o redimensionamento de imagens carregadas com o Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Criar uma função que se integra no Azure Logic Apps](./functions-twitter-email.md)
