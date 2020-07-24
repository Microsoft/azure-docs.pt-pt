---
title: Use rastreio distribuído com nuvem de primavera Azure
description: Saiba como usar o rastreio distribuído da Cloud da primavera através do Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1e3579f79f9daa80c3d3f2206be7a76cc5505e80
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037024"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Use rastreio distribuído com Azure Spring Cloud

Com as ferramentas de rastreio distribuídas em Azure Spring Cloud, você pode facilmente depurar e monitorizar problemas complexos. A Azure Spring Cloud integra [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com os Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)da Azure. Esta integração fornece uma poderosa capacidade de rastreio distribuído a partir do portal Azure.

Neste artigo, saiba como:

> [!div class="checklist"]
> * Ativar o rastreio distribuído no portal Azure.
> * Adicione a Spring Cloud Sleuth à sua aplicação.
> * Veja os mapas de dependência das suas aplicações de microserviços.
> * Pesquisar dados de rastreio com filtros diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, precisa de um serviço Azure Spring Cloud que já esteja a ser a provisionado e em funcionamento. Complete o [quickstart na implementação de uma aplicação através do CLI Azure](spring-cloud-quickstart-launch-app-cli.md) para provisões e executar um serviço Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao ficheiro application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após esta alteração, o remetente Zipkin pode enviar para a web.

1. Ignore este passo se seguir o nosso [guia para preparar uma aplicação Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, vá ao seu ambiente de desenvolvimento local e edite o seu ficheiro pom.xml para incluir a seguinte dependência da Sleuth cloud da primavera:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Construa e implemente novamente para o seu serviço Azure Spring Cloud para refletir estas alterações.

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostra

Pode alterar a taxa a que a sua telemetria é recolhida modificando a taxa de amostragem. Por exemplo, se quiser provar metade com a frequência, abra o ficheiro application.properties e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Se já construiu e implementou uma aplicação, pode modificar a taxa de amostra. Faça-o adicionando a linha anterior como uma variável ambiental no CLI Azure ou no portal Azure.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure.
1. Na página **de Monitorização,** selecione **Distributed Tracing**.
1. Selecione **a definição de Edição** para editar ou adicione uma nova definição.
1. Crie uma nova consulta de Insights de Aplicação ou selecione uma existente.
1. Escolha qual a categoria de registo que pretende monitorizar e especifique o tempo de retenção em dias.
1. **Selecione Aplicar** para aplicar o novo rastreio.

## <a name="view-the-application-map"></a>Ver o mapa de aplicações

Volte à página **de rastreio distribuído** e selecione Ver mapa de **aplicações.** Reveja a representação visual das definições de aplicação e monitorização. Para saber como utilizar o mapa de aplicações, consulte o [Mapa de Aplicações: Aplicações distribuídas por triagem.](https://docs.microsoft.com/azure/azure-monitor/app/app-map)

## <a name="use-search"></a>Use a pesquisa

Utilize a função de pesquisa para consultar outros itens específicos de telemetria. Na página **de rastreio distribuído,** selecione **Search**. Para obter mais informações sobre como utilizar a função de pesquisa, consulte ['Pesquisar em Insights de Aplicação'.](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)

## <a name="use-application-insights"></a>Utilizar insights de aplicações

O Application Insights fornece capacidades de monitorização para além do mapa de aplicações e da função de pesquisa. Procure o nome do portal Azure e, em seguida, abra uma página de Insights de Aplicação para encontrar informações de monitorização. Para obter mais informações sobre como utilizar estas ferramentas, consulte [as consultas de registo do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Insights de aplicações para desativação

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure.
1. No **Monitor,** selecione **Distributed Tracing**.
1. Selecione **Desativar** para desativar insights de aplicações.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e a compreender o rastreio distribuído em Azure Spring Cloud. Para saber mais sobre serviços de ligação a uma aplicação, consulte [uma base de dados DB da Azure Cosmos para uma aplicação Azure Spring Cloud](spring-cloud-tutorial-bind-cosmos.md).
