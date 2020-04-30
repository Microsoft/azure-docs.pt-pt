---
title: Como - Preparar uma aplicação java spring para implantação em Azure Spring Cloud
description: Neste tópico, você prepara uma aplicação Java Spring para implantação para Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731169"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Prepare uma aplicação java spring para implantação em Azure Spring Cloud

Este tópico mostra como preparar uma aplicação java primavera existente para implantação para Azure Spring Cloud. Se configurado corretamente, a Azure Spring Cloud fornece serviços robustos para monitorizar, escalar e atualizar a sua aplicação Java Spring Cloud.

Outros exemplos explicam como implementar uma aplicação para a Nuvem de primavera Azure quando o ficheiro POM está configurado. 
* [App de lançamento usando o portal Azure](spring-cloud-quickstart-launch-app-portal.md)
* [App de lançamento usando o Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

Este artigo explica as dependências necessárias e como adicioná-las ao ficheiro POM.

## <a name="java-runtime-version"></a>Versão Java Runtime

Apenas as aplicações primavera/Java podem ser executadas em Azure Spring Cloud.

Azure Spring Cloud suporta Java 8 e Java 11. O ambiente de hospedagem contém a versão mais recente do Blue Zulu OpenJDK para o Azure. Para mais informações sobre azul Zulu OpenJDK para Azure, consulte [Instalar o JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões spring boot e spring cloud

Para preparar uma aplicação de Boot de primavera existente para implantação para Azure Spring Cloud inclua as dependências da Bota de primavera e da Nuvem de primavera no ficheiro POM da aplicação, como mostrado nas seguintes secções.

O Azure Spring Cloud suporta apenas aplicações spring boot, quer a versão 2.1 da Spring Boot quer a versão 2.2. A tabela seguinte lista as combinações suportadas de Spring Boot e Spring Cloud:

Versão Spring Boot | Versão Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2,2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências para a versão 2.1 da Bota de primavera

Para a versão 2.1 da Spring Boot adicione as seguintes dependências ao ficheiro POM da aplicação.

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
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Dependências para a versão 2.2 da Bota de primavera

Para a versão 2.2 da Spring Boot, adicione as seguintes dependências ao ficheiro POM da aplicação.

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
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dependência do cliente da Nuvem de primavera Azure

A Nuvem de primavera Azure acolhe e gere os componentes da Nuvem de primavera. Os componentes incluem o Registo de Serviço de Nuvem de primavera e o Servidor Config da Nuvem de primavera. Inclua a biblioteca de clientes Azure Spring Cloud nas suas dependências para permitir a comunicação com a sua instância de serviço Azure Spring Cloud.

A tabela seguinte lista as versões corretas da Nuvem de primavera do Azure para a sua aplicação que utiliza spring boot e Spring Cloud.

Versão Spring Boot | Versão Spring Cloud | Versão Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2,2 | Hoxton.RELEASE | 2,2

Inclua uma das seguintes dependências no seu ficheiro pom.xml. Selecione a dependência cuja versão Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência da versão 2.1 da Nuvem de primavera do Azure

Para a versão 2.1 da Bota de Mola, adicione a seguinte dependência ao ficheiro POM da aplicação.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Dependência para Azure Spring Cloud versão 2.2

Para a versão 2.2 da Spring Boot adicione a seguinte dependência ao ficheiro POM da aplicação.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para ativar as características incorporadas da Nuvem de primavera Azure, a sua aplicação deve incluir as seguintes dependências. Esta inclusão garante que a sua aplicação se coneja corretamente com cada componente.

### <a name="enablediscoveryclient-annotation"></a>Anotação EnableDiscoveryClient

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

### <a name="service-registry-dependency"></a>Dependência do Registo de Serviços

Para utilizar o serviço de registo de `spring-cloud-starter-netflix-eureka-client` serviços azure gerido, inclua a dependência do ficheiro pom.xml, como mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto final do servidor de Registo de Serviços é automaticamente injetado como variáveis ambientais com a sua aplicação. As aplicações podem registar-se no servidor do Registo de Serviços e descobrir outros microserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência de Configuração Distribuída

Para ativar a Configuração `spring-cloud-config-client` Distribuída, inclua a seguinte dependência na secção de dependências do seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração da sua saque. Caso contrário, a sua aplicação deixa de funcionar com o Config Server.

### <a name="metrics-dependency"></a>Dependência de métricas

Inclua `spring-boot-starter-actuator` a dependência na secção de dependências do seu ficheiro pom.xml, como mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são periodicamente retiradas dos pontos finais JMX. Pode visualizar as métricas utilizando o portal Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de rastreio distribuído

Inclua `spring-cloud-starter-sleuth` as `spring-cloud-starter-zipkin` seguintes dependências na secção de dependências do seu ficheiro pom.xml:

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

 Também precisa de permitir que um exemplo de Insights de Aplicação Azure funcione com a sua instância de serviço Azure Spring Cloud. Leia o [tutorial sobre rastreiodistribuído](spring-cloud-tutorial-distributed-tracing.md) para aprender a usar insights de aplicação com nuvem de primavera azure.

## <a name="see-also"></a>Consulte também
* [Analisar registos e métricas de aplicações](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Configurar o Servidor de Configuração](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Use rastreio distribuído com nuvem de primavera azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Guia de arranque rápido da primavera](https://spring.io/quickstart)
* [Documentação da Bota de primavera](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Passos seguintes

Neste tópico, aprendeu a configurar a sua aplicação Java Spring para implantação na Azure Spring Cloud. Para aprender a configurar uma instância do Config Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância do Config Server](spring-cloud-tutorial-config-server.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
