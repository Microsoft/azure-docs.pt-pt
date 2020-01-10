---
title: Relatar e verificar a integridade com o Azure Service Fabric
description: Saiba como enviar relatórios de integridade do seu código de serviço e como verificar a integridade do seu serviço usando as ferramentas de monitoramento de integridade fornecidas pelo Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464633"
---
# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento dos serviços
Quando seus serviços encontram problemas, sua capacidade de responder e corrigir incidentes e interrupções depende da sua capacidade de detectar os problemas rapidamente. Se você relatar problemas e falhas ao Gerenciador de integridade do Azure Service Fabric do seu código de serviço, poderá usar as ferramentas de monitoramento de integridade padrão que o Service Fabric fornece para verificar o status de integridade.

Há três maneiras de relatar a integridade do serviço:

* Use objetos [Partition](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) ou [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) .  
  Você pode usar os objetos `Partition` e `CodePackageActivationContext` para relatar a integridade dos elementos que fazem parte do contexto atual. Por exemplo, o código que é executado como parte de uma réplica pode relatar a integridade somente nessa réplica, a partição à qual ela pertence e o aplicativo do qual faz parte.
* Use `FabricClient`.   
  Você pode usar `FabricClient` para relatar a integridade do código de serviço se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o serviço estiver sendo executado com privilégios de administrador. A maioria dos cenários do mundo real não usa clusters não seguros ou fornece privilégios de administrador. Com `FabricClient`, você pode relatar a integridade em qualquer entidade que faça parte do cluster. O ideal é que, no entanto, o código de serviço só deva enviar relatórios relacionados à sua própria integridade.
* Use as APIs REST no cluster, aplicativo, aplicativo implantado, serviço, pacote de serviço, partição, réplica ou níveis de nó. Isso pode ser usado para relatar a integridade de dentro de um contêiner.

Este artigo orienta você por um exemplo que relata a integridade do código de serviço. O exemplo também mostra como as ferramentas fornecidas pelo Service Fabric podem ser usadas para verificar o status de integridade. Este artigo destina-se a ser uma breve introdução aos recursos de monitoramento de integridade do Service Fabric. Para obter informações mais detalhadas, você pode ler a série de artigos detalhados sobre a integridade que começam com o link no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte instalado:

* Visual Studio 2015 ou Visual Studio 2019
* SDK do Service Fabric

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster local de desenvolvimento seguro
* Abra o PowerShell com privilégios de administrador e execute os seguintes comandos:

![Comandos que mostram como criar um cluster de desenvolvimento seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implantar um aplicativo e verificar sua integridade
1. Abra o Visual Studio como administrador.
1. Crie um projeto usando o modelo de **serviço com estado** .
   
    ![Criar um aplicativo Service Fabric com serviço com estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Pressione **F5** para executar o aplicativo no modo de depuração. O aplicativo é implantado no cluster local.
1. Depois que o aplicativo estiver em execução, clique com o botão direito do mouse no ícone do Gerenciador de cluster local na área de notificação e selecione **gerenciar cluster local** no menu de atalho para abrir Service Fabric Explorer.
   
    ![Abrir Service Fabric Explorer da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. A integridade do aplicativo deve ser exibida como nesta imagem. Neste momento, o aplicativo deve estar íntegro sem erros.
   
    ![Aplicativo íntegro no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Você também pode verificar a integridade usando o PowerShell. Você pode usar ```Get-ServiceFabricApplicationHealth``` para verificar a integridade de um aplicativo e pode usar ```Get-ServiceFabricServiceHealth``` para verificar a integridade de um serviço. O relatório de integridade para o mesmo aplicativo no PowerShell está nesta imagem.
   
    ![Aplicativo íntegro no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de integridade personalizados ao seu código de serviço
Os modelos de projeto Service Fabric no Visual Studio contêm código de exemplo. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados do seu código de serviço. Esses relatórios são exibidos automaticamente nas ferramentas padrão para monitoramento de integridade que o Service Fabric fornece, como Service Fabric Explorer, portal do Azure exibição de integridade e PowerShell.

1. Reabra o aplicativo que você criou anteriormente no Visual Studio ou crie um novo aplicativo usando o modelo do Visual Studio de **serviço com estado** .
1. Abra o arquivo Stateful1.cs e localize a chamada `myDictionary.TryGetValueAsync` no método `RunAsync`. Você pode ver que esse método retorna um `result` que contém o valor atual do contador, pois a lógica de chave nesse aplicativo é manter uma contagem em execução. Se esse aplicativo era um aplicativo real e, se a falta de resultados representasse uma falha, você desejaria sinalizar esse evento.
1. Para relatar um evento de integridade quando a falta de resultado representa uma falha, adicione as etapas a seguir.
   
    a. Adicione o namespace `System.Fabric.Health` ao arquivo Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o código a seguir após a chamada de `myDictionary.TryGetValueAsync`
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Relatamos a integridade da réplica porque ela está sendo relatada de um serviço com estado. O parâmetro `HealthInformation` armazena informações sobre o problema de integridade que está sendo relatado.
   
    Se você tiver criado um serviço sem estado, use o código a seguir
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Se o serviço estiver sendo executado com privilégios de administrador ou se o cluster não for [seguro](service-fabric-cluster-security.md), você também poderá usar `FabricClient` para relatar a integridade, conforme mostrado nas etapas a seguir.  
   
    a. Crie a instância de `FabricClient` após a declaração de `var myDictionary`.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o código a seguir após a chamada de `myDictionary.TryGetValueAsync`.
   
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
1. Vamos simular essa falha e vê-la aparecer nas ferramentas de monitoramento de integridade. Para simular a falha, comente a primeira linha no código de relatório de integridade que você adicionou anteriormente. Depois de comentar a primeira linha, o código se parecerá com o exemplo a seguir.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Esse código dispara o relatório de integridade a cada vez que `RunAsync` é executado. Depois de fazer a alteração, pressione **F5** para executar o aplicativo.
1. Depois que o aplicativo estiver em execução, abra Service Fabric Explorer para verificar a integridade do aplicativo. Desta vez, Service Fabric Explorer mostra que o aplicativo não está íntegro. O aplicativo é exibido como não íntegro porque o erro relatado a partir do código que adicionamos anteriormente.
   
    ![Aplicativo não íntegro no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Se você selecionar a réplica primária no modo de exibição de árvore de Service Fabric Explorer, verá que o **estado de integridade** indica um erro também. Service Fabric Explorer também exibe os detalhes do relatório de integridade que foram adicionados ao parâmetro `HealthInformation` no código. Você pode ver os mesmos relatórios de integridade no PowerShell e no portal do Azure.
   
    ![Integridade da réplica no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Esse relatório permanece no Gerenciador de integridade até que seja substituído por outro relatório ou até que essa réplica seja excluída. Como não definimos `TimeToLive` para esse relatório de integridade no objeto `HealthInformation`, o relatório nunca expira.

É recomendável que a integridade seja relatada no nível mais granular, que nesse caso é a réplica. Você também pode relatar a integridade em `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para relatar a integridade em `Application`, `DeployedApplication`e `DeployedServicePackage`, use `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passos seguintes
* [Aprofunde-se na integridade Service Fabric](service-fabric-health-introduction.md)
* [API REST para a integridade do serviço de relatório](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [API REST para relatar a integridade do aplicativo](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

