---
title: Relatório e verifica a saúde com tecido de serviço Azure
description: Saiba como enviar relatórios de saúde do seu código de serviço e como verificar a saúde do seu serviço utilizando as ferramentas de monitorização da saúde que o Azure Service Fabric fornece.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464633"
---
# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento dos serviços
Quando os seus serviços encontram problemas, a sua capacidade de responder e corrigir incidentes e interrupções depende da sua capacidade de detetar rapidamente os problemas. Se reportar problemas e falhas ao gestor de saúde azure Service Fabric a partir do seu código de serviço, pode utilizar ferramentas padrão de monitorização da saúde que o Service Fabric fornece para verificar o estado de saúde.

Há três maneiras de informar a saúde do serviço:

* Utilize objetos [de partição](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) ou [códigos de activaçãoContexto.](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext)  
  Pode utilizar `Partition` os `CodePackageActivationContext` e objetos para relatar a saúde dos elementos que fazem parte do contexto atual. Por exemplo, o código que funciona como parte de uma réplica só pode reportar a saúde nessa réplica, na partição a que pertence, e na aplicação da sua parte.
* `FabricClient`Utilize.   
  Pode utilizar `FabricClient` para reportar a saúde do código de serviço se o cluster não estiver [seguro](service-fabric-cluster-security.md) ou se o serviço estiver a funcionar com privilégios administrativos. A maioria dos cenários do mundo real não usam aglomerados não seguros, nem proporcionam privilégios administrativos. Com, `FabricClient`pode reportar saúde em qualquer entidade que faça parte do cluster. Idealmente, no entanto, o código de serviço só deve enviar relatórios relacionados com a sua própria saúde.
* Utilize as APIs REST no cluster, aplicação, aplicação implementada, serviço, pacote de serviço, partição, réplica ou nó. Isto pode ser usado para reportar a saúde de dentro de um recipiente.

Este artigo acompanha-o através de um exemplo que reporta a saúde do código de serviço. O exemplo mostra também como as ferramentas fornecidas pelo Tecido de Serviço podem ser usadas para verificar o estado de saúde. Este artigo pretende ser uma introdução rápida às capacidades de monitorização da saúde do Tecido de Serviço. Para obter informações mais detalhadas, pode ler a série de artigos aprofundados sobre saúde que começam com o link no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Deve ter o seguinte instalado:

* Estúdio Visual 2015 ou Estúdio Visual 2019
* SDK de tecido de serviço

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um aglomerado de v seguro local
* Abra a PowerShell com privilégios de administração e execute os seguintes comandos:

![Comandos que mostram como criar um aglomerado de v seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implementar uma aplicação e verificar a sua saúde
1. Open Visual Studio como administrador.
1. Crie um projeto utilizando o modelo **de Serviço Estatal.**
   
    ![Criar uma aplicação de Tecido de Serviço com Serviço Estatal](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Pressione **F5** para executar a aplicação em modo dedepura. A aplicação está implantada para o aglomerado local.
1. Depois de a aplicação estar em execução, clique no ícone do Gestor de Cluster Local na área de notificação e selecione **Manage Local Cluster** a partir do menu de atalho para abrir o Service Fabric Explorer.
   
    ![Explorador de tecido de serviço aberto da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. A saúde da aplicação deve ser exibida como nesta imagem. Neste momento, a aplicação deve ser saudável sem erros.
   
    ![Aplicação saudável no Explorador de Tecido de Serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Também pode verificar a saúde utilizando o PowerShell. Você pode ```Get-ServiceFabricApplicationHealth``` usar para verificar a saúde ```Get-ServiceFabricServiceHealth``` de uma aplicação, e você pode usar para verificar a saúde de um serviço. O relatório de saúde da mesma aplicação na PowerShell está nesta imagem.
   
    ![Aplicação saudável na PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de saúde personalizados ao seu código de serviço
Os modelos de projeto service Fabric no Estúdio Visual contêm código de amostra. Os seguintes passos mostram como pode reportar eventos de saúde personalizados a partir do seu código de serviço. Estes relatórios aparecem automaticamente nas ferramentas padrão de monitorização da saúde que o Service Fabric fornece, como o Service Fabric Explorer, a visão de saúde do portal Azure e a PowerShell.

1. Reabra a aplicação que criou anteriormente no Visual Studio, ou crie uma nova aplicação utilizando o modelo **stateful Service** Visual Studio.
1. Abra o ficheiro Stateful1.cs `myDictionary.TryGetValueAsync` e encontre `RunAsync` a chamada no método. Pode ver que este `result` método devolve um que detém o valor atual do contador porque a lógica chave nesta aplicação é manter a contagem em funcionamento. Se esta aplicação fosse uma aplicação real, e se a falta de resultados representasse uma falha, você quereria sinalizar esse evento.
1. Para reportar um evento de saúde quando a falta de resultados representa uma falha, adicione os seguintes passos.
   
    a. Adicione `System.Fabric.Health` o espaço de nome ao ficheiro Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o seguinte `myDictionary.TryGetValueAsync` código após a chamada
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Relatamos a saúde da réplica porque está a ser reportada de um serviço estatal. O `HealthInformation` parâmetro armazena informações sobre o problema de saúde que está a ser reportado.
   
    Se tivesse criado um serviço apátrida, use o seguinte código
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Se o seu serviço estiver a funcionar com privilégios administrativos `FabricClient` ou se o cluster não estiver [seguro,](service-fabric-cluster-security.md)também pode usar para reportar a saúde, como mostra os seguintes passos.  
   
    a. Crie `FabricClient` a `var myDictionary` instância após a declaração.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o seguinte `myDictionary.TryGetValueAsync` código após a chamada.
   
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
1. Vamos simular esta falha e vê-la aparecer nas ferramentas de monitorização da saúde. Para simular a falha, comente a primeira linha do código de informação de saúde que adicionou anteriormente. Depois de comentar a primeira linha, o código será o seguinte exemplo.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Este código dispara o `RunAsync` relatório de saúde cada vez que executa. Depois de fazer a mudança, prima **F5** para executar a aplicação.
1. Depois de a aplicação estar em execução, abra o Service Fabric Explorer para verificar a saúde da aplicação. Desta vez, o Service Fabric Explorer mostra que a aplicação não é saudável. A aplicação mostra-se pouco saudável porque o erro que foi reportado a partir do código que adicionámos anteriormente.
   
    ![Aplicação pouco saudável no Explorador de Tecidos de Serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Se selecionar a réplica primária na vista da árvore do Service Fabric Explorer, verá que o Estado de **Saúde** também indica um erro. Service Fabric Explorer também apresenta os detalhes `HealthInformation` do relatório de saúde que foram adicionados ao parâmetro no código. Pode ver os mesmos relatórios de saúde na PowerShell e no portal Azure.
   
    ![Saúde de réplica no Explorador de Tecido de Serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanece no gestor de saúde até que seja substituído por outro relatório ou até que esta réplica seja eliminada. Uma vez que `TimeToLive` não apresentámos `HealthInformation` este relatório de saúde no objeto, o relatório nunca expira.

Recomendamos que a saúde seja reportada ao nível mais granular, que neste caso é a réplica. Também pode reportar `Partition`saúde em .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para relatar `Application`a `DeployedApplication`saúde sobre, e, `DeployedServicePackage`usar. `CodePackageActivationContext`

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passos seguintes
* [Mergulho profundo na saúde do tecido de serviço](service-fabric-health-introduction.md)
* [REST API para reportar saúde do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [Rest API para reportar saúde de aplicações](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

