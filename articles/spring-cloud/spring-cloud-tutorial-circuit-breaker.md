---
title: 'Tutorial: Use painel de disjuntor com nuvem de mola azure'
description: Aprenda a utilizar o painel de disjuntores com a Nuvem de Mola Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.openlocfilehash: e5fbb14477275ca329243797b75550cfe92077b6
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701550"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Use painel de disjuntor com nuvem de mola azure
Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) é amplamente usada para agregar vários fluxos de métricas [Hystrix](https://github.com/Netflix/Hystrix) para que os streams possam ser monitorizados numa única vista usando o dashboard Hystrix. Este tutorial demonstra como usá-los na Nuvem de primavera Azure.
> [!NOTE]
> O Netflix Hystrix é amplamente utilizado em muitas aplicações existentes da Spring Cloud, mas já não está em desenvolvimento ativo. Se estiver a desenvolver um novo projeto, utilize implementações de Spring Cloud Circuit Breaker como [resilience4j](https://github.com/resilience4j/resilience4j). Diferente da Turbina mostrada neste tutorial, o novo quadro de Disjuntor de Nuvem de primavera unifica todas as implementações do seu pipeline de dados de métricas em Micrometro. Ainda estamos a trabalhar no suporte do micrometro em Azure Spring Cloud, pelo que não será coberto por este tutorial.

## <a name="prepare-your-sample-applications"></a>Prepare as suas aplicações de amostra
A amostra é bifurcada deste [repositório.](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)

Clone o repositório da amostra para o seu ambiente de desenvolvimento:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Construa as 3 aplicações que serão usadas neste tutorial:
* serviço de utilizador: Um simples serviço REST que tem um único ponto final de /personalizado/{id}
* serviço de recomendação: Um simples serviço REST que tem um único ponto final de/recomendações, que será chamado pelo serviço de utilizador.
* hystrix-turbine: Um serviço de tablier Hystrix para exibir fluxos Hystrix e um serviço de turbina agregando o fluxo de métricas Hystrix de outros serviços.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Fornecer a sua instância Azure Spring Cloud
Siga o procedimento, provisão de uma instância de [serviço no Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Desloque as suas aplicações para azure Spring Cloud
Estas aplicações não utilizam **o Config Server**, pelo que não há necessidade de configurar **o Config Server** para a Nuvem de primavera Azure.  Criar e implementar da seguinte forma:
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Verifique as suas aplicações
Depois de todas as aplicações estarem a correr e detetáveis, acede `user-service` ao caminho https:// <username> -user-service.azuremicroservices.io/personalized/1 do seu navegador. Se o serviço de utilizador puder `recommendation-service` aceder, deverá obter a seguinte saída. Refresque a página web algumas vezes se não funcionar.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Aceda ao seu dashboard Hystrix e fluxo de métricas
Verifique utilizando pontos finais públicos ou pontos finais de teste privados.

### <a name="using-public-endpoints"></a>Usando pontos finais públicos
Aceda à hystrix-turbine com o caminho `https://<SERVICE-NAME>-hystrix-turbine azuremicroservices.io/hystrix` do seu navegador.  A figura que se segue mostra o dashboard Hystrix a funcionar nesta aplicação.

![Painel histrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Copie o url de fluxo turbine `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` para a caixa de texto e clique no Monitor **Stream**.  Isto irá exibir o painel de instrumentos. Se nada aparecer no espectador, acerte os `user-service` pontos finais para gerar fluxos.

![Fluxo hystrix Agora pode experimentar com o Painel de ](media/spring-cloud-circuit-breaker/hystrix-stream.png) Disjuntores.
> [!NOTE] 
> Na produção, o painel de instrumentos hystrix e o fluxo de métricas não devem ser expostos à Internet.

### <a name="using-private-test-endpoints"></a>Utilização de pontos finais de teste privados
Os fluxos de métricas histrix também são acessíveis a partir de `test-endpoint` . Como um serviço de backend, não atribuímos um ponto final público `recommendation-service` para, mas podemos mostrar as suas métricas com ponto final de teste em`https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Fluxo de final de teste de Hystrix](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Como uma aplicação web, o dashboard Hystrix deve estar a funcionar `test-endpoint` . Se não estiver a funcionar corretamente, pode haver duas razões: primeiro, usar `test-endpoint` o URL base de , `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` ou, em segundo lugar, a aplicação web está a usar o caminho absoluto para o recurso estático. Para que `test-endpoint` funcione, poderá ser necessário editar manualmente <base> os ficheiros frontais.

## <a name="next-steps"></a>Próximos passos
* [Prever uma instância de serviço no Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)
* [Prepare uma aplicação java spring para implantação em Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
