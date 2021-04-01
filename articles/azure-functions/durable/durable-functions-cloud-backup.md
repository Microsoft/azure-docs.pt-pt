---
title: Cenários fan-out/fan-in em Funções Duradouras - Azure
description: Saiba como implementar um cenário fan-out-fan-in na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027664"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/fan-in cenário em Funções Duradouras - Exemplo de backup em nuvem

*Fan-out/fan-in refere-se* ao padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação nos resultados. Este artigo explica uma amostra que utiliza [Funções Duradouras](durable-functions-overview.md) para implementar um cenário de fan-in/fan-out. A amostra é uma função durável que faz o back up all ou parte do conteúdo do site de uma aplicação no Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Nesta amostra, as funções carregam todos os ficheiros sob um diretório especificado recursivamente para o armazenamento de bolhas. Também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuide de tudo. O principal problema que encontraria é **a escalabilidade.** Uma única função só pode ser executada numa única máquina virtual, pelo que a produção será limitada pela produção desse único VM. Outro problema é **a fiabilidade.** Se houver uma falha a meio do processo, ou se todo o processo demorar mais de 5 minutos, o backup pode falhar num estado parcialmente concluído. Teria então de ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: uma enumeraria os ficheiros e adicionaria os nomes dos ficheiros a uma fila, e outra leria da fila e carregaria os ficheiros para o armazenamento de bolhas. Esta abordagem é melhor em termos de produção e fiabilidade, mas requer que você provisa e gere uma fila. Mais importante ainda, introduz-se uma complexidade significativa em termos de gestão e **coordenação** **do Estado,** se quiser fazer mais alguma coisa, como reportar o número total de bytes carregados.

