---
title: Comunicar e verificar o estado de funcionamento com o Azure Service Fabric | Documentos da Microsoft
description: Saiba como enviar relatórios de estado de funcionamento a partir do código de serviço e como verificar o estado de funcionamento do seu serviço com as ferramentas de monitorização de estado de funcionamento do Azure Service Fabric fornece.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2126157f49bd978d2218986601245cae2e4157b6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821683"
---
# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento dos serviços
Quando os serviços de encontram problemas, sua capacidade de responder a e corrigir incidentes e interrupções depende da sua capacidade de detetar os problemas rapidamente. Se as reportar problemas e falhas para o Gestor de estado de funcionamento do Azure Service Fabric a partir do código de serviço, pode usar ferramentas que o Service Fabric fornece para verificar o estado de funcionamento de monitoramento de integridade padrão.

Existem três formas que pode comunicar o estado de funcionamento do serviço:

* Uso [partição](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) ou [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objetos.  
  Pode utilizar o `Partition` e `CodePackageActivationContext` objetos para reportar o estado de funcionamento dos elementos que fazem parte do contexto atual. Por exemplo, código que é executado como parte de uma réplica pode reportar o estado de funcionamento apenas nessa réplica, a partição que pertence a e a aplicação que é uma parte do.
* Use `FabricClient`.   
  Pode usar `FabricClient` estado de funcionamento do relatório do código de serviço, se o cluster não estiver [seguro](service-fabric-cluster-security.md) ou se o serviço está em execução com privilégios de administrador. A maioria dos cenários do mundo real não utilizem clusters não seguros, ou fornecer privilégios de administrador. Com `FabricClient`, pode relatar estado de funcionamento em qualquer entidade que é uma parte do cluster. O ideal é que, no entanto, o código de serviço só deve enviar relatórios relacionados com o seu próprio Estado de funcionamento.
* Utilize as APIs REST no cluster, aplicação, aplicação implementada, serviço, o pacote de serviço, partição, réplica ou níveis de nó. Isto pode ser utilizado para comunicar o estado de funcionamento de dentro de um contêiner.

Este artigo orienta-o num exemplo que reporte o estado de funcionamento do código de serviço. O exemplo também mostra como as ferramentas fornecidas pelo Service Fabric podem ser utilizadas para verificar o estado de funcionamento. Este artigo destina-se para ser uma introdução rápida para o estado de funcionamento monitorização de recursos do Service Fabric. Para obter informações mais detalhadas, leia a série de artigos detalhados sobre o estado de funcionamento que começam com a ligação no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado:

* Visual Studio 2015 ou Visual Studio 2017
* SDK do Service Fabric

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster de desenvolvimento seguro local
* Abra o PowerShell com privilégios de administrador e execute os seguintes comandos:

![Comandos que mostram como criar um cluster de desenvolvimento seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implementar uma aplicação e verificar o respetivo estado de funcionamento
1. Abra o Visual Studio como administrador.
1. Crie um projeto com o **serviço com estado** modelo.
   
    ![Criar uma aplicação do Service Fabric com o serviço com estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Prima **F5** para executar a aplicação no modo de depuração. A aplicação é implementada no cluster local.
1. Depois da aplicação está em execução, clique com o botão direito no ícone do Gestor de clusters locais na área de notificação e selecione **gerir Cluster Local** no menu de atalho para abrir o Explorador de recursos de infraestrutura do serviço.
   
    ![Abra o Explorador de recursos de infraestrutura do serviço da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. O estado de funcionamento do aplicativo deverá ser apresentado como esta imagem. Neste momento, a aplicação deve estar em bom estado sem erros.
   
    ![Aplicação de bom estado de funcionamento no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Também pode verificar o estado de funcionamento com o PowerShell. Pode usar ```Get-ServiceFabricApplicationHealth``` para verificar o estado de funcionamento de um aplicativo e pode usar ```Get-ServiceFabricServiceHealth``` para verificar o estado de funcionamento de um serviço. O relatório de estado de funcionamento para a mesma aplicação no PowerShell é nesta imagem.
   
    ![Aplicação de bom estado de funcionamento no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Adicionar eventos de estado de funcionamento personalizado ao seu código de serviço
Os modelos de projeto do Service Fabric no Visual Studio contêm código de exemplo. Os passos seguintes mostram como pode comunicar os eventos de estado de funcionamento personalizados a partir do código de serviço. Esses relatórios aparecem automaticamente nas ferramentas padrão para monitorização de estado de funcionamento que o Service Fabric fornece, como o Service Fabric Explorer, a vista de estado de funcionamento de portal do Azure e PowerShell.

1. Reabrir a aplicação que criou anteriormente no Visual Studio ou criar uma nova aplicação com o **serviço com estado** modelo do Visual Studio.
1. Abra o ficheiro de Stateful1.cs e encontre a `myDictionary.TryGetValueAsync` chamar o `RunAsync` método. Pode ver que esse método retorna um `result` que contém o valor atual do contador de uma vez que a lógica de chave nesse aplicativo é manter uma contagem em execução. Se este fosse um aplicativo real, e se a falta de resultado representado uma falha, desejaria sinalizar que o evento.
1. Para reportar um evento de estado de funcionamento quando a falta de resultado representa uma falha, adicione os seguintes passos.
   
    a. Adicionar o `System.Fabric.Health` espaço de nomes para o ficheiro de Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamar
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Elaboramos relatórios de estado de funcionamento de réplica porque está a ser comunicado a partir de um serviço com estado. O `HealthInformation` parâmetro armazena informações sobre o problema de estado de funcionamento que está a ser comunicado.
   
    Se tiver criado um serviço sem estado, utilize o seguinte código
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Se o seu serviço está em execução com privilégios de administrador ou se o cluster não estiver [segura](service-fabric-cluster-security.md), também pode utilizar `FabricClient` para Estado de funcionamento do relatório conforme mostrado nas etapas a seguir.  
   
    a. Criar a `FabricClient` instância depois do `var myDictionary` declaração.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamar.
   
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
1. Vamos simular esta falha e é exibido nas ferramentas de monitorização do Estado de funcionamento. Para simular a falha, comente a primeira linha no código de geração de relatórios de estado de funcionamento que adicionou anteriormente. Depois que comente a primeira linha, o código será a aparência semelhante ao seguinte exemplo.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Este código dispara o relatório de estado de funcionamento de cada vez `RunAsync` é executado. Depois de efetuar a alteração, prima **F5** para executar a aplicação.
1. Depois da aplicação está em execução, abra o Service Fabric Explorer para verificar o estado de funcionamento da aplicação. Desta vez, Service Fabric Explorer mostra que a aplicação está danificada. Isso é devido ao erro que foi relatado do código que adicionamos anteriormente.
   
    ![Aplicação de mau estado de funcionamento no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Se selecionar a réplica primária na vista de árvore do Service Fabric Explorer, verá que **estado de funcionamento** indica um erro, demasiado. Service Fabric Explorer também apresenta os detalhes do relatório de estado de funcionamento que foram adicionados para a `HealthInformation` parâmetro no código. Pode ver os relatórios de estado de funcionamento mesmo no PowerShell e o portal do Azure.
   
    ![Estado de funcionamento de réplica no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanece no Gestor de estado de funcionamento até que ele é substituído por outro relatório ou até que esta réplica seja eliminada. Uma vez que não definiu `TimeToLive` para este relatório de estado de funcionamento no `HealthInformation` de objeto, o relatório nunca expira.

Recomendamos que o estado de funcionamento deve ser informado sobre o nível mais granular, que neste caso é a réplica. Também pode comunicar o estado de funcionamento no `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para o estado de funcionamento do relatório no `Application`, `DeployedApplication`, e `DeployedServicePackage`, utilize `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passos Seguintes
* [Descrição detalhada da integridade do Service Fabric](service-fabric-health-introduction.md)
* [API REST para geração de relatórios de estado de funcionamento do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [API REST para geração de relatórios de estado de funcionamento da aplicação](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

