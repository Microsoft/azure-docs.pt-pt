---
title: Cenários fan-out/fan-in em Funções Duráveis - Azure
description: Saiba como implementar um cenário de fan-out-fan-in na extensão Funções Duráveis para Funções Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562195"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in em Funções Duráveis - exemplo de backup em nuvem

*Fan-out/fan-in refere-se* ao padrão de execução de múltiplas funções simultaneamente e, em seguida, realizando alguma agregação nos resultados. Este artigo explica uma amostra que utiliza [Funções Duráveis](durable-functions-overview.md) para implementar um cenário de fã-in/fan-out. A amostra é uma função durável que faz com que todo ou parte do conteúdo do site de uma aplicação seja armazenado no Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Nesta amostra, as funções fazem o upload de todos os ficheiros sob um diretório especificado recursivamente no armazenamento de blob. Também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuide de tudo. O principal problema que se encontra é a **escalabilidade.** Uma única execução de função só pode ser executada numa única máquina virtual, pelo que a entrada será limitada pela entrada desse único VM. Outro problema é a **fiabilidade.** Se houver uma falha a meio, ou se todo o processo demorar mais de 5 minutos, a cópia de segurança pode falhar num estado parcialmente concluído. Teria então de ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: uma enumeraria os ficheiros e acrescentaria os nomes dos ficheiros a uma fila, e outra lia da fila e enviava os ficheiros para o armazenamento de bolhas. Esta abordagem é melhor em termos de entrada e fiabilidade, mas requer que você proponha e gere uma fila. Mais importante ainda, é introduzida uma complexidade significativa em termos de gestão e **coordenação** **do Estado,** se quiser fazer mais alguma coisa, como reportar o número total de bytes carregados.

