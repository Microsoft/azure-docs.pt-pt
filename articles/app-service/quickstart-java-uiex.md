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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050380"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Quickstart: Criar uma aplicação Java no Azure App Service

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este quickstart mostra como usar o [CLI Azure](/cli/azure/get-started-with-azure-cli) com o [Azure Web App Plugin para maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) implementar um ficheiro .jar, ou ficheiro .war. 

Há também versões IDE deste artigo. Confira [o Azure Toolkit para intelliJ Quickstart](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Azure Toolkit para eclipse Quickstart](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, deve ter o seguinte:

+ Uma <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">Conta do Azure</abbr> com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ O [Azure CLI.](/cli/azure/install-azure-cli)

+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11.

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

O plugin Maven utiliza credenciais de conta do CLI Azure para implementar nos Serviços de Aplicações. [Inscreva-se com o CLI Azure](/cli/azure/authenticate-azure-cli) antes de continuar. Para mais informações, consulte [a autenticação com plugins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Criar uma aplicação Java

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

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. Configurar o plugin Maven

Execute o comando do Maven indicado abaixo para configurar a implementação. Este comando irá ajudá-lo a configurar o sistema operativo App Service, versão Java e versão Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando lhe for pedido para confirmar a opção **Subscription** (Subscrição), selecione a `Subscription` (Subscrição) adequada ao introduzir o número impresso no início da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção padrão `<create>` pressionando a entrada ou selecione uma aplicação existente.
1. Quando solicitado com opção **OS,** selecione **Windows** inserindo `3` .
1. Quando solicitado com a opção **Priceing Tier,** selecione **B2** entrando `2` .
1. Utilize a versão Java padrão, **Java 8,** premindo a entrada.
1. Por último, prima Enter no último pedido para confirmar as suas seleções.

    A sua saída de resumo será semelhante à do snippet mostrado abaixo.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
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
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando lhe for pedido para confirmar a opção **Subscription** (Subscrição), selecione a `Subscription` (Subscrição) adequada ao introduzir o número impresso no início da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção padrão `<create>` pressionando a entrada ou selecione uma aplicação existente.
1. Quando solicitado com opção **OS,** selecione **Windows** inserindo `3` .
1. Quando solicitado com a opção **Priceing Tier,** selecione **B2** entrando `2` .
1. Utilize a versão Java padrão, **Java 8,** premindo a entrada.
1. Utilize o recipiente web predefinido, **Tomcat 8.5,** premindo a entrada.
1. Por último, prima Enter no último pedido para confirmar as suas seleções.

    A sua saída de resumo será semelhante à do snippet mostrado abaixo.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
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
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando lhe for pedido para confirmar a opção **Subscription** (Subscrição), selecione a `Subscription` (Subscrição) adequada ao introduzir o número impresso no início da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção padrão `<create>` pressionando a entrada ou selecione uma aplicação existente.
1. Quando solicitado com opção **SO,** selecione **Linux** pressionando a entrada.
1. Quando solicitado com a opção **Priceing Tier,** selecione **B2** entrando `2` .
1. Utilize a versão Java padrão, **Java 8,** premindo a entrada.
1. Por último, prima Enter no último pedido para confirmar as suas seleções.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
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
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando lhe for pedido para confirmar a opção **Subscription** (Subscrição), selecione a `Subscription` (Subscrição) adequada ao introduzir o número impresso no início da linha.
1. Quando solicitado com a opção **Web App,** aceite a opção padrão `<create>` pressionando a entrada ou selecione uma aplicação existente.
1. Quando solicitado com opção **SO,** selecione **Linux** pressionando a entrada.
1. Quando solicitado com a opção **Priceing Tier,** selecione **B2** entrando `2` .
1. Utilize a versão Java padrão, **Java 8,** premindo a entrada.
1. Utilize o recipiente web predefinido, **Tomcat 8.5,** premindo a entrada.
1. Por último, prima Enter no último pedido para confirmar as suas seleções.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
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
    </pre>

---

::: zone-end

Pode modificar as configurações do Serviço de Aplicações diretamente no `pom.xml` seu, se necessário. Algumas comuns estão listadas abaixo:

Propriedade | Necessário | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1` `v2` . . | 1.5.2
`<subscriptionId>` | false | Especificar o ID de assinatura. | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="Um recipiente lógico para recursos Azure relacionados que você pode gerir como uma unidade.">grupo de recursos</abbr> para a sua Web App. | 0.1.0+
`<appName>` | true | O nome da sua Aplicação Web. | 0.1.0+
`<region>` | true | Especifica a região onde a sua Web App será hospedada; o valor predefinido é **westeurope**. Todas as regiões válidas na secção [Regiões Apoiadas.](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) | 0.1.0+
`<pricingTier>` | false | O nível de preços da sua Web App. O valor predefinido é **P1V2** para carga de trabalho de produção, enquanto **B2** é o mínimo recomendado para Java dev/teste. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | A configuração do ambiente de tempo de execução, pode ver o detalhe [aqui.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) | 0.1.0+
`<deployment>` | true | A configuração de implementação, pode ver os detalhes [aqui.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) | 0.1.0+

Tenha cuidado com os valores de `<appName>` e `<resourceGroup>` ( e `helloworld-1590394316693` `helloworld-1590394316693-rg` consequentemente na saída de exemplo), serão usados mais tarde.

[Reportar um problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. Implementar a aplicação

Implemente a sua aplicação Java para Azure utilizando o seguinte comando.

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, a sua aplicação estará pronta em `http://<appName>.azurewebsites.net/` . Abra o url com o seu navegador web local. Deve ver o seguinte visor:

![App de amostra em execução no Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Implementou a sua primeira aplicação Java para o Serviço de Aplicações.

[Reportar um problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Limpar recursos

Elimine a aplicação Java e os seus recursos relacionados para evitar incorrer em custos adicionais.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Este comando pode demorar alguns minutos a ser executado.

<br>
<hr/>

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
