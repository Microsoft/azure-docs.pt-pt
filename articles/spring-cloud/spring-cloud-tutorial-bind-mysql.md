---
title: Como associar o banco de dados do Azure para MySQL ao seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Este artigo mostrará como associar o Azure MySQL ao seu aplicativo Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607592"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Tutorial: associar os serviços do Azure ao seu aplicativo Azure Spring Cloud: banco de dados do Azure para MySQL

O Azure Spring Cloud permite associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente o aplicativo Spring boot. Este tutorial mostrará como associar seu aplicativo ao MySQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma conta do banco de dados do Azure para MySQL
* CLI do Azure

Se você não tiver uma instância do Azure Spring Cloud implantada, siga as etapas neste guia de [início rápido](spring-cloud-quickstart-launch-app-portal.md) para implantar seu primeiro aplicativo Spring Cloud.

## <a name="bind-azure-database-for-mysql"></a>Associar banco de dados do Azure para MySQL

1. Anote o nome de usuário do administrador e a senha de sua conta do Azure MySQL. Conecte-se ao servidor e crie um banco de dados chamado `testdb` de um cliente MySQL. Crie uma nova conta que não seja de administrador.

1. Adicione a seguinte dependência na `pom.xml` do seu projeto

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Remova as propriedades `spring.datasource.*`, se houver, no arquivo `application.properties`.

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma nova implantação para essa alteração usando `az spring-cloud app deployment create`.  Esses comandos atualizarão ou criarão o aplicativo com a nova dependência.

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Localize o **painel do aplicativo** e selecione o aplicativo a ser associado ao MySQL do Azure.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Em seguida, selecione `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, não se esqueça de selecionar o **tipo de associação** `Azure MySQL`, o mesmo nome de banco de dados usado anteriormente e o mesmo nome de usuário e senha que você anotou na primeira etapa.

1. Reinicie o aplicativo e essa associação agora deve funcionar.

1. Para garantir que a associação de serviço esteja correta, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ter a seguinte aparência:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure a um banco de BD MySQL.  Para saber mais sobre como gerenciar seu serviço de nuvem do Azure Spring, continue lendo para saber mais sobre a descoberta e o registro de serviços.

> [!div class="nextstepaction"]
> [Saiba como habilitar os registros e a descoberta de serviços usando o registro do serviço Spring Cloud](spring-cloud-service-registration.md).

