---
title: Preparar um aplicativo Spring para implantação no Azure Spring Cloud | Microsoft Docs
description: Neste guia de início rápido, você prepara um aplicativo Spring Java para implantação.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6c3048fd27c3d3d4fdba0841fb4ab21bd10e15fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721246"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Tutorial: preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este guia de início rápido mostra como preparar um aplicativo Java Spring Cloud existente para implantação no Azure Spring Cloud.  Configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Spring Cloud. 

## <a name="java-runtime-version"></a>Versão de tempo de execução Java

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

Há suporte para Java 8 e Java 11. O ambiente de hospedagem contém o azul Zulu OpenJDK mais recente para o Azure. Consulte [Este artigo](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) para obter mais informações sobre azul Zulu OpenJDK para Azure. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring boot e Spring Cloud

Somente os aplicativos Spring boot têm suporte no Azure Spring Cloud. Há suporte para o Spring boot 2,0 e o 2,1. As combinações de Spring boot e Spring Cloud com suporte estão listadas na tabela abaixo.

Versão do Spring boot | Versão da Spring Cloud
---|---
2.0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

Verifique se o arquivo `pom.xml` tem as dependências Spring boot e Spring Cloud com base em sua versão.

### <a name="version-20"></a>Versão 2,0:

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

### <a name="version-21"></a>Versão 2,1:

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

O Azure Spring Cloud hospeda e gerencia os componentes Spring Cloud para você, como o registro do serviço Spring Cloud e o servidor de configuração do Spring Cloud. Inclua a biblioteca de cliente do Azure Spring Cloud em suas dependências para permitir a comunicação com sua instância do serviço de nuvem do Azure Spring.

A tabela a seguir lista as versões corretas para seu aplicativo Spring boot/Spring Cloud.

Versão do Spring boot | Versão da Spring Cloud | Versão do Azure Spring Cloud
---|---|---
2.0. x | Finchley. RELEASE | 2.0. x
2.1. x | Greenwich. RELEASE | 2.1. x

Inclua um dos trechos de código abaixo em seu `pom.xml`.  Selecione o trecho cuja versão corresponde a sua.

### <a name="version-20x"></a>Versão 2.0. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Versão 2.1. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Isso garantirá que seu aplicativo seja configurado corretamente com cada componente.  

### <a name="service-registry"></a>Registro de serviço

Para usar o serviço gerenciado do registro de serviço do Azure, inclua `spring-cloud-starter-netflix-eureka-client` em `POM.xml`, conforme mostrado abaixo.

O ponto de extremidade do servidor de registro de serviço será injetado automaticamente como variáveis de ambiente com seu aplicativo. Os aplicativos poderão se registrar com o servidor de registro de serviço e descobrir outros microserviços dependentes.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Configuração distribuída

Para habilitar a configuração distribuída, inclua `spring-cloud-config-client` na seção dependências do seu `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração de inicialização, pois ela impedirá que o aplicativo funcione com o servidor de configuração.

### <a name="metrics"></a>Métricas

Inclua `spring-boot-starter-actuator` na seção dependências do seu pom. xml. As métricas serão retiradas periodicamente dos pontos de extremidade JMX e podem ser visualizadas usando o portal do Azure.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Rastreamento distribuído

Inclua `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` na seção dependências de seu pom. xml, como mostrado abaixo. Além disso, você precisa habilitar uma instância do Azure App insights para trabalhar com sua instância do serviço de nuvem do Azure Spring. Leia mais sobre como habilitar o app insights com o Azure Spring Cloud [aqui](spring-cloud-tutorial-distributed-tracing.md)

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a configurar seu aplicativo Java Spring para implantação no Azure Spring Cloud.  Para saber como habilitar o servidor de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar o servidor de configuração](spring-cloud-tutorial-config-server.md).

