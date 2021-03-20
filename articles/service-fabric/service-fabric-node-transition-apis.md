---
title: Iniciar e parar os nosdes de cluster
description: Aprenda a usar a injeção de falha para testar uma aplicação de Tecido de Serviço iniciando e interrompendo os nós do cluster.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89021260"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Substituição do nó de início e apis de nó de paragem por API de transição de nó

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que fazem os APIs do Nó de Paragem e do Nó de Início?

O Stop Node API (gerido: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode)][stopnodeps]para um nó de tecido de serviço.  Um nó de tecido de serviço está processo, não um VM ou máquina – o VM ou a máquina continuarão a funcionar.  Para o resto do documento "nó" significa-se nó de tecido de serviço.  Parar um nó coloca-o num estado *parado* onde não é membro do cluster e não pode acolher serviços, simulando assim um nó *para baixo.*  Isto é útil para injetar falhas no sistema para testar a sua aplicação.  O API do Nó de Início (gerido: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) inverte a API do nó de paragem, o que traz o nó de volta a um estado normal.

## <a name="why-are-we-replacing-these"></a>Porque estamos a substituir isto?

Como descrito anteriormente, um nó de tecido de serviço *parado* é um nó intencionalmente direcionado usando a API stop Node.  Um nó *para baixo* é um nó que está para baixo por qualquer outra razão (por exemplo, o VM ou a máquina está desligado).  Com a API stop node, o sistema não expõe informação para diferenciar entre nós *parados* e nós *para baixo.*

Além disso, alguns erros devolvidos por estas APIs não são tão descritivos como poderiam ser.  Por exemplo, invocar a API stop Node num nó já *parado* irá devolver o erro *InvalidAddress*.  Esta experiência poderia ser melhorada.

Além disso, a duração para a qual um nó é interrompido é "infinita" até que a API do nó de início seja invocada.  Descobrimos que isto pode causar problemas e pode ser propenso a erros.  Por exemplo, vimos problemas em que um utilizador invocou a API stop node num nó e depois esqueceu-se dele.  Mais tarde, não ficou claro se o nó estava *para baixo* ou *parado.*


## <a name="introducing-the-node-transition-apis"></a>Introduzindo as APIs de Transição do Nó

Abordámos estas questões acima num novo conjunto de APIs.  A nova API de Transição de Nó (gerida: [StartNodeTransitionAsync()][snt]pode ser usada para transitar um nó de tecido de serviço para um estado *parado,* ou para transferi-lo de um estado *parado* para um estado normal de subida.  Por favor, note que o "Iniciar" em nome da API não se refere ao início de um nó.  Refere-se ao início de uma operação assíncronea que o sistema executará para transitar o nó para *parar* ou iniciar o estado.

**Utilização**

Se a API de Transição de Nó não abrir uma exceção quando invocada, então o sistema aceitou a operação assíncrono e executará-a.  Uma chamada bem sucedida não implica que a operação esteja terminada ainda.  Para obter informações sobre o estado atual da operação, ligue para a API de Progressão de Transição no Nó (gerida: [GetNodeTransitionProgressAsync))][gntp]com o guia utilizado ao invocar a API de Transição do Nó para esta operação.  A API de Transição de Nó devolve um objeto NodeTransitionProgress.  A propriedade do Estado deste objeto especifica o estado atual da operação.  Se o Estado está a "correr", então a operação está a ser executada.  Se estiver concluída, a operação terminou sem erros.  Se for falhado, houve um problema na execução da operação.  A propriedade Resultado Exception indicará qual era o problema.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate mais informações sobre a propriedade do Estado e a secção "Utilização da amostra" abaixo para obter exemplos de código.


**Diferenciando entre um nó parado e um nó para baixo** Se um nó for *parado* usando a API de Transição de Nó, a saída de uma consulta de nó (gerida: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) mostrará que este nó tem um valor de propriedade *top isS* de verdade.  Note que isto é diferente do valor da propriedade *NodeStatus,* que dirá *Down*.  Se a propriedade *NodeStatus* tem um valor de *Down*, mas *isStopped* é falso, então o nó não foi parado usando a API de transição de nó, e está *baixo* devido a algum outro motivo.  Se a propriedade *isStopped* é verdadeira, e a propriedade *NodeStatus* é *Down*, então foi interrompida usando a API de Transição de Nó.

Iniciar um nó *parado* utilizando a API de Transição de Nó irá devolvê-lo ao funcionamento como um membro normal do cluster novamente.  A saída da consulta de nó API mostrará *isS top como* falso, e *NodeStatus* como algo que não está para baixo (por exemplo, Up).


**Duração Limitada** Ao utilizar a API de Transição de Nó para parar um nó, um dos parâmetros necessários, *stopNodeDurationInSeconds,* representa a quantidade de tempo em segundos para manter o nó *parado*.  Este valor deve estar na faixa permitida, que tem um mínimo de 600, e um máximo de 14400.  Após o termo deste tempo, o nó reiniciar-se-á automaticamente no estado up.  Consulte a amostra 1 abaixo para obter um exemplo de utilização.

> [!WARNING]
> Evite misturar APIs de Transição de Nó e APIs de nó de paragem e início.  A recomendação é utilizar apenas a API de Transição de Nó.  > Se já foi parado um nó utilizando a API do nó de paragem, deve começar a utilizar a API do nó de partida antes de utilizar as APIs de Transição de Nó >.

> [!WARNING]
> As várias chamadas APIs de Transição de Nó não podem ser feitas no mesmo nó em paralelo.  Em tal situação, a API de Transição de Nó > lançará uma FabricException com um valor de propriedade ErrorCode da NodeTransitionInProgress.  Uma vez iniciada uma transição de nó num nó específico, >, deve esperar até que a operação atinja um estado terminal (Concluído, Defeituoso ou ForceCancelled) antes de iniciar uma > nova transição no mesmo nó.  São permitidas chamadas de transição de nóis paralelos em diferentes nós.


#### <a name="sample-usage"></a>Utilização de Exemplo


**Amostra 1** - A amostra a seguir utiliza a API de Transição do Nó para parar um nó.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Amostra 2** - A amostra a seguir inicia um nó *parado.*  Usa alguns métodos de ajuda da primeira amostra.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Amostra 3** - A amostra a seguir mostra uma utilização incorreta.  Esta utilização é incorreta porque o *stopDurationInSegundos* que fornece é maior do que o intervalo permitido.  Uma vez que a StartNodeTransitionAsync() falhará com um erro fatal, a operação não foi aceite e a API em progresso não deve ser chamada.  Esta amostra utiliza alguns métodos de ajuda da primeira amostra.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Amostra 4** - A amostra a seguir mostra as informações de erro que serão devolvidas da API de Progressão de Transição do Nó quando a operação iniciada pela API de Transição de Nó é aceite, mas falha mais tarde durante a execução.  No caso, falha porque a API de Transição de Nó tenta iniciar um nó que não existe.  Esta amostra utiliza alguns métodos de ajuda da primeira amostra.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
