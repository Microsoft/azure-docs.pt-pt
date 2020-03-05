---
title: Introdução à Nuvem de primavera Azure
description: Aprenda as funcionalidades e benefícios da Nuvem de primavera Azure para implementar e gerir aplicações java spring em Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273264"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

A Nuvem de primavera Azure facilita a implementação de aplicações de microserviço baseadas em Botas de primavera para O Azure com alterações de código zero.  A Azure Spring Cloud gere o ciclo de vida das aplicações da Nuvem de primavera, para que os desenvolvedores possam focar-se no seu código.  A Spring Cloud fornece uma gestão de ciclo de vida utilizando monitorização e diagnóstico abrangentes, gestão de configuração, descoberta de serviços, integração CI/CD, implementações azul-verde, e muito mais.

Como parte do ecossistema Azure, a Nuvem de primavera Azure permite uma fácil ligação a outros serviços Azure, incluindo armazenamento, bases de dados, monitorização e muito mais.

A Nuvem de primavera Azure é atualmente oferecida como uma pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.  Para mais informações sobre suporte durante as pré-visualizações, por favor reveja as nossas [FAQ](https://azure.microsoft.com/support/faq/) ou preencha um pedido de [Suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

Para começar, complete o quickstart spring cloud utilizando o [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), o [portal Azure](spring-cloud-quickstart-launch-app-portal.md), ou [Maven](spring-cloud-quickstart-launch-app-maven.md).

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Configuração da aplicação

### <a name="spring-cloud-config-server"></a>Servidor Config nuvem de primavera

O Azure Spring Cloud Config Server fornece uma configuração externa num sistema distribuído com suporte ao servidor e ao cliente.  O Config Server fornece uma localização central para gerir as propriedades da aplicação em todos os ambientes.  Para saber mais, visite a referência do [Servidor Config](https://spring.io/projects/spring-cloud-config.md) da Nuvem de primavera e complete o tutorial.

### <a name="enable-bluegreen-deployments"></a>Ativar implantações azuis/verdes

A Azure Spring Cloud suporta implementações azuis/verdes para a libertação e atualização do código para ambientes de produção.  A alavancagem deste padrão de gestão de alterações permite aos desenvolvedores implementar funcionalidades e alterações de código com a segurança de um recuo imediato quando necessário.  O Azure permite que os desenvolvedores se concentrem no código de escrita, gerindo múltiplos ambientes de produção e facilitando a atualização ou reversão das alterações de código sem interromper a aplicação.  Para saber mais sobre ambientes de encenação e implantações azuis/verdes, visite este [artigo Como-Fazer](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatizar os gasodutos CI/CD

A Azure Spring Cloud proporciona integração com o Azure DevOps utilizando o Azure CLI.  Utilizando o Azure DevOps, pode automatizar a integração e implementação de códigos na sua aplicação Spring.  Para saber mais, visite este [artigo.](spring-cloud-howto-cicd.md)

### <a name="scale-your-application"></a>Escala a sua aplicação

A Nuvem de primavera Azure permite-lhe escalar facilmente os microserviços no seu painel de instrumentos Azure Spring Cloud.  Tanto o número de vCPUs como a quantidade de memória disponível para os seus microserviços podem ser dimensionados para cima ou para baixo de acordo com os seus requisitos.  A escala faz efeito em segundos e não requer alterações de código ou redistribuição.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-scale-manual.md)

## <a name="application-monitoring"></a>Monitorização de Aplicações

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorize a sua aplicação utilizando rastreios distribuídos e Insights de Aplicações Azure

As ferramentas de rastreio distribuídas da Spring Cloud permitem aos desenvolvedores desbugicar e monitorizar as complexas interligações entre microserviços numa aplicação.  Ao integrar a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com a Azure's [Application Insights,](../azure-monitor/insights/insights-overview.md)o Azure fornece uma poderosa capacidade de rastreio distribuída diretamente do portal Azure.  Para saber mais, complete este [tutorial.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="next-steps"></a>Passos seguintes

- [Lance a sua aplicação Spring Cloud a partir do CLI](spring-cloud-quickstart-launch-app-cli.md)
