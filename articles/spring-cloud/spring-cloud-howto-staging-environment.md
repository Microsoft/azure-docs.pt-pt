---
title: Crie um ambiente de preparação em Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implementação azul-verde com a nuvem de primavera Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226118"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Crie um ambiente de encenação em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo discute como configurar uma implementação de encenação usando o padrão de implantação azul-verde em Azure Spring Cloud. A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova. Este artigo mostra-lhe como colocar essa encenação em produção sem alterar a implantação da produção diretamente.

## <a name="prerequisites"></a>Pré-requisitos

* Exemplo de nuvem de primavera azure com **nível de preços** *padrão* .
* Um pedido de execução.  Consulte [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md).
* [Extensão Azure](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) CLI asc

Se quiser utilizar uma aplicação diferente para este exemplo, tem de fazer uma simples alteração numa parte virada para o público da aplicação.  Esta alteração diferencia a sua colocação de produção.

>[!TIP]
> AZure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções neste artigo.  Tem ferramentas Azure comuns e pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver assinado a sua subscrição Azure, inicie a sua [Azure Cloud Shell](https://shell.azure.com).  Para saber mais, consulte [a visão geral da Azure Cloud Shell.](../cloud-shell/overview.md)

Para configurar um ambiente de preparação em Azure Spring Cloud, siga as instruções nas secções seguintes.

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Ver aplicativos e implementações

Ver aplicações implementadas utilizando os seguintes procedimentos.

1. Vá ao seu exemplo de Azure Spring Cloud no portal Azure.

1. A partir do painel de navegação à **esquerda, as implementações**.

    [![Deprecação de implantação](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Abra a lâmina "Apps" para ver aplicativos para a sua instância de serviço.

    [![Apps-dashboard](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Pode clicar numa aplicação e ver detalhes.

    [![Visão geral de apps](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Abra a lâmina **de Implementações** para ver todas as implementações da aplicação. A grelha de implantação mostra se a implantação é produção ou encenação.

    [![Painel de implementações](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Pode clicar no nome da implementação para visualizar a visão geral da implementação. Neste caso, a única implementação é denominada *Padrão*.

    [![Visão geral das implementações](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Criar uma implementação de encenação

1. No seu ambiente de desenvolvimento local, faça uma pequena modificação na sua aplicação. Ao fazê-lo, permite-lhe diferenciar facilmente as duas implementações. Para construir o pacote de frascos, executar o seguinte comando: 

    ```console
    mvn clean package -DskipTests
    ```

1. No CLI Azure, crie uma nova implantação, e dê-lhe o nome de implementação de encenação "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. Após o acabamento da implementação do CLI com sucesso, aceda à página da aplicação a partir do Painel de **Aplicações** e veja todas as suas instâncias no separador **Implementações** à esquerda.

   [![Implementa painel de instrumentos após implantação verde](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> O estado de descoberta é *OUT_OF_SERVICE* para que o tráfego não seja encaminhado para esta implementação antes que a verificação esteja completa.

## <a name="verify-the-staging-deployment"></a>Verifique a implantação da encenação

Para verificar se o desenvolvimento da encenação verde está a funcionar:
1. ir para **Implementações** e clicar na `green` **implementação de Encenação**.
1. A partir da página **'Vista Geral',** clique no ponto final do **Teste.**
1. Isto abrirá a construção da encenação mostrando as suas alterações.

>[!TIP]
> * Confirme que o seu ponto final de teste termina com um corte (/) para garantir que o ficheiro CSS está carregado corretamente.  
> * Se o seu navegador exigir que introduza credenciais de login para visualizar a página, use [o descodificar o URL](https://www.urldecoder.org/) para descodificar o seu ponto final de teste. O descodifica URL devolve um URL no formulário "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green".  Utilize este formulário para aceder ao seu ponto final.

>[!NOTE]    
> As definições do servidor Config aplicam-se tanto ao ambiente de preparação como à produção. Por exemplo, se definir o caminho de contexto () para o `server.servlet.context-path` seu gateway de aplicações no servidor config como *algum caminho,* o caminho para a sua implementação verde muda para "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green/somepath/...".
 
 Se visitar o seu portal de aplicações virado para o público neste momento, deverá ver a página antiga sem a sua nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar a sua mudança no seu ambiente de encenação, pode empurrá-la para a produção. Voltar à **gestão da Implementação**, e selecione a aplicação atualmente em `Production` .

1. Clique nas elipses após o **estado de Registo** e desempate a construção da produção para `staging` .

   [![Implementações definem a implementação da encenação](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Volte à página **de gestão de implementação.** Desa `green` estação para `production` . Quando a definição terminar, o seu `green` estado de implantação deve aparecer .  Esta é agora a construção da produção em execução.

   [![Implementações definem o resultado da implementação da fase](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. O URL da aplicação deve apresentar as suas alterações.

>[!NOTE]
> Depois de definir a implantação verde como ambiente de produção, a implantação anterior torna-se a colocação de encenação.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação da encenação

Se não estiver satisfeito com a sua alteração, pode modificar o seu código de aplicação, construir um novo pacote de frascos e carregá-lo para a sua versão verde utilizando o Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminar a implementação da encenação

Para eliminar a sua colocação a partir da porta Azure, aceda à sua página de implementação de encenação e, em seguida, selecione o botão **Eliminar.**

Em alternativa, elimine a sua colocação do CLI Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Passos seguintes

* [CI/CD para Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
