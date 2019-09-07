---
title: Cenários de Fan-out/Fan-in no Durable Functions-Azure
description: Saiba como implementar um cenário de Fan-out-in na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 81c1279670e786ddaa03946869773121a859d3b7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735242"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de Fan-out/Fan-in no exemplo de backup Durable Functions-Cloud

*Fan-out/Fan-in* refere-se ao padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação nos resultados. Este artigo explica um exemplo que usa [Durable Functions](durable-functions-overview.md) para implementar um cenário de Fan-in/Fan-out. O exemplo é uma função durável que faz backup de todo ou de parte do conteúdo do site de um aplicativo no armazenamento do Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Neste exemplo, as funções carregam todos os arquivos em um diretório especificado recursivamente no armazenamento de BLOBs. Eles também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuida de tudo. O principal problema que você encontraria é a **escalabilidade**. Uma única execução de função só pode ser executada em uma única VM, portanto, a taxa de transferência será limitada pela taxa de transferência dessa única VM. Outro problema é a **confiabilidade**. Se houver uma falha percorrendo ou se o processo inteiro levar mais de 5 minutos, o backup poderá falhar em um estado parcialmente concluído. Em seguida, ele precisaria ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: uma para enumerar os arquivos e adicionar os nomes de arquivo a uma fila, e outra seria ler da fila e carregar os arquivos no armazenamento de BLOBs. Isso é melhor em termos de produtividade e confiabilidade, mas requer que você provisione e gerencie uma fila. Mais importante, uma complexidade significativa é introduzida em termos de **Gerenciamento de estado** e **coordenação** se você quiser fazer mais alguma coisa, como relatar o número total de bytes carregados.

Uma abordagem Durable Functions dá a você todos os benefícios mencionados com uma sobrecarga muito baixa.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

As seções a seguir explicam a configuração e o código que C# são usados para scripts. O código para desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de backup na nuvem (Visual Studio Code e portal do Azure código de exemplo)

A `E2_BackupSiteContent` função usa o *Function. JSON* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Essa função de orquestrador basicamente faz o seguinte:

1. Usa um `rootDirectory` valor como um parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de arquivos em `rootDirectory`.
3. Faz várias chamadas de função paralelas para carregar cada arquivo no armazenamento de BLOBs do Azure.
4. Aguarda a conclusão de todos os carregamentos.
5. Retorna a soma total de bytes que foram carregados no armazenamento de BLOBs do Azure.

Observe as `await Task.WhenAll(tasks);` linhasC#() `yield context.df.Task.all(tasks);` e (JavaScript). Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram aguardadas. Isso é intencional para permitir que eles sejam executados em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll` (C#) ou `context.df.Task.all` (JavaScript), obtemos uma tarefa que não será concluída *até que todas as operações de cópia sejam concluídas*. Se você estiver familiarizado com a TPL (biblioteca paralela de tarefas) no .NET [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) ou em JavaScript, isso não será novidade para você. A diferença é que essas tarefas podem ser executadas em várias VMs simultaneamente, e a extensão de Durable Functions garante que a execução de ponta a ponta seja resiliente à reciclagem do processo.

> [!NOTE]
> Embora as tarefas sejam conceitualmente semelhantes às promessas do JavaScript, as funções de `context.df.Task.all` orquestrador devem `Promise.all` usar `Promise.race` e `context.df.Task.any` em vez de e gerenciar a paralelização de tarefas.

Depois de `Task.WhenAll` aguardar (ou produzir de `context.df.Task.all`), sabemos que todas as chamadas de função foram concluídas e retornaram valores de volta para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, portanto, o cálculo da contagem total de bytes da soma é uma questão de adicionar todos esses valores de retorno juntos.

## <a name="helper-activity-functions"></a>Funções de atividade do auxiliar

As funções de atividade auxiliar, assim como acontece com outros exemplos, são funções regulares que `activityTrigger` usam a associação de gatilho. Por exemplo, o arquivo `E2_GetFileList` *Function. JSON* é semelhante ao seguinte:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

E aqui está a implementação:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A implementação de JavaScript `E2_GetFileList` do usa `readdirp` o módulo para ler recursivamente a estrutura do diretório.

> [!NOTE]
> Você deve estar se perguntando por que não poderia simplesmente colocar esse código diretamente na função de orquestrador. Você poderia, mas isso quebraria uma das regras fundamentais das funções de orquestrador, o que é que elas nunca devem fazer e/s, incluindo o acesso ao sistema de arquivos local.

O arquivo *Function. JSON* do `E2_CopyFileToBlob` é igualmente simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A C# implementação também é bem simples. Ele usa alguns recursos avançados de associações de Azure Functions (ou seja, o uso do `Binder` parâmetro), mas você não precisa se preocupar com esses detalhes para fins deste passo a passos.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

A implementação do JavaScript não tem acesso ao `Binder` recurso do Azure functions, portanto, o [SDK do armazenamento do Azure para o nó](https://github.com/Azure/azure-storage-node) assume seu lugar.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A implementação carrega o arquivo do disco e transmite de forma assíncrona o conteúdo para um blob de mesmo nome no contêiner "backups". O valor de retorno é o número de bytes copiados para o armazenamento, que é então usado pela função de orquestrador para calcular a soma de agregação.

> [!NOTE]
> Este é um exemplo perfeito de movimentação de operações de e/s `activityTrigger` para uma função. O trabalho não só pode ser distribuído entre várias VMs diferentes, mas você também obtém os benefícios de ponto de verificação do progresso. Se o processo do host for encerrado por algum motivo, você saberá quais carregamentos já foram concluídos.

## <a name="run-the-sample"></a>Executar o exemplo

Você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` função que você está invocando só funciona com conteúdo formatado em JSON. Por esse motivo, o `Content-Type: application/json` cabeçalho é necessário e o caminho do diretório é codificado como uma cadeia de caracteres JSON. Além disso, o trecho http pressupõe que há uma entrada `host.json` no arquivo que remove o `api/` prefixo padrão de todas as URLs de funções de gatilho http. Você pode encontrar a marcação para essa configuração no `host.json` arquivo nos exemplos.

Essa solicitação HTTP aciona o `E2_BackupSiteContent` orquestrador e passa a cadeia `D:\home\LogFiles` de caracteres como um parâmetro. A resposta fornece um link para obter o status da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos arquivos de log você tem em seu aplicativo de funções, essa operação pode levar vários minutos para ser concluída. Você pode obter o status mais recente consultando a URL no `Location` cabeçalho da resposta http 202 anterior.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

Nesse caso, a função ainda está em execução. Você pode ver a entrada que foi salva no estado do orquestrador e a hora da última atualização. Você pode continuar a usar os `Location` valores de cabeçalho para sondar a conclusão. Quando o status for "concluído", você verá um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Agora você pode ver que a orquestração está concluída e aproximadamente quanto tempo levou para ser concluída. Você também verá um valor para o `output` campo, que indica que cerca de 450 KB de logs foram carregados.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Aqui está a orquestração como um único C# arquivo em um projeto do Visual Studio:

> [!NOTE]
> Será necessário instalar o `Microsoft.Azure.WebJobs.Extensions.Storage` pacote NuGet para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo mostrou como implementar o padrão Fan-out/Fan-in. O exemplo a seguir mostra como implementar o padrão de monitor usando [temporizadores duráveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de monitor](durable-functions-monitor.md)