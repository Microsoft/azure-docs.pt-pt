---
title: 'Tutorial: Use o painel de disjuntores com a nuvem de mola Azure'
description: Aprenda a utilizar o painel de disjuntores com a nuvem de mola Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java
ms.openlocfilehash: fa66f17c6f96ac7f70188c5a28c0b180ed2f03e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906883"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Use o painel de disjuntores com a nuvem de mola Azure

**Este artigo aplica-se a:** ✔️ Java

::: zone pivot="programming-language-java"
A [Turbina Netflix](https://github.com/Netflix/Turbine) da Cloud Spring é amplamente utilizada para agregar várias métricas [Hystrix](https://github.com/Netflix/Hystrix) para que os streams possam ser monitorizados numa única vista usando o painel Hystrix. Este tutorial demonstra como usá-los na Nuvem de primavera de Azure.
> [!NOTE]
> O Netflix Hystrix é amplamente utilizado em muitas aplicações da Spring Cloud existentes, mas já não está em desenvolvimento ativo. Se estiver a desenvolver um novo projeto, utilize implementações de Disjuntores de Nuvens de primavera como [a resiliência4j](https://github.com/resilience4j/resilience4j). Diferente da Turbina mostrada neste tutorial, a nova estrutura de disjuntor de molas de nuvem unifica todas as implementações do seu pipeline de dados métricas em Micrometro. Ainda estamos a trabalhar no apoio ao micrometro na Nuvem de primavera de Azure, pelo que não será coberto por este tutorial.

## <a name="prepare-your-sample-applications"></a>Prepare as suas aplicações de amostra
A amostra é forfurada deste [repositório.](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)

Clone o repositório de amostras para o seu ambiente de desenvolvimento:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Construa as 3 aplicações que serão utilizadas neste tutorial:
* serviço de utilizador: Um serviço REST simples que tem um único ponto final de /personalizado/{id}
* serviço de recomendação: Um simples serviço REST que tem um único ponto final de /recomendações, que será chamado pelo serviço de utilizador.
* hystrix-turbina: Um serviço de dashboard Hystrix para exibir streams Hystrix e um serviço de turbina agregando métricas Hystrix fluírem de outros serviços.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Provisionar o seu exemplo de Nuvem de primavera Azure
Siga o procedimento, [Provisão uma instância de serviço no Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Implemente as suas aplicações para Azure Spring Cloud
Estas aplicações não utilizam **o Config Server**, pelo que não é necessário configurar o **Config Server** para a Azure Spring Cloud.  Criar e implementar da seguinte forma:
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Verifique as suas apps
Depois de todas as aplicações estarem a ser executando e detetáveis, o acesso `user-service` com o caminho https:// <username> -user-service.azuremicroservices.io/personalized/1 do seu navegador. Se o serviço de utilizador puder `recommendation-service` aceder, deverá obter a seguinte saída. Refresque a página web algumas vezes se não funcionar.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Aceda ao seu painel hystrix e fluxo de métricas
Verifique a utilização de pontos finais públicos ou pontos finais de teste privados.

### <a name="using-public-endpoints"></a>Usando pontos finais públicos
Aceda a hstrix-turbina com o caminho `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` do seu navegador.  A seguinte figura mostra o painel Hystrix em execução nesta aplicação.

![Painel Hystrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Copie o url de fluxo de turbina `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` na caixa de texto e clique em Monitor **Stream**.  Isto mostrará o painel de instrumentos. Se nada aparecer no espectador, acerte nos `user-service` pontos finais para gerar fluxos.

![Fluxo Hystrix ](media/spring-cloud-circuit-breaker/hystrix-stream.png) Agora pode experimentar com o painel de disjuntores.
> [!NOTE] 
> Na produção, o painel hystrix e o fluxo de métricas não devem ser expostos à Internet.

### <a name="using-private-test-endpoints"></a>Utilizando pontos finais de teste privados
As métricas hystrix também são acessíveis a partir de `test-endpoint` . Como serviço de backend, não atribuímos um ponto final público `recommendation-service` para, mas podemos mostrar as suas métricas com o ponto final de teste em `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Fluxo de ponta de teste Hystrix](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Como uma aplicação web, o painel Hystrix deve estar a trabalhar `test-endpoint` em . Se não estiver a funcionar corretamente, pode haver duas razões: primeiro, utilizar `test-endpoint` o URL base de , `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` ou, em segundo lugar, a aplicação web está a usar o caminho absoluto para o recurso estático. Para que `test-endpoint` funcione, poderá ter de editar manualmente os <base> ficheiros frontais.

## <a name="next-steps"></a>Passos seguintes
* [Prestação de uma instância de serviço no CLI Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)
* [Prepare uma aplicação java spring para implantação em Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
::: zone-end
