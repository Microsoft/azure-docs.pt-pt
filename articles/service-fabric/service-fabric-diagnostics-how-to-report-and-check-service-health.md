---
title: Informe e verifique a saúde com o Tecido de Serviço Azure
description: Saiba como enviar relatórios de saúde do seu código de serviço e como verificar a saúde do seu serviço utilizando as ferramentas de monitorização de saúde que a Azure Service Fabric fornece.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: dc15ea7f062d3912c297050ffac755990fdd3b7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625947"
---
# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento dos serviços
Quando os seus serviços encontram problemas, a sua capacidade de responder e corrigir incidentes e interrupções depende da sua capacidade de detetar os problemas rapidamente. Se reportar problemas e falhas ao gestor de saúde Azure Service Fabric a partir do seu código de serviço, pode utilizar ferramentas de monitorização sanitária padrão que o Service Fabric fornece para verificar o estado de saúde.

Há três maneiras de relatar a saúde do serviço:

* Utilize [objetos partição](/dotnet/api/system.fabric.istatefulservicepartition) ou [CodePackageActivationContexto.](/dotnet/api/system.fabric.codepackageactivationcontext)  
  Pode utilizar o `Partition` e `CodePackageActivationContext` os objetos para relatar a saúde de elementos que fazem parte do contexto atual. Por exemplo, o código que funciona como parte de uma réplica só pode reportar a saúde nessa réplica, a partição a que pertence, e a aplicação da qual faz parte.
* Utilize `FabricClient`.   
  Pode utilizar `FabricClient` para reportar a saúde a partir do código de serviço se o cluster não estiver [seguro](service-fabric-cluster-security.md) ou se o serviço estiver a funcionar com privilégios administrativos. A maioria dos cenários do mundo real não usam aglomerados não protegidos, nem fornecem privilégios administrativos. Com `FabricClient` , você pode reportar saúde sobre qualquer entidade que faça parte do cluster. No entanto, o código de serviço apenas deve enviar relatórios relacionados com a sua própria saúde.
* Utilize as APIs REST no cluster, aplicação, aplicação implantada, serviço, pacote de serviço, partição, réplica ou níveis de nó. Isto pode ser usado para reportar a saúde a partir de um recipiente.

Este artigo percorre um exemplo que reporta saúde a partir do código de serviço. O exemplo também mostra como as ferramentas fornecidas pela Service Fabric podem ser usadas para verificar o estado de saúde. Este artigo pretende ser uma rápida introdução às capacidades de monitorização da saúde do Tecido de Serviço. Para obter informações mais detalhadas, pode ler a série de artigos aprofundados sobre saúde que começam com o link no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Deve ter o seguinte instalado:

* Visual Studio 2015 ou Visual Studio 2019
* SDK de tecido de serviço

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster local de dev seguro
* Open PowerShell com privilégios de administração, e executar os seguintes comandos:

