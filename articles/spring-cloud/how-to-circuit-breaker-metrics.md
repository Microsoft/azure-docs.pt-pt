---
title: Colete as métricas do disjuntor de disjuntores de resiliência da nuvem de primavera4J
description: Como recolher as métricas de disjuntor de disjuntores de resiliência da nuvem de primavera4J.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9fc8ccc5ba21f02885b2002d5040051baac92068
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878364"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Colete as métricas do disjuntor de disjuntores de resiliência da nuvem de primavera4J (pré-visualização)

Este documento explica como recolher as Métricas de Disjuntor de Disjuntores de Resiliência da Nuvem de primavera com o agente java do Application Insights em processo.  Com esta funcionalidade é possível monitorizar métricas de disjuntor de resiliência4j a partir de Application Insights.

Usamos a [demonstração de disjuntor de molas](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) para mostrar como funciona.

## <a name="prerequisites"></a>Pré-requisitos

* Ativar o agente java In-Process do [guia Java In-Process Agent for Application Insights](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Permitir a recolha de dimensões para métricas de resiliência4j do [guia Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Instale git, Maven e Java, se não já estiver em uso pelo computador de desenvolvimento.

## <a name="build-and-deploy-apps"></a>Criar e implementar aplicações

O procedimento a seguir constrói e implementa aplicações.

1. Clone e construa o repositório de demonstração.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Criar aplicações com pontos finais

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Implementar aplicações.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Inclua a dependência necessária para a Resiliência4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * O código do cliente deve utilizar a API de `CircuitBreakerFactory` , que é implementada como um automaticamente criado quando se inclui um arranque de `bean` Disjuntor de Mola cloud. Para mais detalhes consulte [o Disjuntor de Nuvens de primavera](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * As 2 dependências seguintes têm conflitos com pacotes resilientes de 4j acima.  Certifique-se de que o cliente não os inclui.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Navegue para o URL fornecido por aplicações de gateway, e aceda ao ponto final a partir da [demonstração de disjuntor de mola-cloud-disjuntor da](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) seguinte forma:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Localizar Resilence4j Métricas do Portal

1. Selecione a Lâmina de Insights de **Aplicação** do portal Azure Spring Cloud e clique em **Insights de Aplicação**.

   [![resiliência4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Selecione **métricas** na página **'Insights de Aplicação'.**  Selecione **azure.applicationinsights** from **Metrics Namespace**.  Selecione também **resilience4j_circuitbreaker_buffered_calls** métricas com **Média.**

   [![resiliência4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Selecione **resilience4j_circuitbreaker_calls** métricas e **média**.

   [![resiliência4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Selecione **resilience4j_circuitbreaker_calls**  métricas e **média**.  Clique **em Adicionar filtro** e, em seguida, selecione o nome como **createNewAccount**.

   [![resiliência4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Selecione **resilience4j_circuitbreaker_calls**  métricas e **média**.  Em seguida, clique **em Aplicar a divisão** e selecionar **tipo**.

   [![resiliência4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Selecione **resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls**, e **métricas de resilience4j_circuitbreaker_slow_calls** com **média**.

   [![resiliência4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Ver também

* [Insights de aplicação](spring-cloud-howto-application-insights.md)
* [Rastreio distribuído](spring-cloud-howto-distributed-tracing.md)
* [Painel de disjuntor](spring-cloud-tutorial-circuit-breaker.md)