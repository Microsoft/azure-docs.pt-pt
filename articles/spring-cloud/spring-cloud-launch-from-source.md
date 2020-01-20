---
title: Início rápido – iniciar seu aplicativo Spring Cloud a partir do código-fonte
description: Neste guia de início rápido, saiba como iniciar seu aplicativo de nuvem Spring do Azure diretamente do seu código-fonte
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 8edadbf946a28239ae4d28d56b3dccd77b451a71
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277163"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Início rápido: iniciar seu aplicativo Spring Cloud a partir do código-fonte

O Azure Spring Cloud permite que você execute facilmente aplicativos de microserviço baseados na Spring Cloud no Azure.

O Azure Spring Cloud permite que você inicie seu aplicativo diretamente do código-fonte Java ou de um JAR predefinido. Este artigo orienta você pelas etapas necessárias.

Seguindo este guia de início rápido, você aprenderá a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Atualmente, o Azure Spring Cloud é oferecido como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações sobre o suporte durante as visualizações, leia nossas [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre um [solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

Antes de começar, verifique se sua assinatura do Azure tem as dependências necessárias:

1. [Instale o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

> [!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure com o seguinte comando

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Provisionar uma instância de serviço usando o CLI do Azure

Faça logon no CLI do Azure e escolha sua assinatura ativa. Certifique-se de escolher a assinatura ativa que está na lista de permissões para o Azure Spring Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Abra uma janela de CLI do Azure e execute os comandos a seguir para provisionar uma instância do Azure Spring Cloud. Observe que também dizemos ao Azure Spring Cloud para atribuir um domínio público aqui.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

A instância do serviço levará cerca de cinco minutos para ser implantada.

Defina o nome do grupo de recursos padrão e o nome do cluster usando os seguintes comandos:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>Criar o aplicativo Spring Cloud

O comando a seguir cria um aplicativo Spring Cloud em sua assinatura.  Isso cria um serviço de nuvem Spring vazio no qual podemos carregar nosso aplicativo.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Implantar seu aplicativo Spring Cloud

Você pode implantar seu aplicativo de um JAR predefinido ou de um repositório gradle ou Maven.  Encontre instruções para cada caso abaixo.

### <a name="deploy-a-built-jar"></a>Implantar um JAR criado

Para implantar a partir de um JAR criado em seu computador local, verifique se sua compilação produz um [Fat-jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Para implantar o Fat-JAR em uma implantação ativa

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Para implantar o Fat-JAR em uma implantação específica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Implantar do código-fonte

O Azure Spring Cloud usa o [kpack](https://github.com/pivotal/kpack) para criar seu projeto.  Você pode usar CLI do Azure para carregar seu código-fonte, criar seu projeto usando kpack e implantá-lo no aplicativo de destino.

> [!WARNING]
> O projeto deve produzir apenas um arquivo JAR com uma entrada `main-class` na `MANIFEST.MF` no `target` (para implantações do Maven ou `build/libs` (para implantações gradle).  Vários arquivos JAR com entradas de `main-class` farão com que a implantação falhe.

Para projetos do único módulo Maven/gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Para projetos Maven/gradle com vários módulos, repita para cada módulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Mostrar logs de implantação

Examine os logs de compilação do kpack usando o seguinte comando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Os logs do kpack só mostrarão a implantação mais recente se essa implantação tiver sido criada a partir da origem usando kpack.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

1. Abra a página **painel do aplicativo** .
2. Selecione o aplicativo `gateway` para mostrar a página de **detalhes do aplicativo** .
3. Selecione **atribuir domínio** para atribuir um ponto de extremidade público ao gateway. Isso pode demorar alguns minutos. 
4. Insira o IP público atribuído em seu navegador para exibir o aplicativo em execução.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Editar variáveis de ambiente para aplicativos
> * Atribuir IP público para o gateway de aplicativo

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)

Mais exemplos estão disponíveis no GitHub: [exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