Uma abordagem De Funções Duráveis dá-lhe todos os benefícios mencionados com despesas muito baixas.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E2_BackupSiteContent`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que liga `E2_GetFileList` para obter `E2_CopyFileToBlob` uma lista de ficheiros para fazer o fazer e, em seguida, chamadas para fazer o back-up de cada ficheiro.
* `E2_GetFileList`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que devolve uma lista de ficheiros num diretório.
* `E2_CopyFileToBlob`: Uma função de atividade que faz o apoio a um único ficheiro para o Armazenamento De Blob Azure.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent função orquestradora

Esta função orquestradora faz essencialmente o seguinte:

1. Tem `rootDirectory` um valor como parâmetro de entrada.
2. Chama uma função para obter uma lista `rootDirectory`recursiva de ficheiros em .
3. Faz várias chamadas paralelas para fazer upload de cada ficheiro para o Armazenamento De Blob Azure.
4. Espera que todos os uploads completem.
5. Devolve os bytes totais que foram enviados para o Armazenamento De Blob Azure.

# <a name="c"></a>[C #](#tab/csharp)

Aqui está o código que implementa a função orquestradora:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Reparem `await Task.WhenAll(tasks);` na fila. Todas as chamadas `E2_CopyFileToBlob` individuais para a função *não* eram aguardadas, o que lhes permite correr em paralelo. Quando passarmos este conjunto `Task.WhenAll`de tarefas para, recebemos de volta uma tarefa que não terminará até que todas as operações de *cópia tenham terminado.* Se está familiarizado com a Biblioteca Paralela de Tarefas (TPL) em .NET, então isso não é novidade para si. A diferença é que estas tarefas podem estar a funcionar em múltiplas máquinas virtuais simultaneamente, e a extensão das Funções Duráveis garante que a execução de ponta a ponta é resistente à reciclagem de processos.

Depois de `Task.WhenAll`esperarmos, sabemos que todas as chamadas de funções já terminaram e devolveram-nos valores. Cada chamada `E2_CopyFileToBlob` para devolver o número de bytes carregados, por isso calcular a contagem total de bytes é uma questão de juntar todos esses valores de retorno.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função utiliza a *função padrão.json* para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Aqui está o código que implementa a função orquestradora:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Reparem `yield context.df.Task.all(tasks);` na fila. Todas as chamadas `E2_CopyFileToBlob` individuais para a função *não* foram cessas, o que lhes permite correr em paralelo. Quando passarmos este conjunto `context.df.Task.all`de tarefas para, recebemos de volta uma tarefa que não terminará até que todas as operações de *cópia tenham terminado.* Se está familiarizado [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) com o JavaScript, então isto não é novidade para si. A diferença é que estas tarefas podem estar a funcionar em múltiplas máquinas virtuais simultaneamente, e a extensão das Funções Duráveis garante que a execução de ponta a ponta é resistente à reciclagem de processos.

> [!NOTE]
> Embora as tarefas sejam conceptualmente semelhantes às promessas `context.df.Task.all` `context.df.Task.any` do `Promise.all` JavaScript, as funções orquestradoras devem ser usadas e em vez de e `Promise.race` gerir a paralelinização da tarefa.

Depois de `context.df.Task.all`cedermos, sabemos que todas as chamadas de funções foram concluídas e devolveram-nos valores. Cada chamada `E2_CopyFileToBlob` para devolver o número de bytes carregados, por isso calcular a contagem total de bytes é uma questão de juntar todos esses valores de retorno.

---

### <a name="helper-activity-functions"></a>Funções de atividade do ajudante

As funções de atividade do ajudante, tal como acontece `activityTrigger` com outras amostras, são apenas funções regulares que utilizam a ligação do gatilho.

#### <a name="e2_getfilelist-activity-function"></a>função de atividade E2_GetFileList

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O ficheiro *função.json* para `E2_GetFileList` se parece com o seguinte:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

E aqui está a implementação:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A função `readdirp` utiliza o módulo (versão 2.x) para ler recursivamente a estrutura do diretório.

---

> [!NOTE]
> Podes estar a perguntar-te porque não podes pôr este código diretamente na função de orquestrador. Podias, mas isso quebraria uma das regras fundamentais das funções orquestradoras, que é que eles nunca deveriam fazer I/O, incluindo o acesso ao sistema de ficheiros local. Para mais informações, consulte as restrições de código de [função Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>função de atividade E2_CopyFileToBlob

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Terá de instalar `Microsoft.Azure.WebJobs.Extensions.Storage` o pacote NuGet para executar o código da amostra.

A função utiliza algumas características avançadas das ligações das Funções Azure (isto é, o uso do [ `Binder` parâmetro),](../functions-dotnet-class-library.md#binding-at-runtime)mas não precisa de se preocupar com esses detalhes para efeitos desta passagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O ficheiro *função.json* é `E2_CopyFileToBlob` igualmente simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A implementação do JavaScript utiliza o [SDK](https://github.com/Azure/azure-storage-node) de Armazenamento Azure para o Nó para fazer o upload dos ficheiros para o Armazenamento De Blob Azure.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

A implementação carrega o ficheiro a partir do disco e transmite o conteúdo sincronicamente para uma bolha com o mesmo nome no recipiente "backups". O valor de retorno é o número de bytes copiados para armazenamento, que é então usado pela função orquestradora para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de mover `activityTrigger` as operações de I/S para uma função. Não só o trabalho pode ser distribuído por muitas máquinas diferentes, como também obtém os benefícios de fazer o checkpoint do progresso. Se o processo de anfitrião for encerrado por qualquer motivo, sabe quais os uploads já concluídos.

## <a name="run-the-sample"></a>Executar o exemplo

Pode iniciar a orquestração enviando o seguinte pedido HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` função que está a invocar funciona apenas com conteúdo formado pela JSON. Por esta razão, o `Content-Type: application/json` cabeçalho é necessário e o caminho do diretório é codificado como uma corda JSON. Além disso, http snippet assume que `host.json` existe uma entrada `api/` no ficheiro que remove o prefixo predefinido de todas as funções de gatilho HTTP URLs. Pode encontrar a marcação para `host.json` esta configuração no ficheiro nas amostras.

Este pedido http `E2_BackupSiteContent` aciona o orquestrador `D:\home\LogFiles` e passa a corda como parâmetro. A resposta fornece um link para obter o estado da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos ficheiros de registo tem na sua aplicação de função, esta operação pode demorar vários minutos a ser concluída. Pode obter o estado mais recente consultando `Location` o URL no cabeçalho da resposta http 202 anterior.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Neste caso, a função ainda está em execução. Você pode ver a entrada que foi guardada no estado orquestrador e a última vez atualizada. Pode continuar a `Location` usar os valores cabeçalho para a sondagem para a conclusão. Quando o estado é "Concluído", vê-se um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Agora pode ver que a orquestração está completa e aproximadamente quanto tempo demorou a completar. Você também vê um `output` valor para o campo, o que indica que cerca de 450 KB de registos foram carregados.

## <a name="next-steps"></a>Passos seguintes

Esta amostra mostrou como implementar o padrão de ventilação/fã-in. A amostra seguinte mostra como implementar o padrão do monitor utilizando [tempos duráveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar a amostra do monitor](durable-functions-monitor.md)