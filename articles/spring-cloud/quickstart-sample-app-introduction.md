---
title: Quickstart - Introdução à aplicação da amostra - Azure Spring Cloud
description: Descreve a aplicação de amostras utilizada nesta série de quickstarts para implantação para Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879330"
---
# <a name="introduction-to-the-sample-app"></a>Introdução à aplicação de exemplo

::: zone pivot="programming-language-csharp"
Esta série de quickstarts usa uma aplicação de amostra composta por dois microserviços para mostrar como implementar uma aplicação .NET Core Steeltoe para o serviço Azure Spring Cloud. Você usará as capacidades da Azure Spring Cloud, tais como a descoberta de serviços, o servidor config, os registos, as métricas e o rastreio distribuído.

## <a name="functional-services"></a>Serviços funcionais

A aplicação da amostra é composta por dois microserviços:

* O `planet-weather-provider` serviço devolve o texto meteorológico em resposta a um pedido HTTP que especifica o nome do planeta. Por exemplo, pode voltar "muito quente" para o planeta Mercúrio. Obtém os dados meteorológicos do servidor Config. O servidor Config obtém os dados meteorológicos de um ficheiro YAML num repositório de Git, por exemplo:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* O `solar-system-weather` serviço devolve dados para quatro planetas em resposta a um pedido HTTP. Obtém os dados fazendo quatro pedidos HTTP para `planet-weather-provider` . Utiliza o serviço de descoberta do servidor Eureka para `planet-weather-provider` ligar. Devolve JSON, por exemplo:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

O diagrama a seguir ilustra a arquitetura da aplicação da amostra:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagrama de aplicação de amostra":::

## <a name="code-repository"></a>Repositório de código

A aplicação de amostra está localizada na pasta da [amostra de aço](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) do repositório Azure-Samples/Azure-Spring-Cloud-Samples no GitHub.

As instruções nos seguintes quickstarts referem-se ao código de origem, se necessário.

::: zone-end

::: zone pivot="programming-language-java"
Neste quickstart, usamos uma amostra de finanças pessoais chamada PiggyMetrics para mostrar como implementar uma aplicação para o serviço Azure Spring Cloud. A PiggyMetrics demonstra o padrão de arquitetura de microserviços e destaca a quebra de serviços. Você verá como é implantado para Azure com poderosas capacidades Azure Spring Cloud, incluindo descoberta de serviço, servidor config, registos, métricas e rastreio distribuído.

Para seguir os exemplos de implementação da Cloud Azure Spring, só precisa da localização do código fonte, que é fornecido conforme necessário.

## <a name="functional-services"></a>Serviços funcionais

A PiggyMetrics está decomposta em três microserviços principais. Todas elas são aplicações desdobráveis independentemente organizadas por domínios empresariais.

* **Serviço de conta (a ser implementado)**: Contém lógica de entrada geral do utilizador e validação: rendimentos/despesas itens, poupanças e definições de conta.
* **Serviço de estatísticas (Não utilizado neste arranque rápido)**: Executa cálculos em parâmetros estatísticos importantes e captura séries de tempo para cada conta. O datapoint contém valores, normalizados para a moeda base e o período de tempo. Estes dados são utilizados para acompanhar a dinâmica do fluxo de caixa durante o tempo de vida útil da conta.
* **Serviço de notificação (Não utilizado neste arranque rápido)**: Armazena as definições de informações de contacto dos utilizadores e de notificação, tais como frequência de lembrete e backup. O trabalhador regular recolhe informações necessárias de outros serviços e envia mensagens de correio eletrónico aos clientes subscritos.

## <a name="infrastructure-services"></a>Serviços de infraestruturas

Existem vários padrões comuns em sistemas distribuídos que ajudam a fazer os serviços centrais funcionarem. A nuvem de primavera Azure fornece ferramentas poderosas que melhoram o comportamento das aplicações da Spring Boot para implementar esses padrões: 

* **Serviço Config (Hospedado por Azure Spring Cloud)**: Azure Spring Cloud Config é um serviço de configuração centralizado horizontalmente escalável para sistemas distribuídos. Usa um repositório pluggable que atualmente suporta armazenamento local, Git e Subversão.
* **Descoberta de serviço (Hospedada por Azure Spring Cloud)**: Permite a deteção automática de localizações de rede para casos de serviço, que poderiam ter endereços atribuídos dinamicamente devido a autoscaling, falhas e atualizações.
* **Serviço Auth (a ser implantado)** As responsabilidades de autorização são completamente extraídas para um servidor separado, que concede fichas OAuth2 para os serviços de recursos backend. O Auth Server autoriza o utilizador e protege a comunicação máquina-a-máquina dentro de um perímetro.
* **API Gateway (A ser implantado)**: Os três serviços centrais expõem uma API externa ao cliente. Nos sistemas do mundo real, o número de funções pode crescer muito rapidamente com a complexidade do sistema. Centenas de serviços podem estar envolvidos na prestação de uma página complexa. O API Gateway é um único ponto de entrada no sistema, usado para lidar com pedidos e encaminhá-los para o serviço de backend apropriado ou para invocar múltiplos serviços de backend, agregando os resultados. 

## <a name="sample-usage-of-piggymetrics"></a>Utilização de amostras de PiggyMetrics

Para obter todos os detalhes de implementação, consulte [a PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). As amostras referem o código de origem conforme necessário.
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Provision Azure Spring Cloud instância](spring-cloud-quickstart-provision-service-instance.md)
