---
title: Tutorial - Use rastreio distribuído com nuvem de mola azure
description: Este tutorial mostra como usar o Rastreio Distribuído da Nuvem de primavera através de Insights de Aplicação Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273199"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Use rastreio distribuído com nuvem de primavera azure

Com as ferramentas de rastreio distribuídas na Nuvem de primavera Azure, pode facilmente depurar e monitorizar problemas complexos. Azure Spring Cloud integra [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com insights de [aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)de Azure. Esta integração proporciona uma poderosa capacidade de rastreio distribuída do portal Azure.

Neste artigo, saiba como:

> [!div class="checklist"]
> * Ativar rastreio distribuído no portal Azure.
> * Adicione a Spring Cloud Sleuth à sua aplicação.
> * Veja mapas de dependência para as suas aplicações de microserviço.
> * Procure dados de rastreio com filtros diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de um serviço Azure Spring Cloud que já está aprovisionado e em funcionamento. Complete o [quickstart na implementação de uma aplicação através do Azure CLI](spring-cloud-quickstart-launch-app-cli.md) para fornecer e executar um serviço Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao ficheiro application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após esta mudança, o remetente Zipkin pode enviar para a web.

1. Ignore este passo se seguiu o nosso [guia para preparar uma aplicação Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, vá ao seu ambiente de desenvolvimento local e edite o seu ficheiro pom.xml para incluir a seguinte dependência da Spring Cloud Sleuth:

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

1. Construa e implante novamente para o seu serviço Azure Spring Cloud para refletir estas alterações.

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostragem

Pode alterar a taxa a que a sua telemetria é recolhida modificando a taxa de amostra. Por exemplo, se quiser provar metade da frequência, abra o ficheiro aplicação.properties e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Se já construiu e implementou uma aplicação, pode modificar a taxa de amostra. Faça-o adicionando a linha anterior como uma variável ambiental no Azure CLI ou no portal Azure.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Vá à sua página de serviço Azure Spring Cloud no portal Azure.
1. Na página **de Monitorização,** selecione **Rastreio Distribuído**.
1. Selecione **a definição editar** ou adicionar uma nova definição.
1. Crie uma nova consulta de Insights de Aplicação ou selecione uma existente.
1. Escolha qual a categoria de registo que pretende monitorizar e especifique o tempo de retenção em dias.
1. Selecione **Aplicar** para aplicar o novo rastreio.

## <a name="view-the-application-map"></a>Ver o mapa de aplicações

Volte à página **de Rastreio Distribuído** e selecione ver mapa de **aplicações**. Reveja a representação visual da sua aplicação e das definições de monitorização. Para aprender a utilizar o mapa de aplicações, consulte o Mapa de [Aplicações: Aplicações distribuídas](https://docs.microsoft.com/azure/azure-monitor/app/app-map)pela Triagem .

## <a name="use-search"></a>Utilizar pesquisa

Utilize a função de pesquisa para consultar outros itens específicos de telemetria. Na página **De rastreio distribuído,** selecione **Search**. Para mais informações sobre como utilizar a função de pesquisa, consulte [A Utilização de Pesquisa em Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)de Aplicação .

## <a name="use-application-insights"></a>Utilizar insights de aplicação

O Application Insights fornece capacidades de monitorização para além do mapa de aplicações e função de pesquisa. Procure no portal Azure o nome da sua aplicação e, em seguida, abra uma página de Informações de Aplicação para encontrar informações de monitorização. Para obter mais orientações sobre como utilizar estas ferramentas, consulte as consultas de registo do [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Desativar insights de aplicação

1. Vá à sua página de serviço Azure Spring Cloud no portal Azure.
1. Na **monitorização,** selecione **Rastreio Distribuído**.
1. Selecione **Desativar** para desativar insights de aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a permitir e compreender o rastreio distribuído na Nuvem de primavera de Azure. Para aprender a ligar a sua aplicação a uma base de dados Azure Cosmos DB, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como se ligar a uma base de dados Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
