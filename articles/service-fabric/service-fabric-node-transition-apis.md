---
title: Iniciar e parar nós de cluster
description: Saiba como usar a injeção de falha para testar um aplicativo Service Fabric Iniciando e parando nós de cluster.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609796"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Substituindo o nó inicial e as APIs do nó de parada com a API de transição de nó

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que o nó de parada e as APIs de nó inicial fazem?

A API de nó de parada (Managed: [StopNodeAsync ()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) para um nó Service Fabric.  Um nó de Service Fabric é um processo, não uma VM ou um computador – a VM ou o computador ainda estará em execução.  Para o restante do documento, "node" significa Service Fabric nó.  Parar um nó o coloca em um estado *parado* em que ele não é membro do cluster e não pode hospedar serviços, simulando, assim, um nó *inoperante* .  Isso é útil para injetar falhas no sistema para testar seu aplicativo.  A API do nó de início (gerenciada: [StartNodeAsync ()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) reverte a API do nó de parada, que coloca o nó de volta em um estado normal.

## <a name="why-are-we-replacing-these"></a>Por que estamos substituindo esses?

Conforme descrito anteriormente, um nó Service Fabric *parado* é um nó intencionalmente direcionado usando a API de nó de parada.  Um nó *para baixo* é um nó inativo por qualquer outro motivo (por exemplo, a VM ou a máquina está desativada).  Com a API de nó de parada, o sistema não expõe informações para diferenciar entre nós *interrompidos* e nós *inativos* .

Além disso, alguns erros retornados por essas APIs não são tão descritivos quanto poderiam ser.  Por exemplo, invocar a API de nó de parada em um nó já *parado* retornará o erro *InvalidAddress*.  Essa experiência pode ser melhorada.

Além disso, a duração pela qual um nó é interrompido é "infinita" até que a API do nó inicial seja invocada.  Descobrimos que isso pode causar problemas e pode ser propenso a erros.  Por exemplo, vimos problemas em que um usuário invocou a API de nó de parada em um nó e, em seguida, esqueceu dela.  Mais tarde, ele ficou incorreto se o nó estava *inoperante* ou *parado*.


## <a name="introducing-the-node-transition-apis"></a>Introdução às APIs de transição de nó

Abordamos esses problemas acima em um novo conjunto de APIs.  A nova API de transição de nó (Managed: [StartNodeTransitionAsync ()][snt]) pode ser usada para fazer a transição de um nó de Service Fabric para um estado *parado* ou para fazer a transição de um estado para cima para *um estado de* cima normal.  Observe que o "início" no nome da API não se refere a iniciar um nó.  Ele se refere ao início de uma operação assíncrona que o sistema executará para fazer a transição do nó para o estado *parado* ou iniciado.

**Utilização**

Se a API de transição de nó não lançar uma exceção quando for invocada, o sistema aceitou a operação assíncrona e a executará.  Uma chamada bem-sucedida não significa que a operação foi concluída ainda.  Para obter informações sobre o estado atual da operação, chame a API de andamento da transição de nó (gerenciada: [GetNodeTransitionProgressAsync ()][gntp]) com o GUID usado ao invocar a API de transição de nó para esta operação.  A API de andamento da transição de nó retorna um objeto NodeTransitionProgress.  A propriedade de estado do objeto especifica o estado atual da operação.  Se o estado for "Running", a operação será executada.  Se estiver concluído, a operação foi concluída sem erros.  Se tiver falhado, houve um problema ao executar a operação.  A propriedade Exception da propriedade Result indicará o problema.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate para obter mais informações sobre a propriedade State e a seção "uso de exemplo" abaixo para obter exemplos de código.


**Diferenciando entre um nó parado e um nó inferior** Se um nó for *interrompido* usando a API de transição de nó, a saída de uma consulta de nó (gerenciada: [GetNodeListAsync ()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) mostrará que esse nó tem um valor de propriedade *IsStopped* de true.  Observe que isso é diferente do valor da propriedade *NodeStatus* , que *dirá.*  Se a propriedade *NodeStatus* tiver um valor de *down*, mas *IsStopped* for false, o nó não foi interrompido usando a API de transição de nó e está *inoperante* por algum outro motivo.  Se a propriedade *IsStopped* for true e a propriedade *NodeStatus* estiver *inoperante*, ela foi interrompida usando a API de transição de nó.

Iniciar um nó *parado* usando a API de transição de nó irá retorná-lo para funcionar como um membro normal do cluster novamente.  A saída da API de consulta de nó mostrará *IsStopped* como false e *NodeStatus* como algo que não está inoperante (por exemplo, para cima).


**Duração limitada** Ao usar a API de transição de nó para interromper um nó, um dos parâmetros necessários, *stopNodeDurationInSeconds*, representa a quantidade de tempo em segundos para manter o nó *parado*.  Esse valor deve estar no intervalo permitido, que tem um mínimo de 600 e um máximo de 14400.  Depois que esse tempo expirar, o nó será reiniciado automaticamente no estado ativo.  Consulte a amostra 1 abaixo para obter um exemplo de uso.

> [!WARNING]
> Evite misturar APIs de transição de nó e as APIs de nó de parada e nó de início.  A recomendação é usar apenas a API de transição de nó.  > Se um nó já foi interrompido usando a API de nó de parada, ele deve ser iniciado usando a API de nó inicial primeiro antes de usar as APIs de transição de nó de >.

> [!WARNING]
> Chamadas de APIs de transição de nó múltiplo não podem ser feitas no mesmo nó em paralelo.  Nessa situação, a API de transição de nó > lançar uma Fabricexception com um valor de Propriedade ErrorCode de NodeTransitionInProgress.  Depois que uma transição de nó em um nó específico tiver > iniciado, você deverá aguardar até que a operação atinja um estado de terminal (concluído, com falha ou ForceCancelled) antes de iniciar um > Nova transição no mesmo nó.  Chamadas de transição de nó paralelo em nós diferentes são permitidas.


#### <a name="sample-usage"></a>Exemplo de Utilização


**Exemplo 1** -o exemplo a seguir usa a API de transição de nó para interromper um nó.

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

**Exemplo 2** -o exemplo a seguir inicia um nó *parado* .  Ele usa alguns métodos auxiliares do primeiro exemplo.

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

**Exemplo 3** -o exemplo a seguir mostra o uso incorreto.  Esse uso está incorreto porque o *stopDurationInSeconds* que ele fornece é maior que o intervalo permitido.  Como StartNodeTransitionAsync () falhará com um erro fatal, a operação não foi aceita e a API de progresso não deve ser chamada.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

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

**Exemplo 4** -o exemplo a seguir mostra as informações de erro que serão retornadas da API de progresso de transição de nó quando a operação iniciada pela API de transição de nó for aceita, mas falhará mais tarde durante a execução.  No caso, ele falhará porque a API de transição de nó tenta iniciar um nó que não existe.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

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
