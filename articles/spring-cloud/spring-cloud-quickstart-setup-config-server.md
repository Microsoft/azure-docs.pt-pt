---
title: Quickstart - Configurar o servidor de configuração Azure Spring Cloud
description: Descreve a configuração do servidor configurar Azure Spring Cloud para implementação de aplicações.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885699"
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

* [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedimentos de configuração de config da Cloud Azure Spring

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir configura o servidor config utilizando o portal Azure para implantar a [amostra piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Aceda à página **de visão geral** do serviço e selecione **Config Server**.

2. Na secção **de repositório padrão,** desafine o **URI** para https://github.com/Azure-Samples/piggymetrics-config " .

3. Selecione **Aplicar** para guardar as alterações.

    ![Screenshot do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir utiliza o CLI Azure para configurar o servidor config para implantar a [amostra piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Config-servidor com a localização do repositório de git para o projeto:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende continuar para o próximo quickstart desta série, salte este passo.

Nestes quickstarts, criou recursos Azure que continuarão a acumular encargos se permanecerem na sua subscrição. Se não pretende continuar para o próximo quickstart, e não espera precisar destes recursos no futuro, elimine o grupo de recursos utilizando o portal ou executando o seguinte comando na Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Num início rápido anterior, também definiu o nome do grupo de recursos predefinido. Se não pretender continuar até ao próximo arranque rápido, limpe o padrão executando o seguinte comando CLI:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e implementar aplicações](spring-cloud-quickstart-deploy-apps.md)