![Comandos que mostram como criar um cluster dev seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implementar uma aplicação e verificar a sua saúde
1. Open Visual Studio como administrador.
1. Crie um projeto utilizando o modelo **stateful Service.**
   
    ![Criar uma aplicação de Tecido de Serviço com Serviço Stateful](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Prima **F5** para executar a aplicação no modo depuragem. A aplicação é implantada no cluster local.
1. Após a execução da aplicação, clique com o botão direito no ícone Do Cluster Manager local na área de notificação e selecione Gerir o **Cluster Local** a partir do menu de atalho para abrir o Service Fabric Explorer.
   
    ![Open Service Fabric Explorer da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. A saúde da aplicação deve ser exibida como nesta imagem. Neste momento, a aplicação deve ser saudável sem erros.
   
    ![Aplicação saudável no Explorador de Tecido de Serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Também pode verificar a saúde utilizando o PowerShell. Pode usar ```Get-ServiceFabricApplicationHealth``` para verificar a saúde de uma aplicação, e pode usar para verificar a ```Get-ServiceFabricServiceHealth``` saúde de um serviço. O relatório de saúde para a mesma aplicação no PowerShell está nesta imagem.
   
    ![Aplicação saudável em PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de saúde personalizados ao seu código de serviço
Os modelos de projeto do Tecido de Serviço no Estúdio Visual contêm código de amostra. Os seguintes passos mostram como pode reportar eventos de saúde personalizados a partir do seu código de serviço. Tais relatórios aparecem automaticamente nas ferramentas padrão de monitorização de saúde que o Service Fabric fornece, tais como Service Fabric Explorer, Azure portal health view e PowerShell.

1. Reabra a aplicação que criou anteriormente no Visual Studio, ou crie uma nova aplicação utilizando o modelo **stateful Service** Visual Studio.
1. Abra o ficheiro stateful1.cs e encontre a `myDictionary.TryGetValueAsync` chamada no `RunAsync` método. Pode ver que este método devolve um `result` que detém o valor atual do contador porque a lógica chave desta aplicação é manter uma contagem em execução. Se esta aplicação fosse uma aplicação real, e se a falta de resultado representasse uma falha, você quereria sinalizar esse evento.
1. Para reportar um evento de saúde quando a falta de resultados representa uma falha, adicione os seguintes passos.
   
    a. Adicione o `System.Fabric.Health` espaço de nomes ao ficheiro .cs Stateful1.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o seguinte código após a `myDictionary.TryGetValueAsync` chamada
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Reportamos a réplica da saúde porque está a ser reportada de um serviço estatal. O `HealthInformation` parâmetro armazena informações sobre o problema de saúde que está a ser reportado.
   
    Se tivesse criado um serviço apátrida, usasse o seguinte código
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Se o seu serviço estiver a funcionar com privilégios administrativos ou se o cluster não estiver [seguro,](service-fabric-cluster-security.md)também pode utilizar `FabricClient` para reportar a saúde como mostrado nos passos seguintes.  
   
    a. Crie o `FabricClient` caso após a `var myDictionary` declaração.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o seguinte código após a `myDictionary.TryGetValueAsync` chamada.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Vamos simular esta falha e vê-la aparecer nas ferramentas de monitorização da saúde. Para simular a falha, comente a primeira linha no código de relatório de saúde que adicionou anteriormente. Depois de comentar a primeira linha, o código será o seguinte exemplo.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Este código dispara o relatório de saúde cada vez `RunAsync` executado. Depois de fazer a alteração, prima **F5** para executar a aplicação.
1. Após a execução da aplicação, abra o Service Fabric Explorer para verificar a saúde da aplicação. Desta vez, o Service Fabric Explorer mostra que a aplicação não é saudável. A aplicação mostra-se pouco saudável porque o erro que foi reportado a partir do código que adicionámos anteriormente.
   
    ![Aplicação pouco saudável no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Se selecionar a réplica primária na vista da árvore do Explorador de Tecidos de Serviço, verá que **o Estado de Saúde** também indica um erro. O Service Fabric Explorer também exibe os detalhes do relatório de saúde que foram adicionados ao `HealthInformation` parâmetro no código. Pode ver os mesmos relatórios de saúde no PowerShell e no portal Azure.
   
    ![Saúde réplica no Explorador de Tecido de Serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanece no gestor de saúde até ser substituído por outro relatório ou até que esta réplica seja eliminada. Como não estabelecemos `TimeToLive` este relatório de saúde no `HealthInformation` objeto, o relatório nunca expira.

Recomendamos que a saúde seja reportada ao nível mais granular, que neste caso é a réplica. Também pode reportar saúde em `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para informar a saúde sobre `Application` , e , usar `DeployedApplication` `DeployedServicePackage`  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passos seguintes
* [Mergulho profundo na saúde do Tecido de Serviço](service-fabric-health-introduction.md)
* [REST API para reportar saúde do serviço](/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API para reportar saúde de aplicações](/rest/api/servicefabric/report-the-health-of-an-application)
