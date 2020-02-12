---
title: Tutorial - Prepare um pedido de Java Spring para implantação em Azure Spring Cloud
description: Neste tutorial, você prepara uma aplicação Java Spring para implantação para Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 1af4bdcfc0fea319b9e5c9f39a1ade3f4492404f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137635"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Prepare uma aplicação java spring para implantação em Azure Spring Cloud

Este quickstart mostra como preparar uma aplicação java spring existente para implantação para Azure Spring Cloud. Se configurado corretamente, a Azure Spring Cloud fornece serviços robustos para monitorizar, escalar e atualizar a sua aplicação Java Spring Cloud.

## <a name="java-runtime-version"></a>Versão Java Runtime

Apenas as aplicações primavera/Java podem ser executadas em Azure Spring Cloud.

Azure Spring Cloud suporta Java 8 e Java 11. O ambiente de hospedagem contém a versão mais recente do Blue Zulu OpenJDK para o Azure. Para mais informações sobre azul Zulu OpenJDK para Azure, consulte [Instalar o JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões spring boot e spring cloud

Azure Spring Cloud suporta apenas aplicativos Spring Boot. Suporta tanto a versão 2.1 da Spring Boot como a versão 2.2. A tabela seguinte lista as combinações suportadas de Spring Boot e Spring Cloud:

Versão Spring Boot | Versão Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

Verifique se o seu ficheiro pom.xml tem as dependências corretas da Bota de primavera e da Nuvem de primavera com base na sua versão Spring Boot.

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências para a versão 2.1 da Bota de primavera

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

A Nuvem de primavera Azure acolhe e gere os componentes da Nuvem de primavera para si. Estes componentes incluem o Registo de Serviço de Nuvem de primavera e o Servidor Config da Nuvem de primavera. Inclua a biblioteca de clientes Azure Spring Cloud nas suas dependências para permitir a comunicação com a sua instância de serviço Azure Spring Cloud.

A tabela seguinte lista as versões corretas da Nuvem de primavera do Azure para a sua aplicação que utiliza spring boot e Spring Cloud.

Versão Spring Boot | Versão Spring Cloud | Versão Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

Inclua uma das seguintes dependências no seu ficheiro pom.xml. Selecione a dependência cuja versão Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência da versão 2.1 da Nuvem de primavera do Azure

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Dependência para Azure Spring Cloud versão 2.2

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para ativar as características incorporadas da Nuvem de primavera Azure, a sua aplicação deve incluir as seguintes dependências. Esta inclusão garante que a sua aplicação se coneja corretamente com cada componente.  

### <a name="service-registry-dependency"></a>Dependência do Registo de Serviços

Para utilizar o serviço de registo de serviços azure gerido, inclua a dependência `spring-cloud-starter-netflix-eureka-client` no ficheiro pom.xml, como mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto final do servidor de Registo de Serviços é automaticamente injetado como variáveis ambientais com a sua aplicação. As aplicações podem registar-se no servidor do Registo de Serviços e descobrir outros microserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência de Configuração Distribuída

Para ativar a Configuração Distribuída, inclua a seguinte dependência `spring-cloud-config-client` na secção de dependências do seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração da sua saque. Caso contrário, a sua aplicação deixa de funcionar com o Config Server.

### <a name="metrics-dependency"></a>Dependência de métricas

Inclua a dependência `spring-boot-starter-actuator` na secção de dependências do seu ficheiro pom.xml, como mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são periodicamente retiradas dos pontos finais JMX. Pode visualizar as métricas utilizando o portal Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de rastreio distribuído

Inclua as seguintes dependências `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` na secção de dependências do seu ficheiro pom.xml:

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar a sua aplicação Java Spring para implantação na Azure Spring Cloud. Para aprender a configurar uma instância do Config Server, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância do Config Server](spring-cloud-tutorial-config-server.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
