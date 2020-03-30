---
title: Iniciar e parar os nós do cluster
description: Aprenda a usar a injeção de falhas para testar uma aplicação de Tecido de Serviço, iniciando e parando os nós do cluster.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609796"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Substituição do nó de arranque e do nó DE APIs com a API de Transição do Nó

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que fazem os "Stop Nó" e "Start Node APIs"?

O Stop Node API (gerido: [StopNodeAsync() ,][stopnode]PowerShell: [Stop-ServiceFabricNode][stopnodeps]) para um nó de tecido de serviço.  Um nó de tecido de serviço é processo, não um VM ou uma máquina – o VM ou a máquina ainda estarão em funcionamento.  Para o resto do documento "nó" significará o nó de tecido de serviço.  Parar um nó coloca-o num estado *de paragem* onde não é membro do agrupamento e não pode acolher serviços, simulando assim um nó de *baixo.*  Isto é útil para injetar falhas no sistema para testar a sua aplicação.  O API do Nó de Arranque (gerido: [StartNodeAsync() ,][startnode]PowerShell: [Start-ServiceFabricNode])][startnodeps]inverte a API do Nó stop, que traz o nó de volta a um estado normal.

## <a name="why-are-we-replacing-these"></a>Por que estamos substituindo isto?

Como descrito anteriormente, um nó de tecido de serviço *parado* é um nó intencionalmente direcionado usando a API stop nó de node.  Um nó *para baixo* é um nó que está para baixo por qualquer outra razão (por exemplo, o VM ou a máquina está desligado).  Com a API stop nóde, o sistema não expõe informações para diferenciar entre nós *parados* e nós *para baixo.*

Além disso, alguns erros devolvidos por estas APIs não são tão descritivos como poderiam ser.  Por exemplo, invocar a API stop nó de ponto num nó já *parado* devolverá o erro *InvalidAddress*.  Esta experiência poderia ser melhorada.

Além disso, a duração para a que um nó é interrompida é "infinita" até que a API do Nó de Início seja invocada.  Descobrimos que isto pode causar problemas e pode ser propenso a erros.  Por exemplo, temos visto problemas em que um utilizador invocou a API stop nó de ponto saque num nó e depois esqueceu-se disso.  Mais tarde, não ficou claro se o nó estava *parabaixo* ou *parado.*


## <a name="introducing-the-node-transition-apis"></a>Introdução das APIs de Transição do Nó

Abordámos estas questões acima num novo conjunto de APIs.  A nova API de Transição do Nó (gerida: [StartNodeTransitionAsync()][snt]pode ser usada para transitar um nó de tecido de serviço para um estado *parado,* ou para transitá-lo de um estado *parado* para um estado normal.  Por favor, note que o "Início" em nome da API não se refere a iniciar um nó.  Refere-se ao início de uma operação assíncrona que o sistema executará para transitar o nó para o estado *parado* ou iniciado.

**Utilização**

Se a API de Transição do Nó não abrir uma exceção quando invocada, então o sistema aceitou a operação assíncrona e irá executá-la.  Uma chamada bem sucedida não implica que a operação esteja terminada ainda.  Para obter informações sobre o estado atual da operação, ligue para a API de Transição do Nó (gerida: [GetNodeTransitionProgressAsync()][gntp]com o guia utilizado ao invocar a API de Transição do Nó para esta operação.  A API de transição do nó devolve um objeto NodeTransitionProgress.  A propriedade do Estado deste objeto especifica o estado atual da operação.  Se o Estado está a "correr", então a operação está a executar.  Se estiver concluída, a operação terminou sem erros.  Se for falhado, houve um problema em executar a operação.  A propriedade de Exceção da propriedade resulta indicará qual era a questão.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate mais informações sobre a propriedade do Estado e a secção "Utilização de amostras" abaixo para exemplos de código.


**Diferenciando entre um nó parado e um nó para baixo** Se um nó *for parado* usando a API de transição do nó, a saída de uma consulta de nó (gerida: [GetNodeListAsync(),][nodequery]PowerShell: [Get-ServiceFabricNode)][nodequeryps]mostrará que este nó tem um valor de propriedade *IsStopped* de verdade.  Note que isto é diferente do valor da propriedade *NodeStatus,* que dirá *Down*.  Se a propriedade *NodeStatus* tem um valor de *Down*, mas *IsStopped* é falso, então o nó não foi parado usando a API de transição do nó, e é *Down* devido a outra razão.  Se a propriedade *IsStopped* é verdadeira, e a propriedade *NodeStatus* é *down,* então foi parada usando a API de transição do nó.

Iniciar um nó *parado* usando a API de transição do nó voltará a funcionar como um membro normal do cluster novamente.  A saída da consulta do nó API mostrará *IsStopped* como falso, e *NodeStatus* como algo que não é Down (por exemplo, Up).


**Duração Limitada** Ao utilizar a API de transição do nó para parar um nó, um dos parâmetros necessários, *stopNodeDurationInSeconds,* representa a quantidade de tempo em segundos para manter o nó *parado*.  Este valor deve estar na gama permitida, que tem um mínimo de 600, e um máximo de 14400.  Após este tempo expirar, o nó recomeçará-se automaticamente em Estado de Up.  Consulte a amostra 1 abaixo para um exemplo de utilização.

> [!WARNING]
> Evite misturar APIs de transição do nó e o nó de paragem e iniciar APIs do nó.  A recomendação é usar apenas a API de Transição do Nó.  > Se um nó já tiver sido interrompido usando a API do nó stop, deve ser iniciado a utilizar primeiro a API do nó de arranque antes de utilizar as APIs de transição do nó de >.

> [!WARNING]
> As chamadas de AIs de Transição múltipla do Nó não podem ser feitas no mesmo nó em paralelo.  Em tal situação, a API de transição do nó irá > lançar uma Violação de Tecido com um valor de propriedade ErrorCode de NodeTransitionInProgress.  Uma vez iniciada a transição do nó num nó específico >, deve esperar até que a operação chegue a um estado terminal (Concluído, Defeituoso ou ForceCancelled) antes de iniciar uma nova transição > no mesmo nó.  São permitidas as chamadas de transição paralela do nó em diferentes nódosos.


#### <a name="sample-usage"></a>Utilização de Exemplo


**Amostra 1** - A amostra seguinte utiliza a API de transição do nó para parar um nó.

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

**Amostra 2** - A amostra seguinte inicia um nó *parado.*  Usa alguns métodos de ajuda desde a primeira amostra.

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

**Amostra 3** - A amostra seguinte mostra uma utilização incorreta.  Esta utilização está incorreta porque o *stopDurationInSeconds* que fornece é maior do que o intervalo permitido.  Uma vez que o StartNodeTransitionAsync() falhará com um erro fatal, a operação não foi aceite e a API de progresso não deve ser chamada.  Esta amostra utiliza alguns métodos de ajuda desde a primeira amostra.

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

**Amostra 4** - A amostra seguinte mostra as informações de erro que serão devolvidas da API de transição do nó quando a operação iniciada pela API de Transição do Nó for aceite, mas falha mais tarde durante a execução.  No caso, falha porque a API de Transição do Nó tenta iniciar um nó que não existe.  Esta amostra utiliza alguns métodos de ajuda desde a primeira amostra.

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

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
