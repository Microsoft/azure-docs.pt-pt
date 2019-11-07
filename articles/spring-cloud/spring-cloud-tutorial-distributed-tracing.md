---
title: 'Tutorial: usar o rastreamento distribuído com o Azure Spring Cloud | Microsoft Docs'
description: Saiba como usar o rastreamento distribuído da Spring Cloud por meio do Aplicativo Azure insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: a9d2100103cdd5858d0d58cf6ef77a6ccac3745f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607554"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Tutorial: usando o rastreamento distribuído com o Azure Spring Cloud

As ferramentas de rastreamento distribuídas da Spring Cloud permitem a fácil depuração e o monitoramento de problemas complexos. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com o [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) do Azure para fornecer um poderoso recurso de rastreamento distribuído do portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Habilitar o rastreamento distribuído no portal do Azure
> * Adicionar o Spring Cloud Sleuth ao seu aplicativo
> * Exibir mapas de dependências para seus aplicativos de microatendimento
> * Pesquisar dados de rastreamento com filtros diferentes

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Um serviço de nuvem do Azure Spring já provisionado e em execução.  Conclua este guia de [início rápido](spring-cloud-quickstart-launch-app-cli.md) para provisionar e iniciar um serviço de nuvem do Azure Spring.
    
## <a name="add-dependencies"></a>Adicionar dependências

Habilite o remetente zipkin para enviar à Web adicionando a seguinte linha ao arquivo Application. Properties:

```xml
spring.zipkin.sender.type = web
```

Você poderá ignorar a próxima etapa se tiver seguido nosso [guia para preparando um aplicativo de nuvem do Azure Spring](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, vá para seu ambiente de desenvolvimento local e edite seu arquivo `pom.xml` para incluir a dependência Sleuth Spring Cloud:

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

* Crie e implante novamente para o serviço de nuvem Spring do Azure para refletir essas alterações. 

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostra
Você pode alterar a taxa na qual a telemetria é coletada modificando a taxa de amostra. Por exemplo, se você quiser fazer amostragem da metade com frequência, vá para o arquivo `application.properties` e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Se você já tiver um aplicativo criado e implantado, poderá modificar a taxa de amostra adicionando a linha acima como uma variável de ambiente no CLI do Azure ou no Portal. 

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure.
1. Na seção monitoramento, selecione **rastreamento distribuído**.
1. Selecione **Editar configuração** para editar ou adicionar uma nova configuração.
1. Crie uma nova consulta do Application Insight ou você pode selecionar uma existente.
1. Escolha qual categoria de log você deseja monitorar e especifique o tempo de retenção (em dias).
1. Selecione **aplicar** para aplicar o novo rastreamento.

## <a name="view-application-map"></a>Exibir mapa do aplicativo

Retorne à página de rastreamento distribuído e selecione **exibir mapa do aplicativo**. Examine a representação visual do seu aplicativo e as configurações de monitoramento. Para saber como usar o mapa do aplicativo, consulte [Este artigo](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Pesquisa

Use a função de pesquisa para consultar outros itens de telemetria específicos. Na página **rastreamento distribuído** , selecione **Pesquisar**. Para obter mais informações sobre como usar a função de pesquisa, consulte [Este artigo](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Página de Application Insights

Application Insights fornece recursos de monitoramento além do mapa do aplicativo e da pesquisa. Pesquise o nome do seu aplicativo na portal do Azure e, em seguida, inicie uma página de Application Insights para saber mais. Para obter mais diretrizes sobre como usar essas ferramentas, [Confira a documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Desabilitar Application Insights

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure.
1. Na seção monitoramento, clique em **rastreamento distribuído**.
1. Clique em **desabilitar** para desabilitar o Application insights

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a habilitar e a entender o rastreamento distribuído na nuvem do Azure Spring. Para saber como associar seu aplicativo a um CosmosDB do Azure, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como associar seu aplicativo a um CosmosDB do Azure](spring-cloud-tutorial-bind-cosmos.md).
