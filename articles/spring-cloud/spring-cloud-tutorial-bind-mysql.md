---
title: Tutorial - Como ligar uma base de dados Azure para a instância MySQL à sua aplicação Azure Spring Cloud
description: Este tutorial irá mostrar-lhe como ligar uma Base de Dados Azure para a instância MySQL à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277547"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Tutorial: Ligue uma base de dados Azure para a instância MySQL à sua aplicação Azure Spring Cloud 

Com a Nuvem de Mola Azure, pode ligar automaticamente os serviços Azure às suas aplicações, em vez de ter de configurar manualmente a sua aplicação Spring Boot. Este tutorial mostra-lhe como ligar a sua aplicação à sua base de dados Azure para a instância MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância de Nuvem de primavera Azure implantada
* Uma base de dados azure para a conta MySQL
* CLI do Azure

Se não tiver uma instância de Nuvem de primavera Azure implantada, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart-launch-app-portal.md) para implementar a sua primeira aplicação Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Ligue a sua aplicação à sua Base de Dados Azure para a instância MySQL

1. Note o nome de utilizador do administrador e a palavra-passe da sua base de dados Azure para a conta MySQL. 

1. Ligue-se ao servidor, crie uma base de dados chamada **testdb** a partir de um cliente MySQL e, em seguida, crie uma nova conta não-administradora.

1. No ficheiro *pom.xml* do seu projeto, adicione a seguinte dependência:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. No ficheiro *application.properties,* `spring.datasource.*` remova quaisquer propriedades.

1. Atualize a implementação atual executando `az spring-cloud app update`, ou `az spring-cloud app deployment create`crie uma nova implementação para esta mudança executando .  Estes comandos atualizam ou criam a aplicação com a nova dependência.

1. No portal Azure, na sua página de serviço **Azure Spring Cloud,** procure o Painel de **Aplicações,** e, em seguida, selecione a aplicação para se ligar à sua Base de Dados Azure para a instância MySQL.  Esta é a mesma aplicação que atualizou ou implementou no passo anterior. 

1. Selecione **a ligação do serviço**e, em seguida, selecione o botão de **encadernação** do serviço Criar. 

1. Preencha o formulário, selecionando **o Azure MySQL** como **o tipo de Encadernação,** utilizando o mesmo nome de base de dados que utilizou anteriormente, e utilizando o mesmo nome de utilizador e palavra-passe que observou no primeiro passo.

1. Reinicie a aplicação, e esta ligação deve agora funcionar.

1. Para garantir que a ligação do serviço está correta, selecione o nome de ligação e verifique os seus detalhes. O `property` campo deve ser assim:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar a sua aplicação Azure Spring Cloud a uma base de dados Azure para a instância MySQL.  Para saber mais sobre a gestão do seu serviço Azure Spring Cloud, consulte o artigo sobre a descoberta e registo de serviços.

> [!div class="nextstepaction"]
> [Ativar a descoberta e o registo do serviço através do Registo de Serviço sinuoso da Nuvem de primavera](spring-cloud-service-registration.md)
