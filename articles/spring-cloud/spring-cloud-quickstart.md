---
title: Início Rápido - Implementar a sua primeira aplicação do Azure Spring Cloud
description: Neste arranque rápido, implementamos uma aplicação Cloud spring para a Nuvem de primavera Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a4400adeff8907e2d4ff690a83c63d32da8031bd
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289315"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Este quickstart explica como implementar uma aplicação simples de microserviço Azure Spring Cloud para funcionar no Azure.

>[!NOTE]
> O suporte steeltoe para Azure Spring Cloud é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem que os clientes experimentem novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para obter mais informações sobre o suporte durante as pré-visualizações, consulte as [FAQ](https://azure.microsoft.com/support/faq/) ou apresente um [pedido de Apoio](../azure-portal/supportability/how-to-create-azure-support-request.md).

Ao seguir este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Gere um projeto básico steeltoe .NET Core
> * Provisionar uma instância de serviço Azure Spring Cloud
> * Construa e implemente a app com um ponto final público
> * Registos de fluxo em tempo real

O código de aplicação utilizado neste quickstart é uma aplicação simples construída com um modelo de projeto de API web .NET Core. Quando tiver concluído este exemplo, a aplicação será acessível online e poderá ser gerida através do portal Azure e do Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). O serviço Azure Spring Cloud suporta versões .NET Core 3.1 e versões posteriores.
* [Versão Azure CLI 2.0.67 ou posterior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git.](https://git-scm.com/)

## <a name="install-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Verifique se a sua versão Azure CLI é 2.0.67 ou posterior:

```azurecli
az --version
```

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

1. Faça login no Azure CLI

    ```azurecli
    az login
    ```

1. Se tiver mais do que uma subscrição, escolha a que pretende utilizar para este arranque rápido.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Gerar um projeto Steeltoe .NET Core

No Visual Studio, crie uma aplicação Core Web ASP.NET chamada "hello-world" com modelo de projeto API. Por favor, note que haverá um WeatherForecastController gerado automaticamente que será o nosso ponto final de teste mais tarde.

1. Crie uma pasta para o código fonte do projeto e gere o projeto.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Navegue no diretório do projeto.

   ```console
   cd hello-world
   ```

1. Editar a *appSettings.jsno* ficheiro para adicionar as seguintes definições:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Também em *appsettings.js,* altere o nível de registo para a `Microsoft` categoria de `Warning` `Information` . Esta alteração garante que os registos serão produzidos quando visualizar os registos de streaming num passo posterior.

   O *appsettings.jsem* arquivo agora parece semelhante ao seguinte exemplo:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Adicione dependências e uma `Zip` tarefa ao ficheiro *.csproj:*

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Os pacotes são para a Steeltoe Service Discovery e para a biblioteca de clientes Azure Spring Cloud. A `Zip` tarefa é a deslocação para Azure. Quando executam o `dotnet publish` comando, gera as binários na pasta *de publicação,* e esta tarefa fecha a pasta *de publicação* num ficheiro *.zip* que envia para a Azure.

3. No ficheiro *Program.cs,* adicione uma `using` diretiva e código que utiliza a biblioteca de clientes Azure Spring Cloud:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   })
                   .UseAzureSpringCloudService();
   ```

4. No ficheiro *Startup.cs,* adicione uma `using` diretiva e código que utiliza a Descoberta do Serviço Steeltoe no final do `ConfigureServices` e `Configure` métodos:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Construa o projeto para garantir que não há erros de compilação.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Prestação de uma instância de serviço

O procedimento a seguir cria uma instância de Azure Spring Cloud usando o portal Azure.

1. Abra o [portal do Azure](https://ms.portal.azure.com/). 

1. A partir da caixa de pesquisa superior, procure *a Azure Spring Cloud.*

1. Selecione *Azure Spring Cloud* a partir dos resultados.

   ![Início do ícone ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Na página Azure Spring Cloud, selecione **+ Adicionar**.

   ![Adicionar ícone ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:

   * **Subscrição** : Selecione a subscrição que pretende ser faturada para este recurso.
   * **Grupo de recursos** : Criar um novo grupo de recursos. O nome que introduzir aqui será usado em etapas posteriores como **\<resource group name\>** .
   * **Dados de serviço/Nome** : Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
   * **Região** : Selecione a região para a sua instância de serviço.

   ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Selecione **Rever e criar**.

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação

O procedimento a seguir constrói e implementa o projeto que criou anteriormente.

1. Certifique-se de que o pedido de comando ainda está na pasta do projeto.

1. Executar o seguinte comando para construir o projeto, publicar as binários e armazenar os binários num ficheiro *.zip* na pasta do projeto.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Crie uma aplicação no seu exemplo Azure Spring Cloud com um ponto final público atribuído. Utilize o mesmo nome de aplicação "hello-world" que especificou no *appsettings.js*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Implemente o ficheiro *.zip* para a aplicação.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   A `--main-entry` opção identifica o ficheiro *.dll* que contém o ponto de entrada da aplicação. Após o serviço carregar o ficheiro *.zip,* extrai todos os ficheiros e pastas e tenta executar o ponto de entrada no ficheiro *.dll* especificado por `--main-entry` .

   Demora alguns minutos a terminar a implementação da aplicação. Para confirmar que foi implementado, vá à lâmina **apps** no portal Azure.

## <a name="test-the-app"></a>Testar a aplicação

Uma vez concluída a implementação, aceda à aplicação no seguinte URL:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

A aplicação devolve dados JSON semelhantes ao seguinte exemplo:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Registos de fluxo em tempo real

Utilize o seguinte comando para obter registos em tempo real da App.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Os registos aparecem na saída:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

Para funcionalidades avançadas de análise de registos, visite o **separador Registos** no menu do [portal Azure](https://portal.azure.com/). Os troncos aqui têm uma latência de alguns minutos.
[![Registos Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Este quickstart explica como implementar uma aplicação simples de microserviço Azure Spring Cloud para funcionar no Azure. 

O código de aplicação utilizado neste tutorial é uma aplicação simples construída com o Initializr primavera. Quando tiver concluído este exemplo, a aplicação será acessível online e poderá ser gerida através do portal Azure.

Este quickstart explica como:

> [!div class="checklist"]
> * Gerar um projeto básico de Nuvem de primavera
> * Prestação de uma instância de serviço
> * Construa e implemente a app com um ponto final público
> * Registos de fluxo em tempo real

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* [Instalar JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Gere um projeto de Nuvem de primavera

Comece com [o Initializr de primavera](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) para gerar um projeto de amostra com dependências recomendadas para Azure Spring Cloud. A imagem a seguir mostra o Initializr criado para este projeto de amostra.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Página inicializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Clique **em Gerar** quando todas as dependências estiverem definidas. Faça o download e desembale o pacote e, em seguida, crie um controlador web para uma simples aplicação web adicionando `src/main/java/com/example/hellospring/HelloController.java` o seguinte:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar um exemplo de Azure Spring Cloud

O procedimento a seguir cria uma instância de Azure Spring Cloud usando o portal Azure.

1. Num novo separador, abra o [portal Azure](https://ms.portal.azure.com/). 

2. A partir da caixa de pesquisa superior, procure *a Azure Spring Cloud.*

3. Selecione *Azure Spring Cloud* a partir dos resultados.

    ![Início do ícone ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar**.

    ![Adicionar ícone ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:
    - **Subscrição** : Selecione a subscrição que pretende ser faturada para este recurso.
    - **Grupo de recursos** : Criar novos grupos de recursos para novos recursos é uma boa prática. Isto será usado em etapas posteriores como **\<resource group name\>** .
    - **Dados de serviço/Nome** : Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização** : Selecione a região para a sua instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Rever e criar**.

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
O procedimento a seguir constrói e implementa a aplicação utilizando o Azure CLI. Execute o seguinte comando na raiz do projeto.

1. Construa o projeto usando Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Se ainda não o instalou) Instale a extensão Azure Spring Cloud para o Azure CLI:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Crie a app com o ponto final público atribuído:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Implementar o ficheiro Jar para a aplicação `target\hellospring-0.0.1-SNAPSHOT.jar` (no Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Demora alguns minutos a terminar a implementação da aplicação. Para confirmar que foi implementado, vá à lâmina **apps** no portal Azure. Devia ver o estado do pedido.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

O procedimento a seguir utiliza o plug-in IntelliJ para a Azure Spring Cloud para implementar a aplicação da amostra no IntelliJ IDEA.  

### <a name="import-project"></a>Importar projeto

1. Abra o diálogo IntelliJ **Welcome** e selecione **Import Project** para abrir o assistente de importação.
1. Selecione `hellospring` a pasta.

    ![Projeto de Importação](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Implementar a aplicação
Para poder implementar no Azure tem de iniciar sôms com a sua conta Azure e escolher a sua subscrição.  Para obter informações de insusição, consulte [a Instalação e a inscrição.](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

1. Clique com o botão direito no seu projeto no explorador de projetos IntelliJ e selecione **Azure**  ->  **Deploy to Azure Spring Cloud**.

    [![Desdobre-se para Azure 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Aceite o nome para aplicação no campo **Nome.** **O nome** refere-se à configuração, não ao nome da aplicação. Os utilizadores normalmente não precisam de alterá-lo.
1. Na caixa de texto **do Artefacto,** selecione *hellospring-0.0.1-SNAPSHOT.jar*.
1. Na caixa de texto **de assinatura,** verifique a sua subscrição.
1. Na caixa de texto **da Nuvem de primavera,** selecione a instância de Azure Spring Cloud que criou no [exemplo de Provision Azure Spring Cloud](./spring-cloud-quickstart-provision-service-instance.md).
1. Definir **ponto de final público** para *ativar*.
1. Na **App:** caixa de texto, selecione **Criar app...**.
1. Introduza *a primavera de hellospring* e, em seguida, clique **em OK**.

    [![Implementar para Azure OK ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** O plug-in executará o comando `mvn package` na `hellospring` aplicação e implantará o frasco gerado pelo `package` comando.
---

Uma vez concluída a implementação, pode aceder à aplicação em `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Aplicativo de acesso a partir do navegador ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Registos de streaming em tempo real

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Utilize o seguinte comando para obter registos em tempo real da App.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Os registos aparecem nos resultados:

[Registos de ![ ](media/spring-cloud-quickstart-java/streaming-logs.png) streaming ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selecione **Azure Explorer,** em **seguida, Cloud primavera**.
1. Clique com o botão direito na aplicação de execução.
1. Selecione **'Registos** de streaming' da lista de drop-down.
1. Selecione a instância.

    [![Selecione registos ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) de streaming](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. O registo de streaming será visível na janela de saída.

    [![Saída de registo ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) de streaming](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Para funcionalidades avançadas de análise de registos, visite o **separador Registos** no menu do [portal Azure.](https://portal.azure.com/) Os troncos aqui têm uma latência de alguns minutos.

[![Registos Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou recursos Azure que continuarão a acumular encargos enquanto permanecem na sua subscrição. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando no Azure CLI:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Gere um projeto básico de Azure Spring Cloud
> * Prestação de uma instância de serviço
> * Construa e implemente a app com um ponto final público
> * Registos de fluxo em tempo real

Para aprender a usar mais capacidades da Azure Spring, avance para a série quickstart que implementa uma aplicação de amostra para Azure Spring Cloud:

> [!div class="nextstepaction"]
> [Construir e Executar Microserviços](spring-cloud-quickstart-sample-app-introduction.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
