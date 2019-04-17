---
title: Cenários de fan-out/fan-in em funções duráveis - Azure
description: Saiba como implementar um cenário de fan-out-fan-in na extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0bef5f1b64ec9f322070ba5c36cab138c7327da2
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608508"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in em funções duráveis - exemplo de cópia de segurança da Cloud

*Fan-out/fan-in* refere-se para o padrão de várias funções em execução em simultâneo e, em seguida, realizar algumas agregação nos resultados. Este artigo explica um exemplo que utiliza [funções duráveis](durable-functions-overview.md) para implementar um cenário fan-in/fan-out. O exemplo é uma função durável, que faz o backup de todos ou alguns dos conteúdos do site de uma aplicação para o armazenamento do Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Neste exemplo, as funções de carregar todos os ficheiros num diretório especificado recursivamente no armazenamento de Blobs. Eles também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuida de tudo. O principal problema encontrariam em é **escalabilidade**. Uma execução de função única só pode ser executado numa única VM, para que o débito será limitado pela taxa de transferência da VM única. Outro problema é **confiabilidade**. Se houver um falha meio, ou se todo o processo demora mais de 5 minutos, a cópia de segurança pode falhar num Estado parcialmente concluída. Em seguida, precisaria ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: um seria enumerar os ficheiros e adicionar os nomes de ficheiro a uma fila e outro teria de leitura da fila e carregue os ficheiros para o armazenamento de Blobs. Isso é melhor em termos de débito e a fiabilidade, mas ela requer que aprovisionar e gerir uma fila. Mais importante, uma complexidade considerável é introduzida em termos de **gerenciamento de estado** e **coordenação** se quiser fazer mais nada, como o relatório o número total de bytes carregados.

Uma abordagem de funções duráveis dá-lhe todos os benefícios mencionados com uma sobrecarga muito baixa.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

As secções seguintes explicam a configuração e o código que são utilizados para c# script. O código para o desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de cópia de segurança da cloud (código de exemplo do portal Visual Studio Code e o Azure)

O `E2_BackupSiteContent` função usa o padrão *Function* para as funções do orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Eis o código que implementa a função de orquestrador:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Esta função de orquestrador, essencialmente, faz o seguinte:

1. Demora um `rootDirectory` valor como um parâmetro de entrada.
2. Chama uma função para obter uma lista de recursiva dos arquivos sob `rootDirectory`.
3. Faz várias chamadas de função paralelos para carregar cada ficheiro no armazenamento de Blobs do Azure.
4. Aguarda todos os carregamentos concluir.
5. Devolve a soma total de bytes que foram carregados para o armazenamento de Blobs do Azure.

