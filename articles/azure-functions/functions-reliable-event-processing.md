---
title: Azure Functions o processamento confiável de eventos
description: Evitar mensagens do hub de eventos ausentes no Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: 019c44cedba166dc1ac06a0244fa2b2e7930e673
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230373"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions o processamento confiável de eventos

O processamento de eventos é um dos cenários mais comuns associados à arquitetura sem servidor. Este artigo descreve como criar um processador de mensagens confiável com Azure Functions para evitar a perda de mensagens.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Desafios dos fluxos de eventos em sistemas distribuídos

Considere um sistema que envia eventos a uma taxa constante de 100 eventos por segundo. A essa taxa, em minutos, várias instâncias de funções paralelas podem consumir os eventos 100 de entrada a cada segundo.

No entanto, qualquer uma das seguintes condições menos ideais é possível:

- E se o Publicador de eventos enviar um evento corrompido?
- E se sua instância do Functions encontrar exceções sem tratamento?
- E se um sistema downstream ficar offline?

Como lidar com essas situações enquanto preserva a taxa de transferência de seu aplicativo?

Com as filas, as mensagens confiáveis são naturalmente. Quando emparelhado com um gatilho functions, a função cria um bloqueio na mensagem da fila. Se o processamento falhar, o bloqueio será liberado para permitir que outra instância seja processada novamente. Em seguida, o processamento continua até que a mensagem seja avaliada com êxito ou adicionada a uma fila de suspeitas.

Mesmo que uma única mensagem de fila possa permanecer em um ciclo de repetição, outras execuções paralelas continuam a manter a remoção da fila de mensagens restantes. O resultado é que a taxa de transferência geral permanece em grande parte não afetada por uma mensagem inadequada. No entanto, as filas de armazenamento não garantem pedidos e não são otimizadas para as demandas de alta taxa de transferência exigidas pelos hubs de eventos

Por outro lado, os hubs de eventos do Azure não incluem um conceito de bloqueio. Para permitir recursos como alta taxa de transferência, vários grupos de consumidores e capacidade de reprodução, os eventos de hubs de eventos se comportam mais como um player de vídeo. Os eventos são lidos de um único ponto no fluxo por partição. No ponteiro, você pode ler ou retroceder a partir desse local, mas você precisa escolher mover o ponteiro para eventos a serem processados.

Quando ocorrerem erros em um fluxo, se você decidir manter o ponteiro no mesmo ponto, o processamento de eventos será bloqueado até que o ponteiro seja avançado. Em outras palavras, se o ponteiro for interrompido para lidar com problemas de processamento de um único evento, os eventos não processados começarão a empilhando-los.

Azure Functions evita deadlocks avançando o ponteiro do fluxo, independentemente de êxito ou falha. Como o ponteiro continua avançando, suas funções precisam lidar com falhas adequadamente.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Como o Azure Functions consome eventos de hubs de eventos

O Azure Functions consome eventos do hub de eventos ao percorrer as seguintes etapas:

1. Um ponteiro é criado e mantido no armazenamento do Azure para cada partição do hub de eventos.
2. Quando novas mensagens são recebidas (em um lote por padrão), o host tenta disparar a função com o lote de mensagens.
3. Se a função concluir a execução (com ou sem exceção), o ponteiro avançará e um ponto de verificação será salvo na conta de armazenamento.
4. Se as condições impedirem a conclusão da execução da função, o host não conseguirá progredir o ponteiro. Se o ponteiro não for avançado, as verificações posteriores acabarão processando as mesmas mensagens.
5. Repita as etapas 2 a 4

Esse comportamento revela alguns pontos importantes:

- *Exceções sem tratamento podem causar a perda de mensagens.* As execuções que resultam em uma exceção continuarão a progredir o ponteiro.
- *As funções garantem a entrega pelo menos uma vez.* Seu código e sistemas dependentes podem precisar [considerar o fato de que a mesma mensagem pode ser recebida duas vezes](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Processamento de exceções

Como regra geral, cada função deve incluir um [bloco try/catch](./functions-bindings-error-pages.md) no nível mais alto de código. Especificamente, todas as funções que consomem eventos de hubs de eventos devem ter um bloco de `catch`. Dessa forma, quando uma exceção é gerada, o bloco catch trata o erro antes de o ponteiro progredir.

### <a name="retry-mechanisms-and-policies"></a>Mecanismos e políticas de repetição

Algumas exceções são transitórias por natureza e não são reexibidas quando uma operação é tentada novamente mais tarde. É por isso que a primeira etapa é sempre repetir a operação. Você pode escrever novas regras de processamento, mas elas são tão comuns que várias ferramentas disponíveis. O uso dessas bibliotecas permite que você defina políticas de repetição robustas, que também podem ajudar a preservar a ordem de processamento.

A introdução de bibliotecas de tratamento de falhas às suas funções permite que você defina políticas básicas e avançadas de repetição. Por exemplo, você pode implementar uma política que segue um fluxo de trabalho ilustrado pelas seguintes regras:

- Tente inserir uma mensagem três vezes (potencialmente com um atraso entre repetições).
- Se o resultado eventual de todas as novas tentativas for uma falha, adicione uma mensagem a uma fila para que o processamento possa continuar no fluxo.
- As mensagens corrompidas ou não processadas são tratadas posteriormente.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) é um exemplo de uma biblioteca de resiliência e de tratamento de falhas transitórias C# para aplicativos.

