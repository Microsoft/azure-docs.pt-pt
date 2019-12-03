---
title: Tutorial-como associar uma instância do banco de dados do Azure para MySQL ao seu aplicativo do Azure Spring Cloud
description: Este tutorial mostrará como associar uma instância do banco de dados do Azure para MySQL ao seu aplicativo de nuvem Spring do Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: a9911798e42db55d5aaae90c933cfb64945b244c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708816"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Tutorial: associar uma instância do banco de dados do Azure para MySQL ao seu aplicativo do Azure Spring Cloud 

Com o Azure Spring Cloud, você pode associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de ter que configurar seu aplicativo Spring boot manualmente. Este tutorial mostra como associar seu aplicativo à instância do banco de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma conta do banco de dados do Azure para MySQL
* CLI do Azure

Se você não tiver uma instância do Azure Spring Cloud implantada, siga as instruções em [início rápido: iniciar um aplicativo do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md) para implantar seu primeiro aplicativo Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Associar seu aplicativo à instância do banco de dados do Azure para MySQL

1. Anote o nome de usuário do administrador e a senha de sua conta do banco de dados do Azure para MySQL. 

1. Conecte-se ao servidor, crie um banco de dados chamado **TestDB** de um cliente MySQL e, em seguida, crie uma nova conta que não seja de administrador.

1. No arquivo *pom. xml* do seu projeto, adicione a seguinte dependência:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. No arquivo *Application. Properties* , remova todas as propriedades de `spring.datasource.*`.

1. Atualize a implantação atual executando `az spring-cloud app update`ou crie uma nova implantação para essa alteração executando `az spring-cloud app deployment create`.  Esses comandos atualizam ou criam o aplicativo com a nova dependência.

1. Na portal do Azure, na página do serviço de **nuvem do Azure Spring** , procure o **painel do aplicativo**e, em seguida, selecione o aplicativo a ser associado à instância do banco de dados do Azure para MySQL.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. 

1. Selecione **Associação de serviço**e, em seguida, selecione o botão **criar Associação de serviço** . 

1. Preencha o formulário, selecionando **Azure MySQL** como o **tipo de associação**, usando o mesmo nome de banco de dados usado anteriormente e usando o mesmo nome de usuário e senha que você anotou na primeira etapa.

1. Reinicie o aplicativo e essa associação agora deve funcionar.

1. Para garantir que a associação de serviço esteja correta, selecione o nome da associação e, em seguida, verifique seus detalhes. O campo `property` deve ter a seguinte aparência:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure a uma instância do banco de dados do Azure para MySQL.  Para saber mais sobre como gerenciar seu serviço de nuvem do Azure Spring, confira o artigo sobre descoberta e registro de serviços.

> [!div class="nextstepaction"]
> [Habilitar o registro e a descoberta de serviços usando o registro do serviço Spring Cloud](spring-cloud-service-registration.md)
