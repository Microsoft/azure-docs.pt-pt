---
title: Quickstart - Construa e implemente aplicativos para Azure Spring Cloud
description: Descreve a implementação da aplicação para Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 770dd021a09e3ba0b1c2c6742ded3a73424b042f
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951751"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Quickstart: Construa e implemente aplicativos para Azure Spring Cloud

Este documento explica como construir e implementar aplicações de microserviços para a Azure Spring Cloud utilizando:
* CLI do Azure
* Maven Plugin
* Intellij

Antes de utilizar o Azure CLI ou o Maven, complete os exemplos que [disposim uma instância de Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md) e [configuram o servidor config](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

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

1. Crie microserviços Azure Spring Cloud utilizando os ficheiros JAR construídos no passo anterior. Irá criar três aplicações: **gateway,** **auth-service**e **serviço de conta.**

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Precisamos de enviar as nossas aplicações para o Azure. Utilize os seguintes comandos para implementar as três aplicações:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Atribuir ponto final público ao gateway

Precisamos de uma forma de aceder à aplicação através de um navegador web. A nossa aplicação gateway precisa de um ponto final virado para o público.

1. Atribuir o ponto final utilizando o seguinte comando:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Consultar a aplicação **gateway** para o seu IP público para que possa verificar se a aplicação está em execução:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Clone e construa o repositório de aplicação de amostra

1. Clone o repositório git executando o seguinte comando:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Alterar o diretório e construir o projeto executando o seguinte comando:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gerei configurações e implementa para a Nuvem de primavera do Azure

1. Gere as configurações executando o seguinte comando na pasta raiz da PiggyMetrics contendo o POM principal. Se já se inscreveu com o Azure CLI, o comando irá automaticamente recolher as credenciais. Caso contrário, irá inscrevê-lo com instruções rápidas. Consulte [a](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) nossa página wiki para mais detalhes.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Ser-lhe-á pedido que selecione:
    * **Módulos:** selecione `gateway` `auth-service` , e 'serviço de conta'))
    * **Subscrição:** a sua subscrição com a azure Spring Cloud exemplo criado
    * **Instância de serviço:** nome da sua instância criada Azure Spring Cloud
    * **Ponto final público:** Na lista de projetos fornecidos, insira o número que corresponde `gateway` para lhe dar acesso público.

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
Para poder implementar no Azure, tem de iniciar sôm entrada na sua conta Azure com o Azure Toolkit para o IntelliJ e escolher a sua subscrição. Para obter informações de insusição, consulte [a Instalação e a inscrição.](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

1. Clique com o botão direito no seu projeto no explorador de projetos IntelliJ e selecione **Azure**  ->  **Deploy to Azure Spring Cloud**.

    ![Desdobre-se para Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. No apêndice do campo **Nome** *:porta de entrada* para o **nome** existente refere-se à configuração.
1. Na caixa de texto **do Artefacto,** selecione *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Na caixa de texto **de assinatura,** verifique a sua subscrição.
1. Na caixa de texto **da Nuvem de primavera,** selecione a instância de Azure Spring Cloud que criou no [exemplo de Provision Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Definir **ponto de final público** para *ativar*.
1. Na **App:** caixa de texto, selecione **Criar app...**.
1. Insira *o gateway,* em seguida, clique **EM OK**.

    ![Implementar para Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Na secção **de lançamento antes** do diálogo, clique duas vezes *Run Maven Goal**.
1. Na caixa de texto do **diretório de trabalho,** navegue para a pasta *piggymetrics/gateway.*
1. Na caixa de texto da **linha de comando,** *introduza o pacote -DskipTests*. Clique em **OK**.
1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** O plug-in executará o comando `mvn package` na `gateway` aplicação e implantará o frasco gerado pelo `package` comando.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Implementar aplicações de serviço de auth e serviço de conta para Azure Spring Cloud
Pode repetir os passos acima para implementar `auth-service` e as `account-service` aplicações para Azure Spring Cloud. Certificar-se:

1. Modifique o **Nome** e **o Artefacto** para identificar a `auth-service` aplicação.
1. Na **App:** caixa de texto, selecione **Criar app...** para criar `auth-service` aplicações.
1. Verifique se a opção **Ponto Final Público** está definida para *Desativado*.
1. Na secção **de lançamento antes** do diálogo, mude o **diretório de trabalho** para a pasta *piggymetrics/serviço de auth.*
1. Inicie a implementação clicando no botão **Executar** na parte inferior do diálogo da **aplicação Implementar Azure Spring Cloud.** 
1. Repita estes procedimentos para configurar e implementar o `account-service` .
---

Navegue para o URL fornecido na saída os passos anteriores para aceder à aplicação PiggyMetrics. por exemplo. `https://<service instance name>-gateway.azuremicroservices.io`

![Acesso PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Também pode navegar no portal Azure para encontrar o URL. 
1. Navegue para o serviço
2. Selecione **Apps**
3. Selecione **gateway**

    ![Navegar app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Encontre o URL no **portal Página geral**

    ![Navegue em segundo aplicativo](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Limpar os recursos
Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando na Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
Nos passos anteriores, também definiu o nome do grupo de recursos predefinido. Para limpar o padrão, executar o seguinte comando na Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Registos, Métricas e Rastreios](spring-cloud-quickstart-logs-metrics-tracing.md)
