---
title: Utilizar um Open Service Broker para a base de dados do Azure com uma aplicação no Cloud Foundry da Pivotal
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Explica como configurar uma aplicação da Cloud Foundry da Pivotal para utilizar um Open Service Broker para a base de dados do Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, abra o Service Broker, o Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197220"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Tutorial: Utilizar um Open Service Broker para a base de dados do Azure com uma aplicação no Cloud Foundry da Pivotal

Utilizar o Open Service Broker para o Azure com uma instância de Cloud Foundry da Pivotal permite-lhe a serviços de aprovisionamento, como bases de dados, no Azure diretamente a partir da Cloud Foundry CLI e sua instância de Cloud Foundry da Pivotal. Também é possível vincular esses serviços para uma aplicação em execução na sua instância de Cloud Foundry da Pivotal. Quando ligar uma aplicação a um serviço dessa maneira, não é necessário que atualizar qualquer código ou configuração dentro de seu aplicativo. Este artigo explica como utilizar um Open Service Broker para o serviço do Azure para uma base de dados com uma aplicação na Cloud Foundry da Pivotal.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o seu espaço de aplicativos no Cloud Foundry da Pivotal.
> * Clone uma origem de aplicação de exemplo do GitHub.
> * Prepare o aplicativo para implantação.
> * Implemente a aplicação.
> * Crie uma base de dados com o Open Service Broker para o Azure.
> * Ligar a base de dados à sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder continuar, tem de:

