---
title: Quickstart - Introdução à app de amostras piggy Metrics - Azure Spring Cloud
description: Descreve a aplicação de amostras Piggy Metrics usada na implementação Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d833c8f136a71d563ce10240f03e2c68e9131687
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951895"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Introdução ao aplicativo de amostra de Piggy Metrics

Neste quickstart, usaremos uma amostra de finanças pessoais de prova de conceito chamada Piggy Metrics para mostrar-lhe como implementar uma aplicação para o serviço Azure Spring Cloud. Piggy Metrics demonstra o padrão de arquitetura de microserviços, e as seguintes secções destacam a quebra de serviços. Você verá como é implantado para Azure com poderosas capacidades fora da caixa Azure Spring Cloud desde a descoberta do serviço, servidor config a registos, métricas e rastreio distribuído.

Para seguir os exemplos de implementação da Cloud Azure Spring, só precisa da localização do código fonte, que é fornecido conforme necessário.

## <a name="functional-services"></a>Serviços funcionais
Piggy Metrics é decomposto em três microserviços principais. Todas elas são aplicações desdobráveis independentemente, organizadas em torno de domínios empresariais.

* **Serviço de conta (a ser implementado)**: Contém lógica de entrada geral do utilizador e validação: rendimentos/despesas itens, poupanças e definições de conta.
* **Serviço de estatísticas (Não utilizado neste arranque rápido)**: Executa cálculos em parâmetros estatísticos importantes e captura séries de tempo para cada conta. O datapoint contém valores, normalizados para a moeda base e o período de tempo. Estes dados são utilizados para acompanhar a dinâmica do fluxo de caixa durante o tempo de vida útil da conta.
* **Serviço de notificação (Não utilizado neste arranque rápido)**: Armazena as definições de informações de contacto dos utilizadores e de notificação, tais como frequência de lembrete e backup. O trabalhador regular recolhe informações necessárias de outros serviços e envia mensagens de correio eletrónico aos clientes subscritos.

## <a name="infrastructure-services"></a>Serviços de infraestruturas
Existem vários padrões comuns em sistemas distribuídos que ajudam a fazer os serviços centrais funcionarem. A nuvem de primavera Azure fornece ferramentas poderosas que melhoram o comportamento das aplicações da Spring Boot para implementar esses padrões: 

* **Serviço Config (Hospedado por Azure Spring Cloud)**: Azure Spring Cloud Config é um serviço de configuração centralizado horizontalmente escalável para sistemas distribuídos. Usa um repositório pluggable que atualmente suporta armazenamento local, Git e Subversão.
* **Descoberta de serviço (Hospedada por Azure Spring Cloud)**: Permite a deteção automática de localizações de rede para casos de serviço, que poderiam ter endereços atribuídos dinamicamente devido a autoscaling, falhas e atualizações.
* **Serviço Auth (será implantado)** As responsabilidades de autorização são completamente extraídas para um servidor separado, que concede fichas OAuth2 para os serviços de recursos backend. O Auth Server autoriza o utilizador e protege a comunicação máquina-a-máquina dentro de um perímetro.
* **API Gateway (será implantada)**: Os três serviços centrais expõem uma API externa ao cliente. Nos sistemas do mundo real, o número de funções pode crescer muito rapidamente com a complexidade do sistema. Centenas de serviços podem estar envolvidos na prestação de uma página complexa. O API Gateway é um único ponto de entrada no sistema, usado para lidar com pedidos e encaminhá-los para o serviço de backend apropriado ou para invocar múltiplos serviços de backend, agregando os resultados. 

## <a name="sample-usage-of-piggy-metrics"></a>Utilização de amostras de métricas de porquinho
Para obter detalhes completos de implementação, consulte [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). As amostras referem o código de origem conforme necessário.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Provision Azure Spring Cloud instância](spring-cloud-quickstart-provision-service-instance.md)
