---
title: Crie um ambiente de preparação em Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implementação azul-verde com a nuvem de primavera Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878284"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Crie um ambiente de encenação em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo explica como configurar uma implementação de encenação usando o padrão de implementação azul-verde em Azure Spring Cloud. A implementação azul-verde é um padrão de entrega contínua Azure DevOps que se baseia em manter uma versão existente (azul) ao vivo enquanto uma nova (verde) é implementada. Este artigo mostra-lhe como colocar essa encenação em produção sem alterar a implantação da produção.

## <a name="prerequisites"></a>Pré-requisitos

* Exemplo de nuvem de primavera azure em um nível de preços standard
* [Extensão da Nuvem de primavera Azure](/cli/azure/azure-cli-extensions-overview) para o CLI Azure

Este artigo utiliza uma aplicação construída a partir de Spring Initializr. Se quiser usar uma aplicação diferente para este exemplo, terá de fazer uma simples alteração numa parte virada para o público da aplicação para diferenciar a sua produção.

>[!TIP]
> [AZure Cloud Shell](https://shell.azure.com) é uma concha interativa gratuita que pode usar para executar as instruções neste artigo.  Tem ferramentas Azure comuns e pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver assinado a sua subscrição Azure, inicie a sua instância Cloud Shell. Para saber mais, consulte [a visão geral da Azure Cloud Shell.](../cloud-shell/overview.md)

Para configurar a colocação azul-esverdeado em Azure Spring Cloud, siga as instruções nas secções seguintes.

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Preparar a aplicação e implementações
Para construir a aplicação, siga estes passos:

1. Gere o código para a aplicação da amostra utilizando o Initializr primavera com [esta configuração](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Descarregue o código.
3. Adicione o seguinte ficheiro de origem .java HelloController à `\src\main\java\com\example\hellospring\` pasta:

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

Ver aplicações implementadas utilizando o seguinte procedimento:

1. Vá ao seu exemplo de Azure Spring Cloud no portal Azure.

1. A partir do painel esquerdo, abra o painel **apps** para ver aplicativos para a sua instância de serviço.

   ![Screenshot do painel de apps aberto.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Pode selecionar uma aplicação e ver detalhes.

   ![Screenshot de detalhes para uma aplicação.](media/spring-cloud-blue-green-staging/app-overview.png)

1. **Open Deployments** para ver todas as implementações da aplicação. A grelha mostra tanto as implantações de produção como de encenação.

   ![Screenshot que mostra implementações de aplicações listadas.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Selecione o URL para abrir a aplicação atualmente implantada.
    
   ![Screenshot que mostra o U R L para a aplicação implementada.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Selecione **Produção** na coluna **do Estado** para ver a aplicação predefinitiva.
    
   ![Screenshot que mostra o U R L para a aplicação padrão.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Selecione **Staging** na coluna **state** para ver a aplicação de encenação.
    
   ![Screenshot que mostra o U R L para a aplicação de encenação.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Confirme que o seu ponto final de teste termina com um corte (/) para garantir que o ficheiro CSS está carregado corretamente.  
> * Se o seu navegador exigir que introduza credenciais de login para visualizar a página, use [o descodificar o URL](https://www.urldecoder.org/) para descodificar o seu ponto final de teste. O descodificam o URL no formato *\<username> https://: \<password> @ \<cluster-name> .test.azureapps.io/gateway/green*. Utilize este formato para aceder ao seu ponto final.

>[!NOTE]    
> As definições do servidor de configuração aplicam-se tanto ao ambiente de preparação como ao ambiente de produção. Por exemplo, se definir o caminho de contexto *(server.servlet.context-path*) para o seu gateway de aplicações no servidor de configuração como *algum caminho,* o caminho para a sua implementação verde muda para *https:// : \<username> \<password> @ \<cluster-name> .test.azureapps.io/gateway/green/somepath/...*.
 
Se visitar o seu portal de aplicações virado para o público neste momento, deverá ver a página antiga sem a sua nova alteração.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar a sua mudança no seu ambiente de encenação, pode empurrá-la para a produção. Na   >  página **implementações de aplicações,** selecione a aplicação atualmente em **Produção**.

1. Selecione a elipse após **o estado** de registo da implantação verde e, em seguida, selecione set **como produção**. 

   ![Screenshot que mostra seleções para definir a construção da encenação para a produção.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Confirme que o URL da aplicação apresenta as suas alterações.

   ![Screenshot que mostra o U R L da app agora em produção.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Depois de definir a implantação verde como ambiente de produção, a implantação anterior torna-se a colocação de encenação.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação da encenação

Se não estiver satisfeito com a sua alteração, pode modificar o seu código de aplicação, construir um novo pacote de .jar e carregá-lo para a sua implementação verde utilizando o Azure CLI:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminar a implementação da encenação

Para eliminar a sua colocação do portal Azure, aceda à página para a sua colocação e selecione o botão **Eliminar.**

Em alternativa, elimine a sua colocação do CLI Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Passos seguintes

* [CI/CD para Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)