Ao trabalhar com bibliotecas de C# classes previamente compatíveis, os [filtros de exceção](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) permitem que você execute o código sempre que uma exceção sem tratamento ocorrer.

Exemplos que demonstram como usar filtros de exceção estão disponíveis no repositório [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) .

## <a name="non-exception-errors"></a>Erros de não exceção

Alguns problemas surgem mesmo quando um erro não está presente. Por exemplo, considere uma falha que ocorra no meio de uma execução. Nesse caso, se uma função não concluir a execução, o ponteiro de deslocamento nunca será progredido. Se o ponteiro não avançar, qualquer instância que for executada após uma falha de execução continuará lendo as mesmas mensagens. Essa situação fornece uma garantia "pelo menos uma vez".

A garantia de que cada mensagem seja processada pelo menos uma vez implica que algumas mensagens podem ser processadas mais de uma vez. Seus aplicativos de funções precisam estar cientes dessa possibilidade e devem ser criados com base nos [princípios do Idempotência](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Parar e reiniciar a execução

Embora alguns erros possam ser aceitáveis, e se seu aplicativo apresentar falhas significativas? Talvez você queira parar de disparar eventos até que o sistema alcance um estado íntegro. Ter o processamento de pausa de oportunidade geralmente é obtido com um padrão de disjuntor. O padrão de disjuntor permite que seu aplicativo "quebre o circuito" do processo de evento e retome em um momento posterior.

Há duas partes necessárias para implementar um disjuntor em um processo de evento:

- Estado compartilhado em todas as instâncias para acompanhar e monitorar a integridade do circuito
- Processo mestre que pode gerenciar o estado do circuito (aberto ou fechado)

Os detalhes da implementação podem variar, mas para compartilhar o estado entre as instâncias, você precisa de um mecanismo de armazenamento. Você pode optar por armazenar o estado no armazenamento do Azure, um cache Redis ou qualquer outra conta que possa ser acessada por uma coleção de funções.

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ou as [entidades duráveis](./durable/durable-functions-overview.md) são uma opção natural para gerenciar o fluxo de trabalho e o estado do circuito. Outros serviços também podem funcionar, mas os aplicativos lógicos são usados para este exemplo. Usando aplicativos lógicos, você pode pausar e reiniciar a execução de uma função, dando a você o controle necessário para implementar o padrão de disjuntor.

### <a name="define-a-failure-threshold-across-instances"></a>Definir um limite de falha entre instâncias

Para considerar várias instâncias processando eventos simultaneamente, é necessário persistir o estado externo compartilhado para monitorar a integridade do circuito.

Uma regra que você pode optar por implementar pode impor isso:

- Se houver mais de 100 falhas eventuals dentro de 30 segundos em todas as instâncias, quebre o circuito e pare de disparar em novas mensagens.

Os detalhes da implementação variam de acordo com suas necessidades, mas, em geral, você pode criar um sistema que:

1. Registrar falhas em uma conta de armazenamento (armazenamento do Azure, Redis, etc.)
1. Quando uma nova falha for registrada, inspecione a contagem de rolagem para ver se o limite foi atingido (por exemplo, mais de 100 nos últimos 30 segundos).
1. Se o limite for atingido, emita um evento para a grade de eventos do Azure informando ao sistema para interromper o circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Gerenciando o estado do circuito com os aplicativos lógicos do Azure

A descrição a seguir destaca uma maneira de criar um aplicativo lógico do Azure para interromper o processamento de um aplicativo de funções.

Os aplicativos lógicos do Azure vêm com conectores internos para diferentes serviços, recursos de orquestrações com monitoração de estado e é uma opção natural para gerenciar o estado do circuito. Depois de detectar que o circuito precisa ser interrompido, você pode criar um aplicativo lógico para implementar o seguinte fluxo de trabalho:

1. Disparar um fluxo de trabalho de grade de eventos e parar a função do Azure (com o conector de recursos do Azure)
1. Enviar um email de notificação que inclui uma opção para reiniciar o fluxo de trabalho

O destinatário do email pode investigar a integridade do circuito e, quando apropriado, reiniciar o circuito por meio de um link no email de notificação. À medida que o fluxo de trabalho reinicia a função, as mensagens são processadas a partir do último ponto de verificação do hub de eventos.

Usando essa abordagem, nenhuma mensagem é perdida, todas as mensagens são processadas em ordem e você pode dividir o circuito, desde que seja necessário.

## <a name="resources"></a>Recursos

- [Exemplos de processamento de eventos confiáveis](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Disjuntor de Durable Functions do Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

- [Tratamento de erro Azure Functions](./functions-bindings-error-pages.md)
- [Utilizar o Event Grid para automatizar o redimensionamento de imagens carregadas](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Criar uma função que se integra nas Azure Logic Apps](./functions-twitter-email.md)