Uma abordagem Funções Duradouras dá-lhe todos os benefícios mencionados com sobrecargas muito baixas.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E2_BackupSiteContent`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que liga `E2_GetFileList` para obter uma lista de ficheiros para fazer o back up e, em seguida, liga para fazer o back up de cada `E2_CopyFileToBlob` ficheiro.
* `E2_GetFileList`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que devolve uma lista de ficheiros num diretório.
* `E2_CopyFileToBlob`: Uma função de atividade que faz o back up de um único ficheiro para o Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>função orquestrador E2_BackupSiteContent

Esta função orquestradora faz essencialmente o seguinte:

1. Toma um `rootDirectory` valor como parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de ficheiros em `rootDirectory` .
3. Faz várias chamadas paralelas para carregar cada ficheiro no Azure Blob Storage.
4. Espera que todos os uploads se concretizem.
5. Devolve o total de bytes que foram enviados para o Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Aqui está o código que implementa a função orquestradora:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Reparem na `await Task.WhenAll(tasks);` linha. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* eram aguardadas, o que lhes permite correr em paralelo. Quando passarmos este conjunto de tarefas `Task.WhenAll` para, voltamos a ter uma tarefa que não estará concluída até que todas as *operações de cópia tenham terminado.* Se estiver familiarizado com a Biblioteca Paralela de Tarefa (TPL) em .NET, então isto não é novidade para si. A diferença é que estas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Funções Duradouras garante que a execução de ponta a ponta é resiliente para processar a reciclagem.

Depois de `Task.WhenAll` aguardarmos, sabemos que todas as chamadas de função foram concluídas e devolveram-nos valores. Cada chamada para `E2_CopyFileToBlob` devolver o número de bytes carregados, por isso calcular a contagem total de bytes é uma questão de adicionar todos esses valores de retorno juntos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Aqui está o código que implementa a função orquestradora:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Reparem na `yield context.df.Task.all(tasks);` linha. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram cedidas, o que lhes permite correr em paralelo. Quando passarmos este conjunto de tarefas `context.df.Task.all` para, voltamos a ter uma tarefa que não estará concluída até que todas as *operações de cópia tenham terminado.* Se está familiarizado com [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) o JavaScript, então isto não é novidade para si. A diferença é que estas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Funções Duradouras garante que a execução de ponta a ponta é resiliente para processar a reciclagem.

> [!NOTE]
> Embora as tarefas sejam conceptualmente semelhantes às promessas do JavaScript, as funções de orquestrador devem ser usadas `context.df.Task.all` e em vez de gerir a `context.df.Task.any` `Promise.all` `Promise.race` paralelização de tarefas.

Depois de `context.df.Task.all` ceder, sabemos que todas as chamadas de função completaram e devolveram-nos valores. Cada chamada para `E2_CopyFileToBlob` devolver o número de bytes carregados, por isso calcular a contagem total de bytes é uma questão de adicionar todos esses valores de retorno juntos.

# <a name="python"></a>[Python](#tab/python)

A função utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Aqui está o código que implementa a função orquestradora:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Reparem na `yield context.task_all(tasks);` linha. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram cedidas, o que lhes permite correr em paralelo. Quando passarmos este conjunto de tarefas `context.task_all` para, voltamos a ter uma tarefa que não estará concluída até que todas as *operações de cópia tenham terminado.* Se estás familiarizado com [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) o Python, então isto não é novidade para ti. A diferença é que estas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Funções Duradouras garante que a execução de ponta a ponta é resiliente para processar a reciclagem.

> [!NOTE]
> Embora as tarefas sejam conceptualmente semelhantes às de Python, as funções de orquestrador devem ser `yield` usadas, bem como as `context.task_all` APIs e `context.task_any` apis para gerir a paralelização de tarefas.

Depois de `context.task_all` ceder, sabemos que todas as chamadas de função completaram e devolveram-nos valores. Cada chamada para `E2_CopyFileToBlob` devolver o número de bytes carregados, para que possamos calcular a contagem total de bytes soma, adicionando todos os valores de retorno juntos.

---

### <a name="helper-activity-functions"></a>Funções de atividade do ajudante

As funções de atividade do ajudante, como com outras amostras, são apenas funções regulares que utilizam a ligação do `activityTrigger` gatilho.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList função de atividade

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O *function.jsarquivado* parece `E2_GetFileList` o seguinte:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

E aqui está a implementação:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A função utiliza o `readdirp` módulo (versão 2.x) para ler novamente a estrutura do diretório.

# <a name="python"></a>[Python](#tab/python)

O *function.jsarquivado* parece `E2_GetFileList` o seguinte:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

E aqui está a implementação:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> Deves estar a perguntar-te porque não puseste este código diretamente na função de orquestrador. Podia, mas isto quebraria uma das regras fundamentais das funções orquestradoras, que é que nunca deveriam fazer I/O, incluindo o acesso ao sistema de ficheiros local. Para obter mais informações, consulte as restrições do código de [função do Orquestrador](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob função de atividade

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Terá de instalar a `Microsoft.Azure.WebJobs.Extensions.Storage` embalagem NuGet para executar o código de amostra.

A função utiliza algumas funcionalidades avançadas das ligações Azure Functions (isto é, o uso do [ `Binder` parâmetro),](../functions-dotnet-class-library.md#binding-at-runtime)mas não precisa de se preocupar com esses detalhes para o propósito desta passagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *function.jsem* ficheiro `E2_CopyFileToBlob` é igualmente simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A implementação do JavaScript utiliza o [Azure Storage SDK para o Nó](https://github.com/Azure/azure-storage-node) para fazer o upload dos ficheiros para o Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

A *function.jsem* ficheiro `E2_CopyFileToBlob` é igualmente simples:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

A implementação python usa o [Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python) para fazer o upload dos ficheiros para o Azure Blob Storage.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

A implementação carrega o ficheiro a partir do disco e transmite assincroticamente o conteúdo numa bolha com o mesmo nome no recipiente "backups". O valor de retorno é o número de bytes copiados para armazenamento, que é então usado pela função orquestradora para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de mover as operações de E/S para uma `activityTrigger` função. Não só o trabalho pode ser distribuído por muitas máquinas diferentes, como também obtém os benefícios de pôr em conta o progresso. Se o processo de anfitrião for encerrado por qualquer motivo, sabe quais os uploads já concluídos.

## <a name="run-the-sample"></a>Executar o exemplo

Pode iniciar a orquestração, no Windows, enviando o seguinte pedido HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Alternativamente, numa App de Função Linux (Python atualmente apenas funciona no Linux para o Serviço de Aplicações), pode iniciar a orquestração assim:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> A `HttpStart` função que está a invocar funciona apenas com conteúdo com formato JSON. Por esta razão, o `Content-Type: application/json` cabeçalho é necessário e o caminho do diretório é codificado como uma corda JSON. Além disso, o snippet HTTP assume que existe uma entrada no `host.json` ficheiro que remove o `api/` prefixo prefixo precável de todas as funções de gatilho HTTP URLs. Pode encontrar a marcação para esta configuração no `host.json` ficheiro nas amostras.

Este pedido HTTP aciona o `E2_BackupSiteContent` orquestrador e passa a cadeia `D:\home\LogFiles` como parâmetro. A resposta fornece um link para obter o estado da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo do número de ficheiros de registo que tem na sua aplicação de função, esta operação pode demorar vários minutos a ser concluída. Pode obter o estado mais recente consultando o URL no `Location` cabeçalho da resposta http 202 anterior.

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

Neste caso, a função ainda está em funcionamento. Você pode ver a entrada que foi guardada no estado do orquestrador e a última hora atualizada. Pode continuar a usar os valores do `Location` cabeçalho para sondar para a conclusão. Quando o estado estiver "Concluído", vê um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Agora pode ver que a orquestração está completa e aproximadamente quanto tempo demorou a completar. Também se vê um valor para o `output` campo, o que indica que cerca de 450 KB de registos foram carregados.

## <a name="next-steps"></a>Passos seguintes

Esta amostra mostrou como implementar o padrão de ventilação/ventilador. A amostra seguinte mostra como implementar o padrão do monitor utilizando [temporizadores duráveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar a amostra do monitor](durable-functions-monitor.md)