Observe que o `await Task.WhenAll(tasks);` (C#) e `yield context.df.Task.all(tasks);` linhas de (JavaScript). Todos os individuais chama-se para o `E2_CopyFileToBlob` função foram *não* aguardada. Isto é intencional para permitir que sejam executados em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll` (C#) ou `context.df.Task.all` (JavaScript), obtemos uma tarefa que não será concluído *até concluíram todas as operações de cópia*. Se estiver familiarizado com a tarefa paralela TPL (biblioteca) no .NET ou [ `Promise.all` ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) no JavaScript, em seguida, isso não é novidade para. A diferença é que estas tarefas poderiam estar em execução em várias VMs ao mesmo tempo, e a extensão de funções duráveis garante que a execução de ponto-a-ponto é resiliente a Reciclagem de processo.

> [!NOTE]
> Embora as tarefas são conceitualmente semelhantes às promessas de JavaScript, funções do orchestrator devem usar `context.df.Task.all` e `context.df.Task.any` em vez de `Promise.all` e `Promise.race` para gerir a paralelização de tarefa.

Depois de aguardar a partir `Task.WhenAll` (ou gerar resultados de `context.df.Task.all`), nós sabemos que todas as chamadas de função foram concluídas e devolveram os valores de volta para nós. Cada chamada para `E2_CopyFileToBlob` devolve o número de bytes carregado, para calcular a contagem de bytes total da soma é uma questão de adicionar todos os valores de retorno em conjunto.

## <a name="helper-activity-functions"></a>Funções de atividade de programa auxiliar

As funções de atividade do auxiliar, tal como acontece com outros exemplos, são funções normais que utilizam o `activityTrigger` acionar a ligação. Por exemplo, o *Function* de ficheiros para `E2_GetFileList` tem a seguinte aparência:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

E Eis a implementação:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A implementação JavaScript de `E2_GetFileList` utiliza o `readdirp` módulo recursivamente ler a estrutura de diretórios.

> [!NOTE]
> Deve estar se perguntando por que simplesmente não foi possível inserir esse código diretamente para a função de orquestrador. Poderia, mas isto iria quebrar as regras fundamentais de funções do orchestrator, que é que nunca deve fazer e/s, incluindo acesso de sistema de ficheiros local.

O *Function* de ficheiros para `E2_CopyFileToBlob` é simples da mesma forma:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A implementação do c# também é bastante simples. Isso acontece usar alguns recursos avançados do enlaces de funções do Azure (ou seja, a utilização do `Binder` parâmetro), mas não precisa se preocupar sobre esses detalhes com o objetivo deste passo a passo.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

A implementação de JavaScript não tem acesso para o `Binder` funcionalidade das funções do Azure, pelo que a [SDK de armazenamento do Azure para o nó](https://github.com/Azure/azure-storage-node) tomou seu lugar.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A implementação carrega o arquivo do disco e fluxos de forma assíncrona o conteúdo num blob no contentor "cópias de segurança", o mesmo nome. O valor de retorno é o número de bytes copiados para o armazenamento, que, em seguida, é utilizado pela função orchestrator para calcular a soma de agregação.

> [!NOTE]
> Este é um exemplo perfeito de mover as operações de e/s num `activityTrigger` função. Não apenas o trabalho pode ser distribuído em muitas VMs diferentes, mas também obtém os benefícios do ponto de verificação do progresso. Se o processo de host é terminado por qualquer motivo, sabe quais carregamentos já foi concluído.

## <a name="run-the-sample"></a>Executar o exemplo

Pode começar a orquestração ao enviar o pedido de HTTP POST seguinte.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> O `HttpStart` função que está a invocar só funciona com conteúdo formatado em JSON. Por esse motivo, o `Content-Type: application/json` cabeçalho é obrigatório e o caminho do diretório é codificado como uma cadeia de caracteres do JSON. Além disso, o fragmento HTTP pressupõe que existe uma entrada no `host.json` ficheiros que remove a predefinição `api/` prefixo a partir de todos os URLs de funções de Acionador HTTP. Pode encontrar a marcação para esta configuração no `host.json` ficheiro nos exemplos.

Acionadores de pedido esta HTTP a `E2_BackupSiteContent` orchestrator e passa a cadeia de caracteres `D:\home\LogFiles` como um parâmetro. A resposta fornece uma ligação para obter o estado da operação de cópia de segurança:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos ficheiros de registo tiver na sua aplicação function app, esta operação pode demorar vários minutos a concluir. Pode obter o estado mais recente através da consulta no URL no `Location` cabeçalho da resposta HTTP 202 anterior.

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

Neste caso, a função ainda está em execução. É possível ver a entrada que foi guardada o estado do orchestrator e a hora da última atualização. Pode continuar a utilizar o `Location` valores de cabeçalho para consultar de conclusão. Quando o estado é "concluído", verá um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Agora pode ver que a orquestração é concluída e aproximadamente quanto tempo demorou a concluir. Também pode ver um valor para o `output` campo, o que indica que cerca de 450 KB de registos foram carregados.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis a orquestração como um único arquivo c# num projeto do Visual Studio:

> [!NOTE]
> Terá de instalar o `Microsoft.Azure.WebJobs.Extensions.Storage` pacote Nuget para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo mostra como implementar o padrão fan-out/fan-in. O exemplo seguinte mostra como implementar o padrão de monitor usando [temporizadores duráveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de monitor](durable-functions-monitor.md)
