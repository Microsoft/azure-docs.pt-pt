---
title: Introdução ao Azure Spring Cloud
description: Conheça os recursos e os benefícios do Azure Spring Cloud para implantar e gerenciar aplicativos Spring em Java no Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 9709d7edb7c48ccfdb551d0e837a098289a5097a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721260"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implantação de aplicativos de microatendimento baseados em Spring boot no Azure com zero alterações de código.  O Azure Spring Cloud permite que os desenvolvedores se concentrem em seu código Gerenciando o ciclo de vida de aplicativos Spring Cloud.  O Spring Cloud fornece gerenciamento de ciclo de vida usando monitoramento e diagnóstico abrangentes, gerenciamento de configuração, descoberta de serviços, integração de CI/CD, implantações em verde-azulado e muito mais.

Como parte do ecossistema do Azure, o Azure Spring Cloud permite uma ligação fácil com outros serviços do Azure, incluindo armazenamento, bancos de dados, monitoramento e muito mais.

Atualmente, o Azure Spring Cloud é oferecido como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações sobre o suporte durante as visualizações, leia nossas [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre um [solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para saber mais.

Para começar, conclua o início rápido do Spring Cloud usando o [CLI do Azure](spring-cloud-quickstart-launch-app-cli.md), o [portal do Azure](spring-cloud-quickstart-launch-app-portal.md)ou o [Maven](spring-cloud-quickstart-launch-app-maven.md).

## <a name="application-configuration"></a>Configuração do aplicativo

### <a name="spring-cloud-config-server"></a>Servidor de configuração do Spring Cloud

O servidor de configuração do Spring Cloud do Azure fornece configuração externa em um sistema distribuído com suporte do lado do cliente e do servidor.  O servidor de configuração fornece um local central para gerenciar Propriedades de aplicativo em todos os ambientes.  Para saber mais, visite a [referência do servidor de configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config.md) e conclua o tutorial.

### <a name="enable-bluegreen-deployments"></a>Habilitar implantações azuis/verdes

O Azure Spring Cloud dá suporte a implantações azuis/verdes para liberar e atualizar o código para ambientes de produção.  Aproveitar esse padrão de gerenciamento de alterações permite que os desenvolvedores implementem recursos e alterações de código com a segurança de um fallback imediato, quando necessário.  O Azure permite que os desenvolvedores se concentrem em escrever código gerenciando vários ambientes de produção e facilitando a atualização ou a reversão de alterações de código sem interromper o aplicativo.  Para saber mais sobre ambientes de preparo e implantações azuis/verdes, visite este [artigo de instruções](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatizar pipelines de CI/CD

O Azure Spring Cloud fornece integração com o Azure DevOps usando o CLI do Azure.  Usando o Azure DevOps, você pode automatizar a integração e a implantação de código em seu aplicativo Spring.  Para saber mais, visite este [artigo](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Dimensionar a sua aplicação

O Azure Spring Cloud permite que você dimensione facilmente os micro-serviços em seu painel do Azure Spring Cloud.  O número de vCPUs e a quantidade de memória disponível para os micro-Services podem ser ampliados ou reduzidos para atender às suas necessidades.  O dimensionamento entra em vigor em segundos e não requer alterações de código ou reimplantação.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoramento de aplicativos

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorar seu aplicativo usando rastreamento distribuído e informações de Azure App

As ferramentas de rastreamento distribuídas da Spring Cloud permitem que os desenvolvedores depurem e monitorem as interconexões complexas entre os microserviços em seus aplicativos.  Ao integrar o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o [Application insights](../azure-monitor/insights/insights-overview.md)do Azure, o Azure fornece um poderoso recurso de rastreamento distribuído diretamente do portal do Azure.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Passos seguintes

- [Iniciar seu aplicativo Spring Cloud por meio da CLI](spring-cloud-quickstart-launch-app-cli.md)
