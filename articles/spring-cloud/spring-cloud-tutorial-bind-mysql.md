---
title: Como associar o banco de dados do Azure para MySQL ao seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Este artigo mostrará como associar o Azure MySQL ao seu aplicativo Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039022"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Tutorial: Associe os serviços do Azure ao seu aplicativo Azure Spring Cloud: Base de Dados do Azure para MySQL

O Azure Spring Cloud permite associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente o aplicativo Spring boot. Este tutorial mostrará como associar seu aplicativo ao MySQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma conta do banco de dados do Azure para MySQL
* CLI do Azure

Se necessário, instale a extensão do Azure Spring Cloud para o CLI do Azure usando o seguinte comando:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

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

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Localize o **painel do aplicativo** e selecione o aplicativo a ser associado ao MySQL do Azure.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Em seguida, selecione `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, certifique-se de selecionar o **tipo de associação** `Azure MySQL`, o mesmo nome de banco de dados usado anteriormente e o mesmo nome de usuário e senha que você anotou na primeira etapa.

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

