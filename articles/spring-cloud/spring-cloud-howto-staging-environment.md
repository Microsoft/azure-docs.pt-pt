---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 5612a514ed89f73453f3751b34263b0beeea1c59
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138147"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurar um ambiente de preparo no Azure Spring Cloud

Este artigo discute como configurar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova. Este artigo mostra-lhe como colocar essa implantação em produção sem alterar diretamente a implantação da produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já implementou a aplicação PiggyMetrics do nosso tutorial sobre o lançamento de [uma aplicação Azure Spring Cloud.](spring-cloud-quickstart-launch-app-portal.md) O PiggyMetrics inclui três aplicativos: "gateway", "Account-Service," e "auth-Service".  

Se você quiser usar um aplicativo diferente para este exemplo, precisará fazer uma alteração simples em uma parte voltada ao público do aplicativo.  Essa alteração diferencia a implantação de preparo da produção.

>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Ele tem ferramentas do Azure já instaladas, incluindo as versões mais recentes do git, do JDK, do Maven e do CLI do Azure. Se tiver assinado a sua assinatura Azure, inicie a sua [Concha Azure Cloud](https://shell.azure.com).  Para saber mais, consulte [a visão geral da Casca de Nuvem Azure.](../cloud-shell/overview.md)

Para configurar um ambiente de preparo no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão Azure CLI

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Exibir todas as implantações

Vá à sua instância de serviço no portal Azure e selecione **a gestão de implantação** para ver todas as implementações. Para exibir mais detalhes, você pode selecionar cada implantação.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação no aplicativo de gateway PiggyMetrics. Por exemplo, altere a cor no *ficheiro gateway/src/main/resources/static/css/launch.css.* Isso permite que você diferencie facilmente as duas implantações. Para compilar o pacote jar, execute o seguinte comando: 

    ```azurecli
    mvn clean package
    ```

1. No CLI do Azure, crie uma nova implantação e dê a ela o nome de implantação de preparo "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois de a implementação terminar com sucesso, aceda à página de gateway do Painel de **Aplicações**, e veja todas as suas instâncias no separador **App Instances** à esquerda.
  
> [!NOTE]
> O estado de descoberta é *OUT_OF_SERVICE* para que o tráfego não seja encaminhado para esta implantação antes que a verificação esteja completa.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

1. Volte à página de gestão de **Implementação** e selecione a sua nova implementação. O estado de implantação deve mostrar *Running*. O botão de **domínio Atribuir/Não Atribuir** deve parecer cinzento, porque o ambiente é um ambiente de preparação.

1. No painel **de visão geral,** deve ver um **ponto final**de teste . Copie e cole-o em uma nova janela do navegador e a nova página PiggyMetrics deverá ser exibida.

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com uma barra (/) para garantir que o arquivo CSS seja carregado corretamente.  
> * Se o seu navegador necessitar de introduzir credenciais de login para visualizar a página, utilize [o URL descodificar](https://www.urldecoder.org/) para descodificar o ponto final do teste. O url descodificar devolve um URL no formulário "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green".  Use este formulário para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam tanto ao ambiente de preparo quanto à produção. Por exemplo, se definir o caminho de contexto (`server.servlet.context-path`) para o seu gateway de aplicação no servidor config como *um caminho,* o caminho para a sua implementação verde muda para "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...".
 
 Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Volte à **gestão de implantação**e selecione a caixa de verificação de aplicações **de gateway.**

2. Selecione **a implementação de conjuntos**.
3. Na lista de implantação de **produção,** selecione **Green**, e, em seguida, selecione **Aplicar**.
4. Vá à sua página **de visão geral** da aplicação gateway. Se já atribuiu um domínio para a sua aplicação gateway, o URL aparecerá no painel **de visão geral.** Para exibir a página PiggyMetrics modificada, selecione a URL e vá para o site.

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior torna-se a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote jar e carregá-lo em sua implantação verde usando o CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Excluir a implantação de preparo

Para eliminar a sua implantação de encenação a partir da porta Azure, vá à sua página de implementação de encenação e, em seguida, selecione o botão **Eliminar.**

Como alternativa, exclua a implantação de preparo do CLI do Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
