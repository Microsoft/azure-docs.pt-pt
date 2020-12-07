---
title: Quickstart - Configurar o servidor de configuração Azure Spring Cloud
description: Descreve a configuração do servidor configurar Azure Spring Cloud para implementação de aplicações.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 7400aeeba80ce168a9dea0d81e1ad0f2fbe24c95
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750848"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Quickstart: Configurar o servidor de configuração Azure Spring Cloud

O servidor Azure Spring Cloud Config é um serviço de configuração centralizado para sistemas distribuídos. Utiliza uma camada de repositório pluggável que suporta atualmente o armazenamento local, Git e Subversão. Neste arranque rápido, você configura o servidor config para obter dados de um repositório git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Pré-requisitos

* Complete o quickstart anterior nesta série: [Provision Azure Spring Cloud service](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedimentos de configuração de config da Cloud Azure Spring

Config-servidor com a localização do repositório de git para o projeto executando o seguinte comando. `<service instance name>`Substitua-o pelo nome do serviço que criou anteriormente. O valor predefinido para o nome de instância de serviço que definiu no quickstart anterior não funciona com este comando.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Este comando diz ao servidor de Configuração para encontrar os dados de configuração na [pasta steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) do repositório da aplicação da amostra. Uma vez que o nome da app que irá obter os dados de configuração é `planet-weather-provider` , o ficheiro que será usado é [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
O servidor Azure Spring Cloud Config é um serviço de configuração centralizado para sistemas distribuídos. Utiliza uma camada de repositório pluggável que suporta atualmente o armazenamento local, Git e Subversão.  Crie o servidor config para implementar aplicações de microserviços para a Azure Spring Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedimentos de configuração de config da Cloud Azure Spring

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir configura o servidor config utilizando o portal Azure para implantar a [amostra piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Aceda à página **de visão geral** do serviço e selecione **Config Server**.

2. Na secção **de repositório padrão,** desafine o **URI** para https://github.com/Azure-Samples/piggymetrics-config " .

3. Clique **em Validar**.

    ![Navegue para o servidor config](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Quando a validação estiver concluída, clique em **Aplicar** para guardar as suas alterações.

    ![Validação do servidor config](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. A atualização da configuração pode demorar alguns minutos.
 
    ![Atualizar o servidor config](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. Deverá receber uma notificação quando a configuração estiver completa.

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir utiliza o CLI Azure para configurar o servidor config para implantar a [amostra piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Config-servidor com a localização do repositório de git para o projeto:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Se estiver a utilizar um repositório privado para o servidor config, consulte o nosso tutorial sobre a [configuração da autenticação](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server).

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Resolução de problemas do servidor de config da Cloud da primavera de Azure

O procedimento a seguir explica como resolver problemas as definições do servidor config.

1. No portal Azure, aceda à página **geral** do serviço e selecione **Registos**. 
1. Selecione **Consultas** e **Mostre os registos de aplicações que contenham os termos de "erro" ou "exceção".** 
1. Clique em **Run** (Executar). 
1. Se encontrar o erro **java.lang.ilegalStateException** em registos, isto indica que o serviço de nuvem de mola não consegue localizar propriedades a partir do servidor config.

    [![Consulta de execução do portal ASC ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Aceda à página **geral** do serviço.
1. Selecione **Diagnosticar e resolver problemas**. 
1. Selecione o detetor **Config Server.**

    [![Problemas de diagnóstico do portal ASC ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Clique **em Config Server Health Check**.

    [![Génio do portal ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Clique **no Estado do Servidor Config** para ver mais detalhes do detetor.

    [Estado de ![ saúde ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) do portal ASC ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou recursos Azure que continuarão a acumular encargos se permanecerem na sua subscrição. Se não pretende continuar para o próximo quickstart, consulte [clean up resources](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Caso contrário, avance para o próximo arranque rápido:

> [!div class="nextstepaction"]
> [Criar e implementar aplicações](spring-cloud-quickstart-deploy-apps.md)
