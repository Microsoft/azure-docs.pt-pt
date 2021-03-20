---
title: Monitorizar e diagnosticar ASP.NET Serviços core
description: Neste tutorial, aprenda a configurar a monitorização e diagnósticos para uma aplicação Azure Service Fabric ASP.NET Core.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e7fe68c2d0c51ffcc67693da722d9243ea3506f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840800"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Tutorial: Monitorizar e diagnosticar uma aplicação ASP.NET Core no Service Fabric com o Application Insights

Este tutorial é a quinta parte de uma série. Percorre os passos para configurar a monitorização e diagnósticos para uma aplicação core ASP.NET em execução num cluster de tecido de serviço usando o Application Insights. Iremos recolher telemetria da aplicação desenvolvida na primeira parte do tutorial [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

Na quarta parte desta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Configurar o Application Insights para a sua aplicação
> * Recolher a telemetria de resposta para rastrear a comunicação baseada em HTTP entre os serviços
> * Utilizar a funcionalidade da Mapa da Aplicação no Application Insights
> * Adicionar eventos personalizados através da API do Application Insights

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar a aplicação num cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Adicionar um ponto final HTTPS a um serviço de front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurar a monitorização e os diagnósticos da aplicação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e instale as cargas de trabalho de desenvolvimento do **Azure** e **ASP.NET e desenvolvimento web.**
* [Instalar o Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de votação de exemplo

Se não tiver construído a aplicação da amostra de voto na [primeira parte desta série tutorial,](service-fabric-tutorial-create-dotnet-app.md)pode descarregá-la. Num terminal ou janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurar um recurso do Application Insights

O Application Insights é a plataforma de gestão de desempenho de aplicações do Azure e a plataforma recomendada do Service Fabric para a monitorização e o diagnóstico de aplicações.

Para criar um recurso do Application Insights, navegue até ao [portal do Azure](https://portal.azure.com). **Selecione Criar um recurso** no menu de navegação à esquerda para abrir o Azure Marketplace. Selecione em **Monitor + Gestão** e, em seguida, **Insights de Aplicação**.

![Criar novo recurso de IA](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Agora tem de preencher as informações necessárias sobre os atributos do recurso a criar. Introduza um *Nome*, *Grupo de Recursos*, e *Subscrição* adequados. Defina a *Localização* para onde pretende implementar o cluster do Service Fabric no futuro. Neste tutorial, iremos implementar a aplicação num cluster local, por isso, o campo *Localização* é irrelevante. O *Tipo de Aplicação* deve ser deixado como “Aplicação Web ASP.NET”.

![Atributos de recurso de IA](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Uma vez preenchido as informações necessárias, **selecione Criar** para obter o recurso - deve demorar cerca de um minuto.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Adicionar o Application Insights aos serviços da aplicação

Lançar Visual Studio 2019 com privilégios elevados clicando no ícone do Estúdio Visual no Menu Iniciar e escolhendo **Run como administrador**. Selecione **File**  >  **Open**  >  **Project/Solution** e navegue para a aplicação de voto (criada na parte um do tutorial ou git clonado). *Voto aberto.sln*. Se for solicitado para restaurar os pacotes NuGet da aplicação, selecione **Sim**.

Siga estes passos para configurar insights de aplicações tanto para os serviços VoteWeb como VoteData:

1. Clique com o botão direito no nome do serviço e **selecione Adicionar > Serviços Conectados > Monitorização com Insights de Aplicações**.

    ![Configurar a IA](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>Dependendo do tipo de projeto, quando clicar no nome do serviço, poderá ter de selecionar Add-> Application Insights Telemetria ...

2. **Selecione Começar**.
3. Faça o sômis na conta que utiliza para a sua subscrição Azure e selecione a subscrição na qual criou o recurso Application Insights. Localize o recurso em *Recurso do Application Insights existente* na lista pendente "Recurso". Selecione **Registar-se** para adicionar Insights de Aplicação ao seu serviço.

    ![Registar IA](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Clique em **Concluir** assim que a caixa de diálogo que aparece concluir a ação.

> [!NOTE]
> Garanta que segue os passos acima para **ambos** os serviços da aplicação para concluir a configuração do Application Insights para a aplicação.
> O mesmo recurso do Application Insights é utilizado para ambos os serviços para ver pedidos de entrada e saída e a comunicação entre os serviços.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Adicionar o NuGet Microsoft.ApplicationInsights.ServiceFabric.Native aos serviços

O Application Insights tem dois NuGets específicos do Service Fabric que podem ser utilizados dependendo do cenário. Um é utilizado com serviços nativos do Service Fabric e o outro com contentores e executáveis convidados. Neste caso, iremos utilizar o NuGet Microsoft.ApplicationInsights.ServiceFabric.Native para tirar partido da compreensão de contexto de serviço que implica. Para ler mais sobre os pacotes NuGet específicos do Application Insights SDK e do Service Fabric, consulte os [Insights da Aplicação da Microsoft para o Tecido de Serviço](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

Aqui estão os passos para configurar o pacote NuGet:

1. Clique com o botão direito na **solução 'Votando'** no topo do Solution Explorer e selecione **Gerir pacotes NuGet para solução...**.
2. **Selecione Navegar** no menu de navegação superior da janela "NuGet - Solução" e verifique a caixa **de pré-relançar-se ao** lado da barra de pesquisa.
>[!NOTE]
>Poderá ter de instalar o pacote de Microsoft.ServiceFabric.Diagnostics.Internal de forma semelhante, se não for pré-instalado antes de instalar o pacote do Application Insights

3. Procure `Microsoft.ApplicationInsights.ServiceFabric.Native` e selecione o pacote NuGet apropriado.
4. À direita, selecione as duas caixas de verificação ao lado dos dois serviços na aplicação, **VotingWeb** e **VoteData** e selecione **Instalar**.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Selecione **OK** na caixa de diálogo de alterações de *pré-visualização* que aparece e aceite a *Aceitação* da Licença . Deste modo, irá concluir a adição do NuGet aos serviços.
6. Agora tem de configurar o inicializador de telemetria nos dois serviços. Para isso, abra *o VoteWeb.cs* e *VoteData.cs*. Para ambos os parâmetros, execute os dois passos seguintes:
    1. Adicione estes dois *usando* declarações no topo de cada *\<ServiceName> .cs,* após as declarações *existentes:*

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. Em ambos os ficheiros, na declaração de *devolução* aninhada dos *CreateServiceInstanceListeners()* ou *CreateServiceReplicaListeners()*, nos *serviços ConfigureServices,*  >  com os outros serviços singleton declarados, adicione:
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    Deste modo, irá adicionar o *Contexto do Serviço* à sua telemetria, permitindo-lhe compreender melhor a origem da sua telemetria no Application Insights. A declaração *devolver* aninhada no *VotingWeb.cs* deve ter este aspeto:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Da mesma forma, no *VotingData.cs*, deverá ter:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Verifique duas vezes se o `UseApplicationInsights()` método é chamado tanto no *VoteWeb.cs* como *no VoteData.cs* como mostrado acima.

>[!NOTE]
>Esta aplicação de exemplo utiliza http para os serviços para comunicar. Se desenvolver uma aplicação com o serviço Gestão remota V2 também terá de adicionar as seguintes linhas de código no mesmo local como fez acima

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Agora, está pronto para implementar a aplicação. Selecione **Iniciar** na parte superior (ou **F5),** e o Visual Studio irá construir e embalar a aplicação, configurar o seu cluster local e implementar a aplicação para o mesmo.

>[!NOTE]
>Pode obter um erro de construção se não tiver uma versão atualizada do .NET Core SDK instalado.

Uma vez que a aplicação seja feita, dirija-se para `localhost:8080` , onde você deve ser capaz de ver a aplicação de uma página única amostra de voto. Vote nalguns itens da sua preferência para criar alguns dados e telemetria de exemplo – escolhi as sobremesas!

![Votos de exemplo de IA](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Não hesite em *Remover* algumas das opções de votos, bem como quando tiver terminado de adicionar alguns votos.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Ver a telemetria e o mapa da aplicação no Application Insights

Vá para recurso do Application Insights no Portal do Azure.

Selecione **visão geral** para voltar à página de aterragem do seu recurso. Em **seguida, selecione Procurar** no topo para ver os vestígios a entrar. Demora alguns minutos para que os rastreios apareçam no Application Insights. Se não vir nenhum, aguarde um pouco e toque no botão **Atualizar** na parte superior.
![IA a ver rastreios](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Desloque-se para baixo na janela *Pesquisar* para mostrar toda a telemetria de entrada que obtém inicialmente com o Application Insights. Para cada ação realizada na aplicação de Votação, deverá haver um pedido PUT de saída de *VotingWeb* (Votos PUT/Put [nome]), um pedido PUT recebido de *VotingData* (PUT VoteData/Put [nome]), seguido de um par de pedidos GET para atualizar os dados que estão a ser apresentados. Também haverá um rastreio de dependência de HTTP no localhost, uma vez que estes são pedidos HTTP. Aqui está um exemplo do que verá pela forma como um voto é adicionado:

![Vestígios de pedido de amostra de IA](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Pode selecionar um dos vestígios para ver mais detalhes sobre o mesmo. Não há informações úteis sobre o pedido realizado pelo Application Insights, incluindo o *Tempo de resposta* e o *URL do Pedido*. Além disso, uma vez que adicionou o NuGet específico do Service Fabric, irá também obter dados sobre a sua aplicação no contexto de um cluster do Service Fabric na secção *Dados Personalizados* abaixo. Esta secção inclui o contexto de serviço, para que possa ver o *PartitionID* e *ReplicaId* da origem do pedido e localizar melhor os problemas quando diagnosticar erros na sua aplicação.

![Detalhes de rastreio de IA](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Além disso, pode selecionar o *mapa de aplicações* no menu esquerdo na página 'Vista Geral' ou selecionar o ícone do mapa da **App** para o levar ao Mapa da Aplicação mostrando os seus dois serviços ligados.

![Screenshot que destaca o mapa de aplicação no menu esquerdo.](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

O Mapa da aplicação pode ajudar a compreender melhor a topologia da aplicação, especialmente quando começar a adicionar vários serviços que funcionam em conjunto. Também lhe dá dados básicos sobre taxas de êxito de pedidos e pode ajudar a diagnosticar pedidos falhados para compreender onde as coisas poderão ter corrido mal. Para saber mais sobre como utilizar o Mapa da aplicação, veja [Mapa da Aplicação no Application Insights](../azure-monitor/app/app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Adicionar instrumentação personalizada à sua aplicação

Apesar de o Application Insights proporcionar muita telemetria desde o início, poderá querer adicionar mais instrumentação personalizada. Tal pode ser baseado nas suas necessidades de negócio ou para melhorar o diagnóstico quando as coisas correrem mal na sua aplicação. O Application Insights tem um API para ingerir eventos e métricas personalizadas, sobre o qual pode ler mais [aqui](../azure-monitor/app/api-custom-events-metrics.md).

Vamos adicionar alguns eventos personalizados a *VoteDataController.cs* (em *Controladores* > *VotingData*) para controlar quando os votos estão a ser adicionados e eliminados do *votesDictionary* subjacente.

1. Adicione `using Microsoft.ApplicationInsights;` no fim do outro através de declarações.
2. Declare um novo *TelemetryClient* no início da classe, abaixo da criação do *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Na função *Put()*, adicione um evento que confirme que um voto foi adicionado. Adicione `telemetry.TrackEvent($"Added a vote for {name}");` após a transação estar concluída, mesmo antes da declaração *OkResult*.
4. Em *Delete()*, há um "if/else" com base na condição de que o *votesDictionary* contém votos para uma determinada opção de voto.
    1. Adicione um evento que confirme a eliminação de um voto na declaração *if*, após *await tx.CommitAsync()*: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Adicione um evento para mostrar que a eliminação não ocorreu na declaração *else*, antes do elemento de matriz: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Eis um exemplo do aspeto que as funções *Put()* e *Delete()* poderão ter depois de adicionar os eventos:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Depois de realizar estas alterações, **Inicie** a aplicação para criar e implementar a versão mais recente da mesma. Uma vez que a aplicação é feita, dirija-se para `localhost:8080` , e adicione e elimine algumas opções de votação. Em seguida, volte ao seu recurso Application Insights para ver os vestígios para a última execução (como antes, os vestígios podem demorar 1-2 minutos a aparecer no Application Insights). Para todos os votos adicionados e eliminados, deverá ver agora um "Evento Personalizado* juntamente com toda a telemetria de resposta.

![eventos personalizados](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Configurar o Application Insights para a sua aplicação
> * Recolher a telemetria de resposta para rastrear a comunicação baseada em HTTP entre os serviços
> * Utilizar a funcionalidade da Mapa da Aplicação no Application Insights
> * Adicionar eventos personalizados através da API do Application Insights

Agora que criou a monitorização e diagnósticos para a sua aplicação ASP.NET, experimente o seguinte:

* [Explorar profundamente a monitorização e diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
* [Análise de eventos do Service Fabric com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* Para saber mais sobre o Application Insights, veja a [Documentação do Application Insights](/azure/application-insights/)
