---
title: Iniciar um aplicativo de nuvem Spring do Azure usando o portal do Azure
description: Implante um aplicativo de exemplo na nuvem Spring do Azure usando o portal do Azure.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 2203f4ba5708728ea8300093bb980a2226e2ffa8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170402"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Início rápido: iniciar um aplicativo de nuvem Spring do Azure usando o portal do Azure

O Azure Spring Cloud permite que você execute facilmente aplicativos de microserviço baseados na Spring Cloud no Azure.

Este guia de início rápido mostra como implantar um aplicativo Spring Cloud existente no Azure. [Aqui está um link](https://github.com/xscript/PiggyMetrics) para o código do aplicativo de exemplo usado neste tutorial. Quando tiver terminado, o aplicativo de exemplo fornecido estará acessível online e pronto para ser gerenciado por meio do portal do Azure.

Seguindo este guia de início rápido, você aprenderá a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Antes de iniciar este guia de início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Como um serviço de visualização, solicitamos que os clientes entrem em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se você quiser explorar os recursos do Azure Spring Cloud, [preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o comando a seguir

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com)e entre em sua conta.

1. Pesquise a **nuvem do Azure Spring** e selecione-a para ir para a página Visão geral. Selecione o botão **criar** para começar.

1. Preencha o formulário, considerando as seguintes diretrizes:
    - Nome do serviço: especifique o nome da sua instância de serviço.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - Assinatura: selecione a assinatura que você deseja que seja cobrada para este recurso.  Verifique se essa assinatura foi adicionada à nossa lista de permissões para o Azure Spring Cloud.
    - Grupo de recursos: a criação de novos grupos de recursos para novos recursos é uma prática recomendada.
    - Local: selecione o local para sua instância de serviço. Os locais com suporte no momento incluem leste dos EUA, oeste dos EUA 2, Europa Ocidental e sudeste asiático.
    
Leva cerca de 5 minutos para que o serviço seja implantado.  Após a implantação, a página **visão geral** da instância do serviço será exibida.

## <a name="set-up-your-configuration-server"></a>Configurar o servidor de configuração

1. Vá para a página **visão geral** do serviço e selecione **servidor de configuração**.

1. Na seção **repositório padrão** , defina **URI** como "https://github.com/Azure-Samples/piggymetrics", defina **rótulo** como "configuração" e selecione **aplicar** para salvar as alterações.

## <a name="build-and-deploy-microservice-applications"></a>Compilar e implantar aplicativos de microatendimento

1. Abra uma janela de comando e execute o comando a seguir para clonar o repositório de aplicativo de exemplo em seu computador local.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Compile o projeto executando o comando abaixo.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Faça logon para CLI do Azure e defina sua assinatura ativa.

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Atribua nomes ao seu grupo de recursos e ao seu serviço. Certifique-se de substituir os espaços reservados abaixo pelo nome do grupo de recursos e pelo nome do serviço que você provisionou anteriormente neste tutorial.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults asc=<service instance name>
    ```

1. Crie o aplicativo `gateway` e implante o arquivo JAR.

    ```azurecli
    az asc app create -n gateway
    az asc app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Seguindo o mesmo padrão, crie os aplicativos `account-service` e `auth-service` e implante seus arquivos JAR.

    ```cli
    az asc app create -n account-service
    az asc app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az asc app create -n auth-service
    az asc app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Leva alguns minutos para concluir a implantação dos aplicativos. Para confirmar que eles foram implantados, acesse o **painel do aplicativo** na portal do Azure. Você deve ver uma linha de cada um dos três aplicativos.

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

1. Abra a página **painel do aplicativo** .
2. Selecione o aplicativo `gateway` para mostrar a página de **detalhes do aplicativo** .
3. Selecione **atribuir domínio** para atribuir um ponto de extremidade público ao gateway. Isso pode demorar alguns minutos. 
4. Insira o IP público atribuído em seu navegador para exibir o aplicativo em execução.


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