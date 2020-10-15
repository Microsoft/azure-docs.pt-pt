---
title: Como preparar uma aplicação para implantação em Azure Spring Cloud
description: Saiba como preparar uma aplicação para implantação na Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 31e25fb8c67e3d271bc37eb4b0d28c67d94a664f
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092805"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Preparar uma aplicação para implantação em Azure Spring Cloud

::: zone pivot="programming-language-csharp"
O Azure Spring Cloud oferece serviços robustos para hospedar, monitorizar, escalar e atualizar uma aplicação Steeltoe. Este artigo mostra como preparar uma aplicação steeltoe existente para implantação para Azure Spring Cloud. 

Este artigo explica as dependências, configuração e código que são necessários para executar uma aplicação .NET Core Steeltoe em Azure Spring Cloud. Para obter informações sobre como implementar uma aplicação para Azure Spring Cloud, consulte [implementar a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md).

>[!Note]
> O suporte steeltoe para Azure Spring Cloud é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem que os clientes experimentem novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para obter mais informações sobre o suporte durante as pré-visualizações, consulte as [FAQ](https://azure.microsoft.com/support/faq/) ou apresente um [pedido de Apoio](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Versões suportadas

Azure Spring Cloud suporta:

* .NET Core 3.1
* Steeltoe 2.4

## <a name="dependencies"></a>Dependências

Instale o pacote [Microsoft.Azure.SpringCloud.Client.](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-programcs"></a>Atualizar Program.cs

No `Program.Main` método, chame o `UseAzureSpringCloudService` método:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .UseAzureSpringCloudService();
```

## <a name="enable-eureka-server-service-discovery"></a>Ativar a descoberta do serviço Eureka Server

Na fonte de configuração que será utilizada quando a aplicação for executada em Azure Spring Cloud, definida `spring.application.name` com o mesmo nome que a aplicação Azure Spring Cloud para a qual o projeto será implementado.

Por exemplo, se implementar um projeto .NET nomeado `EurekaDataProvider` para uma aplicação Azure Spring Cloud com o nome `planet-weather-provider` deappSettings.js* no* ficheiro deve incluir o seguinte JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Use a descoberta do serviço

Para ligar para um serviço utilizando a descoberta do serviço Eureka Server, faça pedidos HTTP para `http://<app_name>` onde está o valor da `app_name` `spring.application.name` aplicação-alvo. Por exemplo, o seguinte código chama o `planet-weather-provider` serviço:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
Este tópico mostra como preparar uma aplicação java spring existente para implantação para Azure Spring Cloud. Se configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorizar, escalar e atualizar a sua aplicação Java Spring Cloud.

Antes de executar este exemplo, pode experimentar o [arranque básico](spring-cloud-quickstart.md).

Outros exemplos explicam como implementar uma aplicação para Azure Spring Cloud quando o ficheiro POM está configurado. 
* [Lance a sua primeira App](spring-cloud-quickstart.md)
* [Criar e executar Microsserviços](spring-cloud-quickstart-sample-app-introduction.md)

Este artigo explica as dependências necessárias e como adicioná-las ao ficheiro POM.

## <a name="java-runtime-version"></a>Versão Java Runtime

Apenas aplicações primavera/Java podem ser executadas em Azure Spring Cloud.

Azure Spring Cloud suporta java 8 e Java 11. O ambiente de hospedagem contém a mais recente versão do Azul Zulu OpenJDK para o Azure. Para obter mais informações sobre o Azul Zulu OpenJDK para a Azure, consulte [instalar o JDK](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring Boot e Spring Cloud

Para preparar uma aplicação de Boot de mola existente para implantação para Azure Spring Cloud incluem as dependências de Spring Boot e Spring Cloud no ficheiro POM da aplicação, como mostrado nas seguintes secções.

A azure Spring Cloud suporta apenas aplicações Spring Boot ou versão Spring Boot 2.1 ou versão 2.2. A tabela a seguir lista as combinações suportadas de Mola Boot e Spring Cloud:

Versão Boot de primavera | Versão Cloud de primavera
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências para a versão 2.1 do Boot de Mola

Para a versão Spring Boot 2.1 adicione as seguintes dependências ao ficheiro POM da aplicação.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Dependências para a versão 2.2 do Boot de Mola

Para a versão Spring Boot 2.2 adicione as seguintes dependências ao ficheiro POM da aplicação.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Dependências para a versão 2.3 do Boot de Mola

Para a versão Spring Boot 2.3 adicione as seguintes dependências ao ficheiro POM da aplicação.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Dependência do cliente Azure Spring Cloud

Azure Spring Cloud acolhe e gere componentes da Cloud spring. Os componentes incluem o Registo de Serviço de Nuvem de primavera e o Servidor Config da Nuvem de primavera. Recomenda-se a utilização do Porta-malas de mola 2.2 ou 2.3. Para o Boot 2.1 da primavera, terá de incluir a biblioteca de clientes Azure Spring Cloud nas suas dependências para permitir a comunicação com a sua instância de serviço Azure Spring Cloud.

A tabela que se segue lista as versões Azure Spring Cloud corretas para a sua aplicação que utiliza o Spring Boot e o Spring Cloud.

Versão Boot de primavera | Versão Cloud de primavera | Versão inicial do cliente Azure Spring Cloud
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2.x | Hoxton.SR8 | Não necessário
2.3.x | Hoxton.SR8 | Não necessário

Inclua a seguinte dependência no seu ficheiro pom.xml se estiver a utilizar o Boot 2.1 da Mola.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```
> [!WARNING]
> Não especifique `server.port` na sua configuração. A Azure Spring Cloud ultrapassará esta definição para um número fixo da porta. Respeite também esta definição e não especifique a porta do servidor no seu código.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Outras dependências recomendadas para permitir funcionalidades da Cloud Azure Spring

Para ativar as funcionalidades incorporadas da Azure Spring Cloud do registo de serviço para o rastreio distribuído, também precisa incluir as seguintes dependências na sua aplicação. Pode deixar cair algumas destas dependências se não precisar de funcionalidades correspondentes para as aplicações específicas.

### <a name="service-registry"></a>Registo de Serviços

Para utilizar o serviço de registo de serviços Azure gerido, inclua a `spring-cloud-starter-netflix-eureka-client` dependência no ficheiro pom.xml como mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto final do servidor de registo de serviço é automaticamente injetado como variáveis ambientais com a sua aplicação. As aplicações podem registar-se no servidor do Registo de Serviço e descobrir outros microserviços dependentes.


#### <a name="enablediscoveryclient-annotation"></a>Anotação enableDiscoveryClient

Adicione a seguinte anotação ao código fonte da aplicação.
```java
@EnableDiscoveryClient
```
Por exemplo, consulte a aplicação piggymetrics de exemplos anteriores:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Configuração distribuída

Para ativar a Configuração Distribuída, inclua a seguinte `spring-cloud-config-client` dependência na secção de dependências do seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração da sua bota. Caso contrário, a sua aplicação deixa de funcionar com o Config Server.

### <a name="metrics"></a>Métricas

Inclua a `spring-boot-starter-actuator` dependência na secção de dependências do seu ficheiro pom.xml como mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são retiradas periodicamente dos pontos finais do JMX. Pode visualizar as métricas utilizando o portal Azure.

 > [!WARNING]
 > Por favor, especifique `spring.jmx.enabled=true` na sua propriedade de configuração. Caso contrário, as métricas não podem ser visualizadas no portal Azure.

### <a name="distributed-tracing"></a>Rastreio distribuído

Inclua o seguinte `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` dependências na secção de dependências do seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Também precisa de permitir que uma instância Azure Application Insights funcione com a sua instância de serviço Azure Spring Cloud. Para obter informações sobre como utilizar o Application Insights com Azure Spring Cloud, consulte a [documentação sobre o rastreio distribuído.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="see-also"></a>Ver também
* [Analisar registos de aplicações e métricas](./diagnostic-services.md)
* [Configurar o Servidor de Configuração](./spring-cloud-tutorial-config-server.md)
* [Use rastreio distribuído com Azure Spring Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Guia de arranque rápido de primavera](https://spring.io/quickstart)
* [Documentação do Boot de primavera](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Passos seguintes

Neste tópico, aprendeu a configurar a sua aplicação Java Spring para implantação na Azure Spring Cloud. Para aprender a configurar uma instância do Servidor Config, consulte [configurar uma instância do Servidor Config](spring-cloud-tutorial-config-server.md).

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end