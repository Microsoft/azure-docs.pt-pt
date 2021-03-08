---
title: Crie um ambiente de preparação em Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implementação azul-verde com a nuvem de primavera Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 403cfe72a4c5b6dbaea7e4eb93c37f687970443c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451936"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Crie um ambiente de encenação em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo explica como configurar uma implementação de encenação usando o padrão de implementação azul-verde em Azure Spring Cloud. A implementação azul-verde é um padrão de entrega contínua Azure DevOps que se baseia em manter uma versão existente (azul) ao vivo, enquanto uma nova (verde) é implementada. Este artigo mostra-lhe como colocar essa encenação em produção sem alterar a implantação da produção.

## <a name="prerequisites"></a>Pré-requisitos

* Exemplo de nuvem de primavera azure no **nível de preços** *padrão* .
* Extensão da nuvem de primavera Azure CLI [Azure](/cli/azure/azure-cli-extensions-overview)

Este artigo utiliza uma aplicação construída a partir do Inicializador de Mola. Se quiser utilizar uma aplicação diferente para este exemplo, terá de fazer uma simples alteração numa parte virada para o público da aplicação para diferenciar a sua produção.

>[!TIP]
> AZure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções neste artigo.  Tem ferramentas Azure comuns e pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver assinado a sua subscrição Azure, inicie a sua [Azure Cloud Shell](https://shell.azure.com).  Para saber mais, consulte [a visão geral da Azure Cloud Shell.](../cloud-shell/overview.md)

Para configurar as colocações verde-azuladas na Nuvem de primavera de Azure, siga as instruções nas secções seguintes.

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Preparar aplicativos e implementações
Para construir a aplicação siga estes passos:
1. Gere o código para a aplicação da amostra utilizando o Inicializador primavera com [esta configuração](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Descarregue o código.
3. Adicione o seguinte ficheiro de origem HelloController.java à pasta `\src\main\java\com\example\hellospring\` .
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. Construa o ficheiro .jar:
```azurecli
mvn clean packge -DskipTests
```
5. Crie a aplicação no seu exemplo Azure Spring Cloud:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
```
6. Implementar a aplicação para Azure Spring Cloud:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. Modifique o código para a sua colocação de encenação:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Reconstruir o ficheiro .jar:
```azurecli
mvn clean packge -DskipTests
```
9. Criar a implantação verde: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Ver aplicativos e implementações

Ver aplicações implementadas utilizando os seguintes procedimentos.

1. Vá ao seu exemplo de Azure Spring Cloud no portal Azure.

1. A partir do painel de navegação à esquerda abra a lâmina "Apps" para ver aplicativos para a sua instância de serviço.

    [![Apps-dashboard](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Pode clicar numa aplicação e ver detalhes.

    [![Visão geral de apps](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. **Open Deployments** para ver todas as implementações da aplicação. A grelha mostra tanto as implantações de produção como de encenação.

    [![Painel de aplicações/implementações](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Clique no URL para abrir a aplicação atualmente implantada.
    ![URL implantado](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Clique em **Produção** na coluna **Do Estado** para ver a aplicação predefinida.
    ![Execução padrão](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Clique em **Staging** na coluna **State** para ver a aplicação de encenação.
    ![Execução de encenação](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Confirme que o seu ponto final de teste termina com um corte (/) para garantir que o ficheiro CSS está carregado corretamente.  
> * Se o seu navegador exigir que introduza credenciais de login para visualizar a página, use [o descodificar o URL](https://www.urldecoder.org/) para descodificar o seu ponto final de teste. O descodifica URL devolve um URL no formulário "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green".  Utilize este formulário para aceder ao seu ponto final.

>[!NOTE]    
> As definições do servidor Config aplicam-se tanto ao ambiente de preparação como à produção. Por exemplo, se definir o caminho de contexto () para o `server.servlet.context-path` seu gateway de aplicações no servidor config como *algum caminho,* o caminho para a sua implementação verde muda para "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green/somepath/...".
 
 Se visitar o seu portal de aplicações virado para o público neste momento, deverá ver a página antiga sem a sua nova alteração.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar a sua mudança no seu ambiente de encenação, pode empurrá-la para a produção. Na  / página **Implementações de Aplicações,** selecione a aplicação atualmente em `Production` .

1. Clique nas elipses após o estado de **registo** da implantação verde e desempate a construção da encenação para a produção. 

   [![Definir produção para encenar](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Agora o URL da aplicação deve apresentar as suas alterações.

   ![Encenação agora em implantação](media/spring-cloud-blue-green-staging/new-production-deployment.png)

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

* [CI/CD para Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)