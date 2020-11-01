---
title: 'Quickstart: Criar uma aplicação Java no Azure App Service'
description: Implemente o seu primeiro Serviço de Aplicações Java Hello World para Azure em minutos. O Azure Web App Plugin para Maven torna conveniente implementar aplicações Java.
keywords: azure, serviço de aplicações, web app, janelas, linux, java, maven, quickstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6d154d8b35c175b88e91545ddc1e78f58f9be685
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146829"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Quickstart: Criar uma aplicação Java no Azure App Service

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este quickstart mostra como usar o [CLI Azure](/cli/azure/get-started-with-azure-cli) com o [Azure Web App Plugin para maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar um ficheiro .jar, ou .war file. Utilize as linguetas para alternar entre as instruções de Java SE e Tomcat.


> [!NOTE]
> O mesmo também pode ser feito usando IDEs populares como IntelliJ e Eclipse. Consulte os nossos documentos semelhantes no [Azure Toolkit para IntelliJ Quickstart](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Azure Toolkit para Eclipse Quickstart](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![App de amostra em execução no Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar uma aplicação em Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clone o projeto de amostra [de bota de primavera.](https://github.com/spring-guides/gs-spring-boot)

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Mude o diretório para o projeto concluído.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Execute o seguinte comando Maven no pedido da Cloud Shell para criar uma nova aplicação chamada `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Em seguida, mude o seu diretório de trabalho para a pasta do projeto:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

O processo de implementação do Azure App Service utilizará automaticamente as suas credenciais Azure do CLI Azure. Se o CLI Azure não for instalado localmente, então o plugin Maven autenticará com Oauth ou início de sessão do dispositivo. Para mais informações, consulte [a autenticação com plugins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Executar o comando Maven abaixo para configurar a implantação. Este comando irá ajudá-lo a configurar o sistema operativo App Service, versão Java e versão Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.11.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando solicitado com a opção **Subscrição,** selecione o adequado `Subscription` introduzindo a impressão de número no arranque da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção difamar `<create>` pressionando a entrada.
1. Quando solicitado com opção **SO,** selecione **janelas** entrando `3` .
1. Utilize a versão Java padrão, 1.8, premindo a entrada.
1. Finalmente, prima a entrada no último aviso para confirmar as suas seleções.

    A sua saída de resumo será semelhante à do snippet mostrado abaixo.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando solicitado com a opção **Subscrição,** selecione o adequado `Subscription` introduzindo a impressão de número no arranque da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção difamar `<create>` pressionando a entrada.
1. Quando solicitado com opção **SO,** selecione **janelas** entrando `2` .
1. Utilize a versão Java padrão, 1.8, premindo a entrada.
1. Utilize o recipiente web predefinido, Tomcat 8.5, pressionando a entrada.
1. Finalmente, prima a entrada no último aviso para confirmar as suas seleções.

    A sua saída de resumo será semelhante à do snippet mostrado abaixo.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando solicitado com a opção **Subscrição,** selecione o adequado `Subscription` introduzindo a impressão de número no arranque da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção difamar `<create>` pressionando a entrada.
1. Quando solicitado com opção **SO,** selecione **linux** pressionando a entrada.
1. Utilize a versão Java padrão, 1.8, premindo a entrada.
1. Finalmente, prima a entrada no último aviso para confirmar as suas seleções.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando solicitado com a opção **Subscrição,** selecione o adequado `Subscription` introduzindo a impressão de número no arranque da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção difamar `<create>` pressionando a entrada.
1. Quando solicitado com opção **SO,** selecione **linux** pressionando a entrada.
1. Utilize a versão Java padrão, 1.8, premindo a entrada.
1. Utilize o recipiente web predefinido, Tomcat 8.5, pressionando a entrada.
1. Finalmente, prima a entrada no último aviso para confirmar as suas seleções.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Pode modificar as configurações do Serviço de Aplicações diretamente no `pom.xml` seu, se necessário. Algumas comuns estão listadas abaixo:

Propriedade | Necessário | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1` `v2` . . | 1.5.2
`<subscriptionId>` | false | Especificar o id de assinatura. | 0.1.0+
`<resourceGroup>` | true | Grupo de Recursos Azure para a sua Aplicação Web. | 0.1.0+
`<appName>` | true | O nome da sua Aplicação Web. | 0.1.0+
`<region>` | true | Especifica a região onde a sua Web App será hospedada; o valor predefinido é **westeurope** . Todas as regiões válidas na secção [Regiões Apoiadas.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | false | O nível de preços da sua Web App. O valor predefinido é **P1V2** .| 0.1.0+
`<runtime>` | true | A configuração do ambiente de tempo de execução, pode ver o detalhe [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<deployment>` | true | A configuração de implementação, pode ver os detalhes [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+

Tenha cuidado com os valores de `<appName>` e `<resourceGroup>` ( e `helloworld-1590394316693` `helloworld-1590394316693-rg` consequentemente na demonstração), eles serão usados mais tarde.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Implementar a aplicação

O plugin Maven utiliza credenciais de conta do CLI Azure para implementar nos Serviços de Aplicações. [Inscreva-se com o CLI Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Em seguida, pode implementar a sua aplicação Java para Azure usando o seguinte comando.

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, a sua aplicação estará pronta `http://<appName>.azurewebsites.net/` em ( `http://helloworld-1590394316693.azurewebsites.net` na demonstração). Abra o url com o seu navegador web local, você deve ver

![App de amostra em execução no Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Implementou a sua primeira aplicação Java para o Serviço de Aplicações.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando na Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Este comando pode demorar alguns minutos a ser executado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se ao Azure DB para PostgreSQL com Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurar CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informação sobre preços](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Registos e métricas agregados](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Aumentar verticalmente](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Azure para Java Developers Resources](/java/azure/)

> [!div class="nextstepaction"]
> [Configure a sua app Java](configure-language-java.md)
