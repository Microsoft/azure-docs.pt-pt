---
title: Como ligar uma base de dados Azure para o caso MySQL à sua aplicação Azure Spring Cloud
description: Saiba como ligar uma Base de Dados Azure para o caso MySQL à sua aplicação Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878369"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Ligue uma base de dados Azure para a sua aplicação Azure Spring Cloud 

**Este artigo aplica-se a:** ✔️ Java

Com a Azure Spring Cloud, pode ligar os serviços Azure às suas aplicações automaticamente, em vez de ter de configurar manualmente a sua aplicação Boot Spring Boot. Este artigo mostra-lhe como ligar a sua aplicação à sua Base de Dados Azure para o caso MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* Um exemplo de Nuvem de primavera de Azure implantado
* Uma base de dados Azure para a conta MySQL
* CLI do Azure

Se não tiver uma instância Azure Spring Cloud implantada, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart.md) para implementar a sua primeira aplicação Cloud spring.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Ligue a sua aplicação à sua Base de Dados Azure para o caso MySQL

1. Note o nome de utilizador e a palavra-passe da sua Base de Dados Azure para a conta MySQL. 

1. Ligue-se ao servidor, crie uma base de dados chamada **testdb** a partir de um cliente MySQL e, em seguida, crie uma nova conta não administrada.

1. No ficheiro *pom.xml* do seu projeto, adicione a seguinte dependência:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. No ficheiro *application.properties,* remova quaisquer `spring.datasource.*` propriedades.

1. Atualize a atual implementação executando `az spring-cloud app update` , ou crie uma nova implementação para esta mudança executando `az spring-cloud app deployment create` .  Estes comandos atualizam ou criam a aplicação com a nova dependência.

1. No portal Azure, na sua página de serviço **Azure Spring Cloud,** procure o **Painel de Aplicação** e, em seguida, selecione a aplicação para ligar à sua Base de Dados Azure para a sua instância MySQL.  Esta é a mesma aplicação que atualizou ou implementou no passo anterior. 

1. Selecione **a ligação de serviço** e, em seguida, selecione o botão de **ligação de serviço Criar.** 

1. Preencha o formulário, selecionando **O Azure MySQL** como o **tipo de Ligação,** utilizando o mesmo nome de base de dados que usou anteriormente, e utilizando o mesmo nome de utilizador e palavra-passe que observou no primeiro passo.

1. Reinicie a aplicação, e esta ligação deve agora funcionar.

1. Para garantir que a ligação de serviço está correta, selecione o nome de encadernação e, em seguida, verifique os seus detalhes. O `property` campo deve ser assim:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar uma aplicação Azure Spring Cloud a uma Base de Dados Azure para o caso MySQL. Para saber mais sobre serviços vinculativos a uma aplicação, consulte [uma base de dados DB da Azure Cosmos para uma aplicação Azure Spring Cloud](spring-cloud-howto-bind-cosmos.md).
