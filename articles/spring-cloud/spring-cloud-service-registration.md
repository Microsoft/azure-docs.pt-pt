---
title: Automate o registo e a descoberta do serviço de automatização
description: Saiba automatizar a descoberta e o registo de serviços através do Registo de Serviço sinuoso da Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278864"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Descubra e registe os seus serviços de Nuvem de primavera

Service Discovery é um requisito chave para uma arquitetura baseada em microserviços.  Configurar cada cliente manualmente leva tempo e introduz a possibilidade de erro humano.  O Registo de Serviço sinuoso da Nuvem de primavera de Azure resolve este problema.  Uma vez configurado, um servidor de Registo de Serviço controlará o registo e a descoberta do serviço para os microserviços da sua aplicação. O servidor do Registo de Serviços mantém um registo dos microserviços implantados, permite o equilíbrio de carga do lado do cliente e dissocia os prestadores de serviços dos clientes sem depender do DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registe a sua aplicação através do Registo de Serviço sinuoso da Nuvem de primavera

Antes que a sua aplicação possa gerir o registo e a descoberta do serviço através do Registo de Serviço seleções da Spring Cloud, várias dependências devem ser incluídas no ficheiro *pom.xml* da aplicação.
Inclua dependências para *spring-cloud-starter-netflix-eureka-client* e *spring-cloud-starter-azure-spring-cloud-client* to your *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Atualize a classe de nível superior

Finalmente, adicionamos uma anotação à classe de nível superior da sua aplicação

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

O ponto final do servidor do serviço da Nuvem de primavera será injetado como uma variável ambiental na sua aplicação.  Os microserviços poderão agora registar-se no servidor do Registo de Serviços e descobrir outros microserviços dependentes.

Note que pode demorar alguns minutos para que as alterações se propaguem do servidor para todos os microserviços.
