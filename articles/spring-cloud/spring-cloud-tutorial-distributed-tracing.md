---
title: Tutorial – usar o rastreamento distribuído com o Azure Spring Cloud
description: Este tutorial mostra como usar o rastreamento distribuído da Spring Cloud por meio do Aplicativo Azure insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 7241287e0438d6da5efb517a89b984bff72848c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461490"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Usar o rastreamento distribuído com o Azure Spring Cloud

Com as ferramentas de rastreamento distribuído no Azure Spring Cloud, você pode facilmente depurar e monitorar problemas complexos. O Azure Spring Cloud integra o [Azure Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)do Azure. Essa integração fornece uma poderosa funcionalidade de rastreamento distribuído do portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Habilite o rastreamento distribuído no portal do Azure.
> * Adicione o Azure Spring Cloud Sleuth ao seu aplicativo.
> * Exiba mapas de dependências para seus aplicativos de microatendimento.
> * Pesquisar dados de rastreamento com filtros diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um serviço de nuvem do Azure Spring que já esteja provisionado e em execução. Conclua o guia de [início rápido sobre como implantar um aplicativo por meio do CLI do Azure](spring-cloud-quickstart-launch-app-cli.md) para provisionar e executar um serviço de nuvem do Azure Spring.
    
## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao arquivo Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após essa alteração, o remetente Zipkin pode enviar para a Web.

1. Ignore esta etapa se você seguiu o nosso [guia para preparar um aplicativo do Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, vá para o ambiente de desenvolvimento local e edite seu arquivo pom. xml para incluir a seguinte dependência do Azure Spring Cloud Sleuth:

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

1. Crie e implante novamente para o serviço de nuvem Spring do Azure para refletir essas alterações.

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostra

Você pode alterar a taxa na qual a telemetria é coletada modificando a taxa de amostra. Por exemplo, se você quiser fazer amostragem da metade com frequência, abra o arquivo Application. Properties e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Se você já tiver criado e implantado um aplicativo, poderá modificar a taxa de amostra. Faça isso adicionando a linha anterior como uma variável de ambiente no CLI do Azure ou no portal do Azure.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure.
1. Na página **monitoramento** , selecione **rastreamento distribuído**.
1. Selecione **Editar configuração** para editar ou adicionar uma nova configuração.
1. Crie uma nova consulta Application Insights ou selecione uma existente.
1. Escolha qual categoria de log você deseja monitorar e especifique o tempo de retenção em dias.
1. Selecione **aplicar** para aplicar o novo rastreamento.

## <a name="view-the-application-map"></a>Exibir o mapa do aplicativo

Retorne à página de **rastreamento distribuído** e selecione **exibir mapa do aplicativo**. Examine a representação visual do seu aplicativo e as configurações de monitoramento. Para saber como usar o mapa do aplicativo, consulte [mapa do aplicativo: triagem de aplicativos distribuídos](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Usar pesquisa

Use a função de pesquisa para consultar outros itens de telemetria específicos. Na página **rastreamento distribuído** , selecione **Pesquisar**. Para obter mais informações sobre como usar a função de pesquisa, consulte [usando a pesquisa no Application insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Usar Application Insights

Application Insights fornece recursos de monitoramento além do mapa do aplicativo e da função de pesquisa. Pesquise o nome do seu aplicativo no portal do Azure e, em seguida, abra uma página de Application Insights para localizar informações de monitoramento. Para obter mais diretrizes sobre como usar essas ferramentas, confira [Azure monitor consultas de log](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Desabilitar Application Insights

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure.
1. Em **monitoramento**, selecione **rastreamento distribuído**.
1. Selecione **desabilitar** para desabilitar o Application insights.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a habilitar e a entender o rastreamento distribuído no Azure Spring Cloud. Para saber como associar seu aplicativo a um Azure Cosmos DB banco de dados, vá para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como associar a um banco de dados Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
