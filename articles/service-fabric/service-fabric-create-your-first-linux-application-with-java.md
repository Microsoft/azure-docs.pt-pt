---
title: Criar uma aplicação Java Reliable Actors do Azure Service Fabric no Linux | Microsoft Docs
description: Saiba como criar e implementar uma aplicação Java Reliable Actors do Service Fabric em cinco minutos.
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2018
ms.author: ryanwi
ms.openlocfilehash: ddde1570534801d394ace23973e4d755a1af7017
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443220"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Criar a sua primeira aplicação Java Reliable Actors do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este guia de introdução ajuda-o a criar a sua primeira aplicação Java do Azure Service Fabric num ambiente de desenvolvimento do Linux em apenas alguns minutos.  Quando tiver terminado, terá uma aplicação Java de serviço único simples em execução no cluster de desenvolvimento local.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, instale o SDK do Service Fabric, a CLI do Service Fabric, Yeoman, configure o ambiente de desenvolvimento Java e configure um cluster de desenvolvimento no seu [ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [Configurar um ambiente de desenvolvimento no Mac através do Docker](service-fabric-get-started-mac.md).

Além disso, instale a [CLI do Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalar e configurar os geradores para Java
O Service Fabric fornece ferramentas estruturais que irão ajudá-lo a criar uma aplicação Java do Service Fabric a partir do terminal, através do gerador de modelos Yeoman.  Se o Yeoman ainda não estiver instalado, consulte [Introdução ao Service Fabric com o Linux](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) para obter instruções de configuração do Yeoman. Execute o seguinte comando para instalar o gerador de modelo do Service Fabric Yeoman para Java.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar o Reliable Actors, só precisa de compreender alguns conceitos básicos:

* **Serviço de ator**. O Reliable Actors é compactado no Reliable Services que pode ser implementado na infraestrutura do Service Fabric. As instâncias de ator são ativadas numa instância de serviço com nome.
* **Registo de ator**. Tal como acontece com o Reliable Services, um serviço Reliable Actor tem de ser registado com o runtime do Service Fabric. Além disso, o tipo de ator tem de ser registado com o runtime de Ator.
* **Interface de ator**. A interface de ator serve para definir uma interface pública com tipos de dados inflexíveis de um ator. Na terminologia modelo do Reliable Actor, a interface de ator define os tipos de mensagens que o ator pode compreender e processar. A interface de ator é utilizada por outros atores e aplicações cliente para "enviar" (de forma assíncrona) mensagens para o ator. O Reliable Actors pode implementar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é utilizada por aplicações cliente para invocar os métodos expostos através da interface de ator. A classe ActorProxy fornece duas funcionalidades importantes:
  
  * Resolução de nomes: É possível localizar o ator no cluster (localizar o nó do cluster onde está alojado).
  * Processamento de falhas: Ele pode repetir invocações de método e voltar a resolver a localização do ator após, por exemplo, uma falha que exija o ator seja relocalizado para outro nó no cluster.

As seguintes regras que pertencem a interfaces de ator merecem especial referência:

* Os métodos de interfaces de ator não podem ser sobrecarregados.
* Os métodos de interfaces de ator não podem ter parâmetros out, ref ou opcionais.
* As interfaces genéricas não são suportadas.

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric contém um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O gerador instalado na última secção facilita a criação do primeiro serviço e a adição posterior de outros.  Também pode criar, construir e implementar aplicações Java do Service Fabric com um plug-in do Eclipse. Veja [Criar e implementar a sua primeira aplicação Java com o Eclipse](service-fabric-get-started-eclipse.md). Neste guia de introdução, utilize Yeoman para criar uma aplicação com um único serviço, que armazena e obtém um valor do contador.

1. Num terminal, escreva ``yo azuresfjava``.
2. Dê um nome à aplicação.
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para este tutorial, escolha um Serviço Reliable Actor. Para obter mais informações sobre os outros tipos de serviço, veja [Service Fabric programming model overview (Descrição geral do modelo de programação do Service Fabric)](service-fabric-choose-framework.md).
   ![Gerador Yeoman do Service Fabric para Java][sf-yeoman]

Se der à aplicação o nome "HelloWorldActorApplication" e ao ator o nome "HelloWorldActor", é criado o andaime seguinte:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Blocos modulares básicos do Reliable Actors
Os conceitos básicos descritos anteriormente traduzem-se nos blocos modulares básicos de um serviço Reliable Actor.

### <a name="actor-interface"></a>Interface de ator
Contém a definição de interface para o ator. Esta interface define o contrato de ator que é partilhado pela implementação de ator e pelos clientes que chamam o ator, pelo que, normalmente, faz sentido defini-lo num local que esteja separado da implementação de ator e que possa ser partilhado por vários outros serviços ou aplicações cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço de ator
Contém a implementação de ator e o código de registo de ator. A classe de ator implementa a interface de ator. É aqui que seu ator faz o seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registo de ator
O serviço de ator tem de ser registado com um tipo de serviço no runtime do Service Fabric. Para que o Serviço de Ator execute as suas instâncias de ator, o tipo de ator também tem de estar registado no Serviço de Ator. O método de registo `ActorRuntime` realiza este trabalho para atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());
    
