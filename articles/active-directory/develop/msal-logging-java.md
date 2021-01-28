---
title: Registar erros e exceções no MSAL para Java
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções no MSAL para Java
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954923"
---
# <a name="logging-in-msal-for-java"></a>Registos no MSAL para Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL para registo de java

O MSAL para Java permite-lhe utilizar a biblioteca de registos que já está a utilizar com a sua aplicação, desde que seja compatível com o SLF4J. O MSAL para Java utiliza a [fachada simples de madeira para Java](http://www.slf4j.org/) (SLF4J) como uma fachada simples ou abstração para várias estruturas de registo, tais como [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) e [Log4j](https://logging.apache.org/log4j/2.x/). O SLF4J permite ao utilizador ligar a estrutura de registo desejada no momento de implementação.

Por exemplo, para utilizar o Logback como estrutura de registo na sua aplicação, adicione a dependência de Logback ao ficheiro Maven pom para a sua aplicação:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Em seguida, adicione o ficheiro de configuração do Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

O SLF4J liga-se automaticamente ao Logback no momento da implementação. Os registos MSAL serão escritos na consola.

Para obter instruções sobre como se ligar a outras estruturas de registo, consulte o [manual SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informações pessoais e de organização

Por predefinição, o registo msal não captura ou regista quaisquer dados pessoais ou organizacionais. No exemplo seguinte, o registo de dados pessoais ou organizacionais está desligado por padrão:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Ligue o registo de dados pessoais e organizacionais definindo `logPii()` o construtor de aplicações do cliente. Se ligar o registo de dados pessoais ou organizacionais, a sua aplicação deve assumir a responsabilidade de lidar com dados altamente sensíveis e cumprir quaisquer requisitos regulamentares.

No exemplo seguinte, é ativado o registo de dados pessoais ou organizacionais:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft.](sample-v2-code.md)