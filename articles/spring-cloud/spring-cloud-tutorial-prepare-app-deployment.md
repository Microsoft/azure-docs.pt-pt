---
title: Tutorial-preparar um aplicativo Spring para implantação no Azure Spring Cloud
description: Neste tutorial, você prepara um aplicativo Spring Java para implantação.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6e35430713a3dbc8317944fed1180432a2083676
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461593"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este guia de início rápido mostra como preparar um aplicativo Java Spring Cloud existente para implantação no Azure Spring Cloud. Se configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Java Spring Cloud.

## <a name="java-runtime-version"></a>Versão de tempo de execução Java

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

O Azure Spring Cloud dá suporte a Java 8 e Java 11. O ambiente de hospedagem contém a versão mais recente do azul Zulu OpenJDK para o Azure. Para obter mais informações sobre azul Zulu OpenJDK para o Azure, consulte [instalar o JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring boot e Spring Cloud

O Azure Spring Cloud dá suporte apenas a aplicativos Spring boot. Ele dá suporte tanto ao Spring boot versão 2,0 quanto à versão 2,1. A tabela a seguir lista as combinações de Spring boot e Spring Cloud com suporte:

Versão do Spring boot | Versão da Spring Cloud
---|---
2.0 | Finchley. RELEASE
2.1 | Greenwich. RELEASE

Verifique se o arquivo pom. XML tem as dependências de Spring boot e Spring Cloud corretas com base em sua versão do Spring boot.

### <a name="dependencies-for-spring-boot-version-20"></a>Dependências para o Spring boot versão 2,0

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências para o Spring boot versão 2,1

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
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
2.0 | Finchley. RELEASE | 2.0
2.1 | Greenwich. RELEASE | 2.1

Inclua uma das seguintes dependências em seu arquivo pom. xml. Selecione a dependência cuja versão do Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Dependência do Azure Spring Cloud versão 2,0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência do Azure Spring Cloud versão 2,1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Essa inclusão garante que seu aplicativo se configure corretamente com cada componente.  

### <a name="service-registry-dependency"></a>Dependência do registro de serviço

Para usar o serviço gerenciado do registro de serviço do Azure, inclua a dependência `spring-cloud-starter-netflix-eureka-client` no arquivo pom. xml, conforme mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto de extremidade do servidor de registro de serviço é injetado automaticamente como variáveis de ambiente com seu aplicativo. Os aplicativos podem se registrar com o servidor de registro de serviço e descobrir outros microserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência de configuração distribuída

Para habilitar a configuração distribuída, inclua a seguinte dependência de `spring-cloud-config-client` na seção de dependências do arquivo pom. xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` em sua configuração de inicialização. Caso contrário, seu aplicativo para de funcionar com o servidor de configuração.

### <a name="metrics-dependency"></a>Dependência de métricas

Inclua a dependência `spring-boot-starter-actuator` na seção dependências do arquivo pom. xml, conforme mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são retiradas periodicamente dos pontos de extremidade JMX. Você pode visualizar as métricas usando o portal do Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de rastreamento distribuído

Inclua as dependências de `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` a seguir na seção de dependências do arquivo pom. xml:

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

 Você também precisa habilitar uma instância do Aplicativo Azure insights para trabalhar com sua instância do serviço de nuvem do Azure Spring. Leia o [tutorial sobre o rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md) para saber como usar Application insights com o Azure Spring Cloud.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a configurar seu aplicativo Java Spring para implantação no Azure Spring Cloud. Para saber como configurar uma instância de servidor de configuração, vá para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância de servidor de configuração](spring-cloud-tutorial-config-server.md)

Mais exemplos estão disponíveis no GitHub: [exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
