---
title: Automatizar o registro e a descoberta de serviços
description: Saiba como automatizar a descoberta e o registro de serviços usando o registro de serviço do Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038746"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Descobrir e registrar seus serviços de nuvem Spring

A descoberta de serviço é um requisito fundamental para uma arquitetura baseada em microserviço.  Configurar cada cliente manualmente leva tempo e introduz a possibilidade de erro humano.  O registro do serviço de nuvem do Azure Spring resolve esse problema.  Uma vez configurado, um servidor de registro de serviço controlará o registro e a descoberta do serviço para os microserviços do seu aplicativo. O servidor do registro de serviço mantém um registro dos microserviços implantados, habilita o balanceamento de carga do lado do cliente e dissocia os provedores de serviço de clientes sem depender do DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrar seu aplicativo usando o registro de serviço do Spring Cloud

Antes que seu aplicativo possa gerenciar o registro e a descoberta de serviços usando o registro de serviço do Spring Cloud, várias dependências devem ser incluídas no arquivo *pom. xml* do aplicativo.

Para começar, adicione um repositório de instantâneos à seção *Repository* do seu *pom. xml*

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Incluir dependências

Em seguida, incluímos dependências para *Spring-Cloud-Starter-Netflix-Eureka-Client* e *Spring-Cloud-Starter-Azure-Spring-Cloud-Client* ao seu *pom. xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Atualizar a classe de nível superior

Por fim, adicionamos uma anotação à classe de nível superior do seu aplicativo

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

O ponto de extremidade do servidor do registro do serviço de nuvem Spring será injetado como uma variável de ambiente em seu aplicativo.  Os microserviços agora serão capazes de se registrar no servidor do registro de serviço e descobrir outros microserviços dependentes.

Observe que pode levar alguns minutos para que as alterações sejam propagadas do servidor para todos os microserviços.
