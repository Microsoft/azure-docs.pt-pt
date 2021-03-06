---
title: Use rastreio distribuído com nuvem de primavera Azure
description: Saiba como usar o rastreio distribuído da Cloud da primavera através do Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6bcb020b14952541c673592c1040fca211ed4edf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011856"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Use rastreio distribuído com Azure Spring Cloud

Com as ferramentas de rastreio distribuídas em Azure Spring Cloud, você pode facilmente depurar e monitorizar problemas complexos. A Azure Spring Cloud integra [a Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) com os Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md)da Azure. Esta integração fornece uma poderosa capacidade de rastreio distribuído a partir do portal Azure.

::: zone pivot="programming-language-csharp"
Neste artigo, você aprende a permitir que uma aplicação .NET Core Steeltoe utilize o rastreio distribuído.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, precisa de uma aplicação Steeltoe que já esteja [preparada para ser implantada na Azure Spring Cloud.](how-to-prepare-app-deployment.md)

## <a name="dependencies"></a>Dependências

Para steeltoe 2.4.4, adicione os seguintes pacotes NuGet:

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe.Management.ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Para steeltoe 3.0.0, adicione o seguinte pacote NuGet:

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Atualização de arranque.cs

1. Para Steeltoe 2.4.4, ligue `AddDistributedTracing` e `AddZipkinExporter` no `ConfigureServices` método.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Para Steeltoe 3.0.0, ligue `AddDistributedTracing` para o `ConfigureServices` método.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Para steeltoe 2.4.4, ligue `UseTracingExporter` para o `Configure` método.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Para o Steeltoe 3.0.0, não são necessárias alterações no `Configure` método.

## <a name="update-configuration"></a>Configuração de atualização

Adicione as seguintes definições à fonte de configuração que será usada quando a aplicação for executada na Cloud Azure Spring:

1. Defina `management.tracing.alwaysSample` como verdadeiro.

2. Se pretender ver os intervalos de rastreio enviados entre o servidor Eureka, o servidor de Configuração e as aplicações do utilizador: definido `management.tracing.egressIgnorePattern` para "/api/v2/spans|/v2/apps/.*/permissões|/eureka/.*| /oauth/.*".

Por exemplo, *appsettings.jsincluiria* as seguintes propriedades:

```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Para obter mais informações sobre o rastreio distribuído em aplicações .NET Core Steeltoe, consulte [o rastreio distribuído](https://steeltoe.io/docs/3/tracing/distributed-tracing) na documentação steeltoe.
::: zone-end
::: zone pivot="programming-language-java"
Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Ativar o rastreio distribuído no portal Azure.
> * Adicione a Spring Cloud Sleuth à sua aplicação.
> * Veja os mapas de dependência das suas aplicações de microserviços.
> * Pesquisar dados de rastreio com filtros diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, precisa de um serviço Azure Spring Cloud que já esteja a ser a provisionado e em funcionamento. Complete a [aplicação Azure Spring Cloud](spring-cloud-quickstart.md) de primeira linha para obter e executar um serviço Azure Spring Cloud.

## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao ficheiro application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após esta alteração, o remetente Zipkin pode enviar para a web.

1. Ignore este passo se seguir o nosso [guia para preparar uma aplicação Azure Spring Cloud](how-to-prepare-app-deployment.md). Caso contrário, vá ao seu ambiente de desenvolvimento local e edite o seu ficheiro pom.xml para incluir a seguinte dependência da Sleuth cloud da primavera:

    * Versão de arranque de mola < 2.4.x.

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
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-zipkin</artifactId>
          </dependency>
      </dependencies>
      ```

    * Versão de arranque de mola >= 2,4.x.

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
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-sleuth-zipkin</artifactId>
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
::: zone-end

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure.
1. Na página **de Monitorização,** selecione **Distributed Tracing**.
1. Selecione **a definição de Edição** para editar ou adicione uma nova definição.
1. Crie uma nova consulta de Insights de Aplicação ou selecione uma existente.
1. Escolha qual a categoria de registo que pretende monitorizar e especifique o tempo de retenção em dias.
1. **Selecione Aplicar** para aplicar o novo rastreio.

## <a name="view-the-application-map"></a>Ver o mapa de aplicações

Volte à página **de rastreio distribuído** e selecione Ver mapa de **aplicações.** Reveja a representação visual das definições de aplicação e monitorização. Para saber como utilizar o mapa de aplicações, consulte o [Mapa de Aplicações: Aplicações distribuídas por triagem.](../azure-monitor/app/app-map.md)

## <a name="use-search"></a>Use a pesquisa

Utilize a função de pesquisa para consultar outros itens específicos de telemetria. Na página **de rastreio distribuído,** selecione **Search**. Para obter mais informações sobre como utilizar a função de pesquisa, consulte ['Pesquisar em Insights de Aplicação'.](../azure-monitor/app/diagnostic-search.md)

## <a name="use-application-insights"></a>Utilizar insights de aplicações

O Application Insights fornece capacidades de monitorização para além do mapa de aplicações e da função de pesquisa. Procure o nome do portal Azure e, em seguida, abra uma página de Insights de Aplicação para encontrar informações de monitorização. Para obter mais informações sobre como utilizar estas ferramentas, consulte [as consultas de registo do Azure Monitor](/azure/data-explorer/kusto/query/).

## <a name="disable-application-insights"></a>Insights de aplicações para desativação

1. Aceda à sua página de serviço Azure Spring Cloud no portal Azure.
1. No **Monitor,** selecione **Distributed Tracing**.
1. Selecione **Desativar** para desativar insights de aplicações.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e a compreender o rastreio distribuído em Azure Spring Cloud. Para saber mais sobre serviços de ligação a uma aplicação, consulte [uma base de dados DB da Azure Cosmos para uma aplicação Azure Spring Cloud](spring-cloud-howto-bind-cosmos.md).