public static void main(String[] args) throws Exception {
        
        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal.
As dependências Java do Service Fabric são obtidas do Maven. Para criar e trabalhar nas aplicações Java do Service Fabric, tem de garantir que o JDK e Gradle estão instalados. Se ainda não estiverem instalados, consulte [Introdução ao Service Fabric com o Linux](service-fabric-get-started-linux.md#set-up-java-development) para obter instruções de instalação do JDK e Gradle.

Para criar e empacotar a aplicação, execute o seguinte:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Implementar a aplicação
Depois de criada a aplicação, pode implementá-la no cluster local.

1. Ligue ao cluster do Service Fabric local (o cluster tem de estar [configurado e em execução](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Execute o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

A implementação da aplicação criada é igual à de qualquer outra aplicação do Service Fabric. Veja a documentação sobre como [gerir uma aplicação do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros desses comandos encontram-se nos manifestos gerados dentro do pacote de aplicação.

Após a implementação da aplicação, abra um browser e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implementar a aplicação a um cluster Linux seguro no Azure, terá de configurar um certificado para validar a sua aplicação com o tempo de execução do Service Fabric. Se o fizer, permite que os seus serviços de Reliable Actors comunicar com o tempo de execução do Service Fabric subjacente APIs. Para obter mais informações, consulte [configurar uma aplicação de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Iniciar o cliente de teste e executar uma ativação pós-falha
Os atores não fazem nada sozinhos, precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

> [!Note]
> O cliente de teste utiliza a classe ActorProxy para comunicar com actors, que tem de ser executado no mesmo cluster que o serviço de ator ou partilhar o mesmo espaço de endereços IP.  Pode executar o cliente de teste no mesmo computador que o cluster de desenvolvimento local.  No entanto, para comunicar com atores num cluster remoto, tem de implementar um gateway no cluster que lida com a comunicação externa com os atores.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.  O script de teste chama o método `setCountAsync()` no ator para incrementar um contador, chama o método `getCountAsync()` no ator para obter o valor do novo contador e apresenta esse valor à consola.

   Em caso de MAC OS X, precisa copiar a pasta de HelloWorldTestClient para a algum local dentro do contentor ao executar os seguintes comandos adicionais.    
    
    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3. A réplica do principal de serviço gere as operações de escrita e leitura.  Alterações no estado de serviço são então replicadas para as réplicas secundárias, em execução em nós 0 e 1 da captura de ecrã abaixo.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Em **Nós**, clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia o nó em execução na réplica do principal de serviço e força uma ativação pós-falha numa das réplicas secundárias em execução noutro nó.  Essa réplica secundária é promovida a principal, outra réplica secundária é criada num nó diferente e a réplica primária começa a executar operações de leitura/escrita. Á medida que o nó reinicia, observe a saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## <a name="remove-the-application"></a>Remover a aplicação
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação, anular o registo do pacote de aplicação e remover o pacote de aplicação do arquivo de imagens do cluster.

```bash
./uninstall.sh
```

No explorador do Service Fabric, pode constatar que a aplicação e o tipo da aplicação já não são apresentados no nó **Aplicações**.

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliotecas Java do Service Fabric no Maven
As bibliotecas Java do Service Fabric foram alojadas no Maven. Pode adicionar as dependências no ``pom.xml`` ou ``build.gradle`` dos seus projetos para utilizar bibliotecas Java do Service Fabric a partir de **mavenCentral**. 

### <a name="actors"></a>Atores

Suporte de Reliable Actor do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Serviços

Suporte de Reliable Services do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Outros
#### <a name="transport"></a>Transporte

Suporte da camada de transporte para a aplicação Java do Service Fabric. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço, a não ser que o programe na camada de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Suporte para o Fabric

Suporte ao nível do sistema para o Service Fabric, que comunica com o runtime do Service Fabric nativo. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço. Isto é obtido automaticamente do Maven, quando incluir as outras dependências acima.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>Passos Seguintes

* [Create your first Service Fabric Java application on Linux using Eclipse (Criar a sua primeira aplicação Java do Service Fabric no Linux com o Eclipse)](service-fabric-get-started-eclipse.md)
* [Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric através da CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
