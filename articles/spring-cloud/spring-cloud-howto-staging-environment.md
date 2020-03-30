---
title: Criar um ambiente de encenação na Nuvem de primavera de Azure [ Microsoft Docs
description: Saiba como usar a implantação azul-verde com a Nuvem de primavera Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471035"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Criar um ambiente de preparação em Azure Spring Cloud

Este artigo discute como configurar uma implantação de encenação usando o padrão de implantação azul-verde em Azure Spring Cloud. A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova. Este artigo mostra-lhe como colocar essa implantação em produção sem alterar diretamente a implantação da produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já implementou a aplicação PiggyMetrics do nosso tutorial sobre o lançamento de [uma aplicação Azure Spring Cloud.](spring-cloud-quickstart-launch-app-portal.md) A PiggyMetrics compreende três aplicações: "gateway", "serviço de conta" e "serviço auth".  

Se quiser utilizar uma aplicação diferente para este exemplo, tem de fazer uma simples alteração numa parte virada para o público da aplicação.  Esta mudança diferencia a sua implantação de encenação da produção.

>[!TIP]
> A Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções deste artigo.  Possui ferramentas Azure comuns e pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver assinado a sua assinatura Azure, inicie a sua [Concha Azure Cloud](https://shell.azure.com).  Para saber mais, consulte [a visão geral da Casca de Nuvem Azure.](../cloud-shell/overview.md)

Para configurar um ambiente de preparação em Azure Spring Cloud, siga as instruções nas secções seguintes.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Ver todas as implementações

Vá à sua instância de serviço no portal Azure e selecione **a gestão de implantação** para ver todas as implementações. Para ver mais detalhes, pode selecionar cada implementação.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de encenação

1. No seu ambiente de desenvolvimento local, faça uma pequena modificação na aplicação de gateway PiggyMetrics. Por exemplo, altere a cor no *ficheiro gateway/src/main/resources/static/css/launch.css.* Fazê-lo permite-lhe facilmente diferenciar as duas implementações. Para construir o pacote do frasco, execute o seguinte comando: 

    ```console
    mvn clean package
    ```

1. No Azure CLI, crie uma nova implantação e dê-lhe o nome de implantação de encenação "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois de a implementação terminar com sucesso, aceda à página de gateway do Painel de **Aplicações**, e veja todas as suas instâncias no separador **App Instances** à esquerda.
  
> [!NOTE]
> O estado de descoberta é *OUT_OF_SERVICE* para que o tráfego não seja encaminhado para esta implantação antes que a verificação esteja completa.

## <a name="verify-the-staging-deployment"></a>Verifique a implantação da encenação

1. Volte à página de gestão de **Implementação** e selecione a sua nova implementação. O estado de implantação deve mostrar *Running*. O botão de **domínio Atribuir/Não Atribuir** deve parecer cinzento, porque o ambiente é um ambiente de preparação.

1. No painel **de visão geral,** deve ver um **ponto final**de teste . Copie e cole numa nova janela do navegador, e a nova página PiggyMetrics deve ser exibida.

>[!TIP]
> * Confirme que o seu ponto final de teste termina com uma barra (/) para garantir que o ficheiro CSS está carregado corretamente.  
> * Se o seu navegador necessitar de introduzir credenciais de login para visualizar a página, utilize [o URL descodificar](https://www.urldecoder.org/) para descodificar o ponto final do teste. O url descodificaum URL\<devolve um\<URL no\<formulário "https:// nome de utilizador>: palavra-passe>@ nome de cluster>.test.azureapps.io/gateway/green".  Utilize este formulário para aceder ao seu ponto final.

>[!NOTE]    
> As definições do servidor Config aplicam-se tanto ao ambiente de preparação como à produção. Por exemplo, se definir o`server.servlet.context-path`caminho de contexto ( ) para o seu gateway de aplicação no servidor\<config\<como *um caminho,* o caminho para a sua implementação verde muda para "https://\<nome de utilizador>: palavra-passe>@ nome de cluster>.test.azureapps.io/gateway/green/somepath/...".
 
 Se visitar o seu portal de aplicações virado para o público neste momento, deverá ver a página antiga sem a sua nova mudança.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar a sua mudança no seu ambiente de encenação, pode empurrá-la para a produção. Volte à **gestão de implantação**e selecione a caixa de verificação de aplicações **de gateway.**

2. Selecione **a implementação de conjuntos**.
3. Na lista de implantação de **produção,** selecione **Green**, e, em seguida, selecione **Aplicar**.
4. Vá à sua página **de visão geral** da aplicação gateway. Se já atribuiu um domínio para a sua aplicação gateway, o URL aparecerá no painel **de visão geral.** Para ver a página PiggyMetrics modificada, selecione o URL e vá ao site.

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implementação anterior torna-se a implantação da encenação.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação da encenação

Se não estiver satisfeito com a sua mudança, pode modificar o seu código de aplicação, construir um novo pacote de frascos e carregá-lo para a sua implementação verde utilizando o Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminar a implantação da encenação

Para eliminar a sua implantação de encenação a partir da porta Azure, vá à sua página de implementação de encenação e, em seguida, selecione o botão **Eliminar.**

Em alternativa, elimine a sua implantação de encenação do Azure CLI executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
