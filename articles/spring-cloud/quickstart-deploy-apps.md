---
title: Quickstart - Construa e implemente aplicativos para Azure Spring Cloud
description: Descreve a implementação da aplicação para Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 727354593488cd5af77408eaa7c8d9e747a0b071
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879346"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Quickstart: Construa e implemente aplicativos para Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Neste arranque rápido, você constrói e implementa aplicações de microserviços para Azure Spring Cloud usando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Complete os arranques rápidos anteriores desta série:

  * [Serviço Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).
  * [Configurar o servidor de configuração Azure Spring Cloud](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>Transferir a aplicação de exemplo

Se tiver usado a Azure Cloud Shell até este ponto, mude para um pedido de comando local para os seguintes passos.

1. Crie uma nova pasta e clone o repositório de aplicações de amostra.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Navegue no diretório do repositório.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>Implementar PlanetWeatherProvider

1. Crie uma aplicação para o projeto PlanetWeatherProvider na sua instância Azure Spring Cloud.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Para ativar o registo automático do serviço, deu à app o mesmo nome que o valor da `spring.application.name`appsettings.jsdo projeto *em* arquivo:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Este comando pode demorar vários minutos a ser executado.

1. Mude o diretório para a pasta do `PlanetWeatherProvider` projeto.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Crie os binários e o ficheiro *.zip* a ser implantado.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > O ficheiro do projeto contém o seguinte XML para embalar as binários num ficheiro *.zip* depois de os ter escrito para a pasta *./publicação:*
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Desloque-se para Azure.

   Certifique-se de que o pedido de comando está na pasta do projeto antes de executar o seguinte comando.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   A `--main-entry` opção especifica o percurso relativo desde a pasta raiz do ficheiro *.zip* até ao ficheiro *.dll* que contém o ponto de entrada da aplicação. Após o serviço carregar o ficheiro *.zip,* extrai todos os ficheiros e pastas e tenta executar o ponto de entrada no ficheiro *.dll* especificado.

   Este comando pode demorar vários minutos a ser executado.

## <a name="deploy-solarsystemweather"></a>Implementar SolarSystemWeather

1. Crie outra aplicação no seu exemplo Azure Spring Cloud, desta vez para o projeto SolarSystemWeather:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` é o nome especificado no `SolarSystemWeather`appsettings.jsdo projeto *em* arquivo.

   Este comando pode demorar vários minutos a ser executado.

1. Mude o diretório para o `SolarSystemWeather` projeto.

   ```console
   cd ../solar-system-weather
   ```

1. Crie os binários e *.zip* ficheiro a ser implantado.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Desloque-se para Azure.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Este comando pode demorar vários minutos a ser executado.

## <a name="assign-public-endpoint"></a>Atribuir ponto final público

Para testar a aplicação, envie um pedido HTTP GET para a aplicação a `solar-system-weather` partir de um browser.  Para isso, precisa de um ponto final público para o pedido.

1. Para atribuir o ponto final, executar o seguinte comando.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --assign-endpoint true
   ```

1. Para obter o URL do ponto final, executar o seguinte comando.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Testar a aplicação

Envie um pedido GET para a `solar-system-weather` aplicação. Num browser, navegue para o URL público com `/weatherforecast` apêndice até ao final do mesmo. Por exemplo:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

A saída é JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Esta resposta mostra que ambas as aplicações de microserviços estão a funcionar. A `SolarSystemWeather` aplicação devolve os dados que recuperou da `PlanetWeatherProvider` aplicação.
::: zone-end

::: zone pivot="programming-language-java"
Este documento explica como construir e implementar aplicações de microserviços para a Azure Spring Cloud utilizando:
* CLI do Azure
* Maven Plugin
* Intellij

Antes de utilizar o Azure CLI ou o Maven, complete os exemplos que [disposim uma instância de Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md) e [configuram o servidor config](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](/java/azure/jdk/)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Procedimentos de implantação

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Construir as aplicações de microserviços localmente

1. Clone o repositório de aplicações de amostra para a sua conta Azure Cloud.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Mude o diretório e construa o projeto.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Compilar o projeto leva cerca de 5 minutos. Uma vez concluído, deverá ter ficheiros JAR individuais para cada serviço nas respetivas pastas.

### <a name="create-and-deploy-the-apps"></a>Criar e implementar as aplicações

1. Desaprote o nome do grupo de recursos predefinido e o nome do cluster utilizando os seguintes comandos:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Crie microserviços Azure Spring Cloud utilizando os ficheiros JAR construídos no passo anterior. Irá criar três aplicações: **gateway,** **auth-service** e **serviço de conta.**

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Precisamos de implementar aplicações criadas no passo anterior para o Azure. Utilize os seguintes comandos para implementar as três aplicações:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Atribuir ponto final público ao gateway

Precisamos de uma forma de aceder à aplicação através de um navegador web. A nossa aplicação gateway precisa de um ponto final virado para o público.

1. Atribuir o ponto final utilizando o seguinte comando:

    ```azurecli
    az spring-cloud app update -n gateway --assign-endpoint true
    ```

2. Consultar a aplicação **gateway** para o seu IP público para que possa verificar se a aplicação está em execução:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Clone e construa o repositório de aplicação de amostra

1. Clone o repositório git executando o seguinte comando:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Alterar o diretório e construir o projeto executando o seguinte comando:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gerei configurações e implementa para a Nuvem de primavera do Azure

1. Gere as configurações executando o seguinte comando na pasta raiz da PiggyMetrics contendo o POM principal. Se já se inscreveu com o Azure CLI, o comando irá automaticamente recolher as credenciais. Caso contrário, irá inscrevê-lo com instruções rápidas. Para mais informações, consulte a nossa [página wiki.](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    Ser-lhe-á pedido que selecione:
    * **Módulos:** Selecione `gateway` `auth-service` , e . `account-service` .
    * **Assinatura:** Esta é a sua subscrição usada para criar uma instância Azure Spring Cloud.
    * **Instância de serviço:** Este é o nome do seu exemplo de Azure Spring Cloud.
    * **Ponto final público:** Na lista de projetos fornecidos, insira o número que corresponde a `gateway` .  Isto dá-lhe acesso público.

1. O POM agora contém as dependências e configurações de plugin. Implemente as aplicações utilizando o seguinte comando. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Projeto de amostra de importação no IntelliJ

1. Faça o download e desaperte o repositório de origem para este tutorial, ou clone-o usando Git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Abra o diálogo IntelliJ **Welcome,** selecione **Import Project** para abrir o assistente de importação.

1. Selecione `piggymetric` a pasta.

    ![Projeto de Importação](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Implementar aplicativo gateway para Azure Spring Cloud
Para poder implementar no Azure tem de iniciar sôms com a sua conta Azure com o Azure Toolkit para o IntelliJ e escolher a sua subscrição. Para obter informações de insusição, consulte [a Instalação e a inscrição.](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

1. Clique com o botão direito no seu projeto no explorador de projetos IntelliJ e selecione **Azure**  ->  **Deploy to Azure Spring Cloud**.

    ![Desdobre-se para Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. No campo **Nome,** *apêndice :porta de entrada* para o **nome** existente .
1. Na caixa de texto **do Artefacto,** selecione *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Na caixa de texto **de assinatura,** verifique a sua subscrição.
1. Na caixa de texto **da Nuvem de primavera,** selecione a instância de Azure Spring Cloud que criou no [exemplo de Provision Azure Spring Cloud](./spring-cloud-quickstart-provision-service-instance.md).
1. Definir **ponto de final público** para *ativar*.
1. Na **App:** caixa de texto, selecione **Criar app...**.
1. Insira *o gateway,* em seguida, clique **EM OK**.

    ![Implementar para Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Na secção **de lançamento antes** do diálogo, clique duas vezes em Run *Maven Goal*.
1. Na caixa de texto do **diretório de trabalho,** navegue para a pasta *piggymetrics/gateway.*
1. Na caixa de texto da **linha de comando,** *introduza o pacote -DskipTests*. Clique em **OK**.
1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** O plug-in executará o comando `mvn package` na `gateway` aplicação e implantará o frasco gerado pelo `package` comando.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Implementar aplicações de serviço de auth e serviço de conta para Azure Spring Cloud
Pode repetir os passos acima para implementar `auth-service` e as `account-service` aplicações para Azure Spring Cloud:

1. Modifique o **Nome** e **o Artefacto** para identificar a `auth-service` aplicação.
1. Na **App:** caixa de texto, selecione **Criar app...** para criar `auth-service` aplicações.
1. Verifique se a opção **Ponto Final Público** está definida para *Desativado*.
1. Na secção **de lançamento antes** do diálogo, mude o **diretório de trabalho** para a pasta *piggymetrics/serviço de auth.*
1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** 
1. Repita estes procedimentos para configurar e implementar o `account-service` .
---

Navegue para o URL fornecido na saída os passos anteriores para aceder à aplicação PiggyMetrics. Por exemplo: `https://<service instance name>-gateway.azuremicroservices.io`

![Acesso PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Também pode navegar no portal Azure para encontrar o URL. 
1. Navegue para o serviço
2. Selecione **Apps**
3. Selecione **gateway**

    ![Navegar app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Encontre o URL na **porta de entrada | Página geral**

    ![Navegue em segundo aplicativo](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou recursos Azure que continuarão a acumular encargos se permanecerem na sua subscrição. Se não pretende continuar para o próximo quickstart, consulte [clean up resources](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Caso contrário, avance para o próximo arranque rápido:

> [!div class="nextstepaction"]
> [Registos, Métricas e Rastreios](spring-cloud-quickstart-logs-metrics-tracing.md)