* [Cloud Foundry da Pivotal instalou e configurou](create-cloud-foundry-on-azure.md)
* [Tem o Open Service Broker para Azure instalado e configurado](https://network.pivotal.io/products/azure-open-service-broker-pcf) com a instância de Cloud Foundry

Eis um exemplo do ecrã Pivotal Cloud Foundry Ops Manager com o Open Service Broker para o mosaico do Azure instalada e configurada:

![Cloud Foundry da Pivotal com o Open Service Broker for Azure instalado](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Preparar o seu espaço de aplicativos no Cloud Foundry da Pivotal

Para implementar a aplicação à sua instância de Cloud Foundry da Pivotal, tem de ter sessão iniciada com o `cf` ferramenta da linha de comandos. Também tem de ter sua organização desejada e o espaço de destino.

Para verificar que tem sessão iniciada no e exibir o espaço que Objetiva, utilize `cf target`. O exemplo abaixo mostra o utilizador já iniciado sessão como *administrador*, utilizando o *myorg* organização e a filtragem o *dev* espaço:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Para iniciar sessão, utilize `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Para criar uma nova organização e o espaço, utilize `cf create-org` e `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Para um espaço de destino, utilize `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Obter o código da aplicação

Para uma aplicação utilizar o Open Service Broker para o Azure, o aplicativo precisa utilizar como origem de dados, como uma base de dados. Neste artigo, utilizaremos o [da Primavera de aplicativo de exemplo de música da Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar uma aplicação que utiliza uma origem de dados.

Clonar a aplicação a partir do GitHub e navegue para o seu diretório:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Para ver as origens de dados para esta aplicação, abra a [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) ficheiro.


## <a name="prepare-the-application-for-deployment"></a>Preparar o aplicativo para implantação

Antes de poder implementar a aplicação à sua instância de Cloud Foundry da Pivotal, terá de criá-lo. Para finalidade demonstrativa, estamos também a ativar a algumas *depurar* registo que registrará em log as informações de ligação de origem de dados.

Para ativar a *depurar* log para detalhes de ligação de base de dados, adicionar o abaixo yaml de propriedade ao final do *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

O aplicativo de exemplo utiliza o gradle para criar o aplicativo num Pote executável de Spring Boot. O executável jar será implementada em sua instância de Cloud Foundry da Pivotal. Para criar a aplicação:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Implementar a sua aplicação

Utilize o `cf push` comando para implementar a aplicação à sua instância de Cloud Foundry da Pivotal:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Copie o valor de *rotas* apresentada nas fora do `cf push`. A rota é o URL que irá utilizar para aceder à aplicação em execução. Por exemplo:

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Assim que a aplicação é implementada, pode ver registos da aplicação para ver o URL de ligação utilizado pela aplicação. O comando abaixo apresenta registos da aplicação e usa `grep` para procurar o *jdbcUrl* configuração.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Observe que o aplicativo está usando *h2:mem:testdb*, que é a base de dados na memória. Uma aplicação Spring é automaticamente configurada para utilizar uma base de dados na memória, se uma dependência de banco de dados na memória estiver no classpath e [a configuração automática](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) está ativada. O aplicativo de exemplo tem o [dependência de banco de dados na memória de h2 configurada](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) e a configuração automática ativada na [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Utilize a rota do aplicativo para navegar até ele num browser. A rota ou a URL, é apresentada no resultado do `cf push` comando.

> [!TIP]
> Também pode apresentar o URL da aplicação através da execução `cf apps`.

Depois de navegar para a aplicação utilizando o seu browser, interagir com ela, a eliminação de alguns existente álbuns e criar algumas novas. O aplicativo de exemplo está a utilizar a base de dados na memória para guardar as alterações. Também observará o aplicativo tiver sido preenchido com algumas [predefinido dados](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Aplicação de música Spring com dados padrão](media/music-app.png)

Uma vez que a sua aplicação está a utilizar uma base de dados na memória, as suas alterações não serão incluídas se seu aplicativo for reiniciado ou implantados novamente. Para mostrar que as alterações não são mantidas, depois de efetuar algumas alterações, restage a sua aplicação utilizando `cf restage`:

```cmd
cf restage spring-music
```

Depois do aplicativo tiver sido gurado, aceda ao mesmo num browser utilizando o mesmo URL. Observe que as alterações que efetuou foram removidas e os dados predefinidos são apresentados.

![Aplicação de música Spring com dados padrão](media/music-app.png)

## <a name="create-a-database"></a>Criar uma base de dados

Para criar uma base de dados persistente no Azure através do Open Service Broker, utilize o `cf create-service` comando. O comando abaixo Aprovisiona uma base de dados PostgreSQL no Azure no grupo de recursos *MyResourceGroup* no *eastus* região. Obter mais informações sobre *resourceGroup*, *localização*, e outros parâmetros JSON de específicos do Azure está disponível na [documentação de referência do módulo PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

A base de dados é exposto na sua instância de Cloud Foundry da Pivotal como um serviço com o nome *mypgsql*. Assim que a sua base de dados foi concluída de aprovisionamento, o que deve demorar alguns minutos, pode vinculá-lo seu aplicativo. Para verificar a sua base de dados foi concluída de aprovisionamento, utilize o `cf services` comando:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

O *última operação* valor para seu serviço será *criada com êxito* quando tiver concluído o aprovisionamento.

## <a name="bind-the-database-to-your-application"></a>Ligar a base de dados à sua aplicação

Utilize o `bind-service` comando para vincular o serviço a sua aplicação.

```cmd
cf bind-service spring-music mypgsql
```

Após vincular o serviço para a sua aplicação, terá de restage o aplicativo para que as alterações entrem em vigor.

```cmd
cf restage spring-music
```

A aplicação está [configurado para utilizar o Spring Cloud conectores](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), que permite que sua instância de Cloud Foundry da Pivotal para usar [reconfiguração automática](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) na origem de dados da sua aplicação. Neste caso, sua instância de Cloud Foundry da Pivotal será automaticamente reconfigurar a sua aplicação para utilizar um serviço seu vinculados a para uma origem de dados quando seu aplicativo é gurado.

Depois de seu aplicativo tiver sido gurado, ele usará *mypgsql* para armazenar dados em vez da base de dados na memória.

As alterações que fizer agora irá ser persistente em reinícios e reimplementa. Também pode ver que o URL de ligação a aplicação está a utilizar ao visualizar os registos da aplicação novamente.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Observe que existem duas entradas:

* O valor original do *h2:mem:testdb*.
* O valor atualizado para a base de dados do PostgreSQL quando seu aplicativo foi gurado.

Para verificar os dados são a ser mantidos na base de dados do PostgreSQL, navegue para a aplicação no seu browser, fazer algumas alterações, em seguida, restage seu aplicativo:

```cmd
cf restage spring-music
```

Depois do aplicativo tiver sido gurado, aceda ao mesmo num browser utilizando o mesmo URL. Tenha em atenção as alterações que efetuou ainda estão ali.

## <a name="cleanup"></a>Limpeza

Se quiser desligar a aplicação da base de dados, pode desvinculá-lo usando o `cf unbind-service` comando.

```cmd
cf unbind-service spring-music mypgsql
```

Semelhante a ligar a sua aplicação para um serviço, tem de restage seu aplicativo para que essas alterações entrem em vigor. Esta ação irá deixe ambas *mypgsql* e a sua aplicação intacta, mas a sua aplicação começar a utilizar a base de dados na memória, em vez de *mypgsql*.

Para eliminar a base de dados, pode usar o `cf delete-service` comando. *Não é possível anular esta ação, por isso, não se esqueça de que pretende eliminar a base de dados antes de continuar.*

```cmd
cf delete-service mypgsql
```

Para remover o seu aplicativo de sua instância de Cloud Foundry da Pivotal:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Passos Seguintes

Este tutorial abordados implementar uma aplicação no Cloud Foundry da Pivotal, bem como a criação de uma base de dados com o Open Service Broker para o Azure. Ele também trata de sua base de dados de ligação para a aplicação em sua instância de Cloud Foundry da Pivotal. Para obter mais informações sobre como implementar aplicações em Cloud Foundry no Azure, consulte:

* [Cloud Foundry no Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Implementar a sua primeira aplicação em Cloud Foundry no Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)