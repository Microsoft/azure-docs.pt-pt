---
title: Tutorial-preparar um aplicativo Spring para implantação no Azure Spring Cloud
description: Neste tutorial, você prepara um aplicativo Spring Java para implantação.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 7a879fa942046376e8cf0acc40a62039e8f3de25
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064721"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este guia de início rápido mostra como preparar um aplicativo Java Spring Cloud existente para implantação no Azure Spring Cloud. Se configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Java Spring Cloud.

## <a name="java-runtime-version"></a>Versão de tempo de execução Java

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

O Azure Spring Cloud dá suporte a Java 8 e Java 11. O ambiente de hospedagem contém a versão mais recente do azul Zulu OpenJDK para o Azure. Para mais informações sobre azul Zulu OpenJDK para Azure, consulte [Instalar o JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring boot e Spring Cloud

O Azure Spring Cloud dá suporte apenas a aplicativos Spring boot. Suporta tanto a versão 2.1 da Spring Boot como a versão 2.2. A tabela a seguir lista as combinações de Spring boot e Spring Cloud com suporte:

Versão do Spring boot | Versão da Spring Cloud
---|---
2.1 | Greenwich. RELEASE
2.2 | Hoxton.RELEASE

Verifique se o arquivo pom. XML tem as dependências de Spring boot e Spring Cloud corretas com base em sua versão do Spring boot.

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências para o Spring boot versão 2,1

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

## <a name="azure-spring-cloud-client-dependency"></a>Dependência de cliente de nuvem do Azure Spring

O Azure Spring Cloud hospeda e gerencia os componentes do Spring Cloud para você. Esses componentes incluem o registro de serviço de nuvem Spring e o servidor de configuração do Spring Cloud. Inclua a biblioteca de cliente de nuvem do Azure Spring em suas dependências para permitir a comunicação com sua instância do serviço de nuvem do Azure Spring.

A tabela a seguir lista as versões corretas do Azure Spring Cloud para seu aplicativo que usa Spring boot e Spring Cloud.

Versão do Spring boot | Versão da Spring Cloud | Versão do Azure Spring Cloud
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

Inclua uma das seguintes dependências em seu arquivo pom. xml. Selecione a dependência cuja versão do Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência do Azure Spring Cloud versão 2,1

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

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Essa inclusão garante que seu aplicativo se configure corretamente com cada componente.  

### <a name="service-registry-dependency"></a>Dependência do registro de serviço

Para utilizar o serviço de registo de serviços azure gerido, inclua a dependência `spring-cloud-starter-netflix-eureka-client` no ficheiro pom.xml, como mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto de extremidade do servidor de registro de serviço é injetado automaticamente como variáveis de ambiente com seu aplicativo. Os aplicativos podem se registrar com o servidor de registro de serviço e descobrir outros microserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência de configuração distribuída

Para ativar a Configuração Distribuída, inclua a seguinte dependência `spring-cloud-config-client` na secção de dependências do seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração da sua saque. Caso contrário, seu aplicativo para de funcionar com o servidor de configuração.

### <a name="metrics-dependency"></a>Dependência de métricas

Inclua a dependência `spring-boot-starter-actuator` na secção de dependências do seu ficheiro pom.xml, como mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são retiradas periodicamente dos pontos de extremidade JMX. Você pode visualizar as métricas usando o portal do Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de rastreamento distribuído

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

 Você também precisa habilitar uma instância do Aplicativo Azure insights para trabalhar com sua instância do serviço de nuvem do Azure Spring. Leia o [tutorial sobre rastreiodistribuído](spring-cloud-tutorial-distributed-tracing.md) para aprender a usar insights de aplicação com nuvem de primavera azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a configurar seu aplicativo Java Spring para implantação no Azure Spring Cloud. Para saber como configurar uma instância de servidor de configuração, vá para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância do Config Server](spring-cloud-tutorial-config-server.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
