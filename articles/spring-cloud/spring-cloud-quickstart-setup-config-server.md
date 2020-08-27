---
title: Quickstart - Configurar o servidor de configuração Azure Spring Cloud
description: Descreve a configuração do servidor configurar Azure Spring Cloud para implementação de aplicações.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951913"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Quickstart: Configurar o servidor de configuração Azure Spring Cloud

O servidor Azure Spring Cloud Config é um serviço de configuração centralizado para sistemas distribuídos. Utiliza uma camada de repositório pluggável que suporta atualmente o armazenamento local, Git e Subversão.  Crie o servidor config para implementar aplicações de microserviços para a Azure Spring Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
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

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construir e implementar aplicativos](spring-cloud-quickstart-deploy-apps.md)
