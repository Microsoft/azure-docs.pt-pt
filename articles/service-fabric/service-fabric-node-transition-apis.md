---
title: Iniciar e parar de nós de cluster para testar aplicações do Azure Service Fabric | Documentos da Microsoft
description: Saiba como utilizar a inserção de falhas para testar uma aplicação do Service Fabric ao iniciar e parar de nós de cluster.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: df0e53736c08fd2c26c467def7328e85f2989f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718143"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Substituindo o nó de iniciar e parar nó APIs com a API de transição de nó

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que o nó de parar e iniciar as APIs de nó?

A API de nó Parar (geridos: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) deixa de um nó do Service Fabric.  Um nó do Service Fabric é o processo, não uma VM ou máquina – a máquina ou VM irá ainda ser em execução.  Para o resto do documento "nó" significa que o nó do Service Fabric.  Parar um nó coloca-o para um *parado* Estado em que não é um membro do cluster e não é possível hospedar serviços, desse modo simulando um *baixo* nó.  Isto é útil para injetar falhas do sistema para testar a aplicação.  A API de nó Iniciar (geridos: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) reverte a API de nó parar, o que traz o nó de volta para um estado normal.

## <a name="why-are-we-replacing-these"></a>Por que motivo são podemos substituir estes?

Conforme descrito anteriormente, um *parado* nó do Service Fabric é um nó intencionalmente direcionado através da API de nó parar.  R *baixo* nó é um nó que está inativo para qualquer outro motivo (por exemplo, a máquina ou VM está desativado).  Com a API de nó parar, o sistema não expõe informações diferenciar *parado* nós e *baixo* nós.

Além disso, alguns erros retornados pelas APIs não são o mais descritivos como poderiam ser.  Por exemplo, invocar a API de nó parar num já *parado* nó retornará o erro *InvalidAddress*.  Esta experiência poderia ser melhorada.

Além disso, a duração de que um nó é interrompido por é "infinita" até que a API de nó Iniciar é invocada.  Descobrimos que isso pode causar problemas e pode ser sujeitas a erros.  Por exemplo, vimos onde um usuário chamado a API de nó parar num nó e esquecer, em seguida, de problemas.  Mais tarde, que não ficou claro se o nó foi *para baixo* ou *parado*.


## <a name="introducing-the-node-transition-apis"></a>Apresentando as APIs de transição de nó

Que já abordamos esses problemas acima num novo conjunto de APIs.  A nova API de transição de nó (geridos: [StartNodeTransitionAsync()][snt]) pode ser utilizado para fazer a transição de um nó do Service Fabric para uma *parado* Estado, ou para fazer a transição de um *parado* estado para um segurança do estado normal.  Tenha em atenção que o "Start" no nome da API não faz referência a partir de um nó.  Ele se refere a partir de uma operação assíncrona que o sistema será executado para fazer a transição de nó para qualquer um *parado* ou ao estado.

**Utilização**

Se a API de transição de nó não lança uma exceção quando invocado, em seguida, o sistema aceitou a operação assíncrona e irá executá-lo.  Uma chamada bem-sucedida não implica que a operação é concluída ainda.  Para obter informações sobre o estado atual da operação, chamar a API de progresso de transição de nó (geridos: [GetNodeTransitionProgressAsync()][gntp]) com o guid utilizado ao invocar a API de transição de nó para esta operação.  A API de progresso de transição de nó retorna um objeto de NodeTransitionProgress.  Propriedade de estado deste objeto Especifica o estado atual da operação.  Se o estado é "executar", em seguida, a operação está em execução.  Se ele for concluído, a operação foi concluída sem erros.  Se tem falhas, Ocorreu um problema ao executar a operação.  Propriedade de exceção da propriedade resultado indica qual era o problema.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate para obter mais informações sobre a propriedade de estado e a secção "Utilização de exemplo" abaixo para obter exemplos de código.


**Diferenciar entre um nó de parada e um nó de baixo** se um nó estiver *parado* usando a API de transição de nó, a saída de uma consulta de nó (geridos: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) mostrará a que este nó tem um *IsStopped* valor da propriedade TRUE.  Tenha em atenção de que isso é diferente do valor dos *NodeStatus* propriedade, que será apresentada a mensagem *baixo*.  Se o *NodeStatus* propriedade tem um valor de *para baixo*, mas *IsStopped* é false, em seguida, o nó não foi parado com a API de transição de nó e é *para baixo*  devido a algum outro motivo.  Se o *IsStopped* propriedade é verdadeira e o *NodeStatus* propriedade é *baixo*, em seguida, foi parado com a API de transição de nó.

A partir de um *parado* nó usando a API de transição de nó retornará que ele funcione como um membro normal do cluster novamente.  O resultado da consulta de nó API apresentará *IsStopped* como false, e *NodeStatus* como algo que não está disponível para baixo (por exemplo,).


**Limitando duração** ao utilizar a API de transição de nó para parar um nó, um dos parâmetros necessários, *stopNodeDurationInSeconds*, representa o período de tempo em segundos para manter o nó *parado*.  Este valor tem de estar no intervalo permitido, que tem um mínimo de 600 e um máximo de 14400.  Após este período expira, o nó será reiniciado em si em cima do estado automaticamente.  Consulte a 1 de exemplo abaixo para obter um exemplo de utilização.

> [!WARNING]
> Evite misturar as APIs de transição de nó e o nó de parar e iniciar APIs de nó.  A recomendação é usar apenas a API de transição de nó.  > Se um nó foi já foi parado com a API de nó parar, que deve ser iniciado com a API de nó iniciar pela primeira vez antes de usar o > APIs de transição de nó.

> [!WARNING]
> Não não possível efetuar várias chamadas de APIs de transição de nó no mesmo nó em paralelo.  Em tal situação, a API de transição de nó serão > lançar um FabricException com um valor de propriedade de código de erro de NodeTransitionInProgress.  Depois que tiver uma transição de nó num nó específico > foi iniciado, deve aguardar até a operação de atinge um Estado terminal (concluído, Faulted ou ForceCancelled) antes de iniciar um > transição de novo no mesmo nó.  São permitidas chamadas de transição de nó paralela em nós diferentes.


#### <a name="sample-usage"></a>Utilização de exemplo


**Exemplo 1** -o exemplo seguinte utiliza a API de transição de nó para parar um nó.

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

**Exemplo 2** -o seguinte exemplo inicia uma *parado* nó.  Ele usa alguns métodos auxiliares do primeiro exemplo.

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

**Exemplo 3** -o exemplo a seguir mostra a utilização incorreta.  Esta utilização está incorreta porque o *stopDurationInSeconds* fornece é maior do que o intervalo permitido.  Uma vez que StartNodeTransitionAsync() irá falhar com um erro fatal, a operação não foi aceite e não deve ser chamada a API de progresso.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

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

**Exemplo 4** -o exemplo a seguir mostra as informações de erro que vão ser devolvidas a partir da API de progresso de transição de nó quando a operação iniciada pela API de transição de nó é aceite, mas falha mais tarde durante a execução.  No caso, ele falhará porque a API de transição de nó tenta iniciar um nó que não existe.  Este exemplo usa alguns métodos auxiliares do primeiro exemplo.

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
