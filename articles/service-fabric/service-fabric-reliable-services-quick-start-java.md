---
title: Crie seu primeiro serviço confiável em Java
description: Introdução à criação de um aplicativo Microsoft Azure Service Fabric com serviços com e sem estado.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614218"
---
# <a name="get-started-with-reliable-services-in-java"></a>Introdução ao Reliable Services em Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica as noções básicas do Azure Service Fabric Reliable Services e orienta você pela criação e implantação de um simples aplicativo de serviço confiável escrito em Java. 

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se você tem o ambiente de desenvolvimento Service Fabric configurado em seu computador.
Se você precisar configurá-lo, acesse [introdução ao Mac](service-fabric-get-started-mac.md) ou [introdução no Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar o Reliable Services, você só precisa entender alguns conceitos básicos:

* **Tipo de serviço**: esta é a implementação do seu serviço. Ele é definido pela classe que você escreve que estende `StatelessService` e quaisquer outros códigos ou dependências usados nele, juntamente com um nome e um número de versão.
* **Instância de serviço nomeada**: para executar o serviço, você cria instâncias nomeadas do seu tipo de serviço, de forma semelhante a criar instâncias de objeto de um tipo de classe. As instâncias de serviço são, na verdade, instanciações de objeto de sua classe de serviço que você escreve.
* **Host de serviço**: as instâncias de serviço nomeadas que você cria precisam ser executadas dentro de um host. O host de serviço é apenas um processo em que as instâncias do serviço podem ser executadas.
* **Registro de serviço**: o registro reúne tudo juntos. O tipo de serviço deve ser registrado com o tempo de execução de Service Fabric em um host de serviço para permitir que Service Fabric criem instâncias dele para serem executados.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Comece criando um aplicativo Service Fabric. O SDK do Service Fabric para Linux inclui um gerador de Yeoman para fornecer o scaffolding para um aplicativo Service Fabric com um serviço sem estado. Comece executando o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **serviço confiável sem estado**. Para este tutorial, nomeie o aplicativo "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui diretórios para o `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Registro de serviço
Os tipos de serviço devem ser registrados com o tempo de execução Service Fabric. O tipo de serviço é definido no `ServiceManifest.xml` e sua classe de serviço que implementa `StatelessService`. O registro de serviço é executado no ponto de entrada principal do processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementar o serviço

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService. java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada aberto, chamado `runAsync()`, no qual você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa execução.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Um ponto de entrada de comunicação no qual você pode conectar sua pilha de comunicação de sua escolha. É aí que você pode começar a receber solicitações de usuários e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Este tutorial se concentra no `runAsync()` método de ponto de entrada. É aí que você pode iniciar imediatamente a execução do código.

### <a name="runasync"></a>RunAsync
A plataforma chama esse método quando uma instância de um serviço é colocada e está pronta para ser executada. Para um serviço sem estado, isso significa que quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço precisa ser fechada. Em Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer muitas vezes durante o tempo de vida do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para o balanceamento de recursos.
* As falhas ocorrem em seu código.
* O aplicativo ou o sistema é atualizado.
* O hardware subjacente passa por uma interrupção.

Essa orquestração é gerenciada pelo Service Fabric para manter seu serviço altamente disponível e equilibrado corretamente.

`runAsync()` não deve bloquear de forma síncrona. Sua implementação de runAsync deve retornar um CompletableFuture para permitir que o tempo de execução continue. Se sua carga de trabalho precisar implementar uma tarefa de execução longa que deve ser feita dentro do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento de sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguarda que a tarefa seja encerrada (com êxito na conclusão, cancelamento ou falha) antes de se mover. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `runAsync()` o mais rápido possível quando o sistema solicita o cancelamento. O exemplo a seguir demonstra como manipular um evento de cancelamento:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor armazenado só existe para o ciclo de vida atual de sua instância de serviço. Quando o serviço é movido ou reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
Service Fabric introduz um novo tipo de serviço com estado. Um serviço com estado pode manter o estado de forma confiável no próprio serviço, localizado em conjunto com o código que o está usando. O estado se torna altamente disponível por Service Fabric sem a necessidade de persistir o estado para um repositório externo.

Para converter um valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço é movido ou reiniciado, você precisa de um serviço com estado.

No mesmo diretório que o aplicativo HelloWorld, você pode adicionar um novo serviço executando o comando `yo azuresfjava:AddService`. Escolha o "serviço com estado confiável" para sua estrutura e nomeie o serviço "HelloWorldStateful". 

Seu aplicativo agora deve ter dois serviços: o serviço sem estado HelloWorld e o serviço com estado HelloWorldStateful.

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de um provedor de estado que pode armazenar o estado de forma confiável. Service Fabric vem com uma implementação de provedor de estado chamada coleções confiáveis, que permite criar estruturas de dados replicadas por meio do Gerenciador de estado confiável. Um serviço confiável com estado usa esse provedor de estado por padrão.

Abra HelloWorldStateful. Java em **HelloWorldStateful-> src**, que contém o seguinte método RunAsync:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` funciona de forma semelhante em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa um trabalho adicional em seu nome antes de executar `RunAsync()`. Esse trabalho pode incluir garantir que o Gerenciador de estado confiável e as coleções confiáveis estejam prontos para uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções confiáveis e o Gerenciador de estado confiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) é uma implementação de dicionário que você pode usar para armazenar o estado de forma confiável no serviço. Com Service Fabric e HashMaps confiáveis, você pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. HashMaps confiáveis tornam seus dados altamente disponíveis. Service Fabric realiza isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e suas transições de estado.

As coleções confiáveis podem armazenar qualquer tipo de Java, incluindo seus tipos personalizados, com algumas limitações:

* Service Fabric torna seu estado altamente disponível ao *replicar* o estado entre os nós e o HashMap confiável armazena seus dados no disco local em cada réplica. Isso significa que tudo que é armazenado em HashMaps confiáveis deve ser *serializável*. 
* Os objetos são replicados para alta disponibilidade quando você confirma transações em HashMaps confiáveis. Os objetos armazenados em HashMaps confiáveis são mantidos na memória local em seu serviço. Isso significa que você tem uma referência local para o objeto.
  
   É importante que você não faça mutação de instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso ocorre porque as alterações em instâncias locais de objetos não serão replicadas automaticamente. Você deve reinserir o objeto de volta no dicionário ou usar um dos métodos de *atualização* no dicionário.

O Gerenciador de estado confiável gerencia HashMaps confiáveis para você. Você pode pedir ao Gerenciador de estado confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar em seu serviço. O Gerenciador de estado confiável garante que você obtenha uma referência de volta. Não recomendamos que você salve referências a instâncias de coleções confiáveis em Propriedades ou variáveis de membro de classe. Deve-se tomar cuidado especial para garantir que a referência seja definida para uma instância em todos os momentos no ciclo de vida do serviço. O Gerenciador de estado confiável manipula esse trabalho para você e é otimizado para visitas repetidas.


### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

As operações em HashMaps confiáveis são assíncronas. Isso ocorre porque as operações de gravação com coleções confiáveis executam operações de e/s para replicar e manter os dados no disco.

As operações confiáveis do HashMap são *transacionais*, para que você possa manter o estado consistente entre várias HashMaps e operações confiáveis. Por exemplo, você pode obter um item de trabalho de um dicionário confiável, executar uma operação nele e salvar o resultado em outro HashMap confiável, tudo em uma única transação. Isso é tratado como uma operação atômica e garante que toda a operação seja realizada com sucesso ou que toda a operação seja revertida. Se ocorrer um erro depois que você remover a fila do item, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.


## <a name="build-the-application"></a>Criar a aplicação

O Yeoman scaffolding inclui um script gradle para compilar o aplicativo e scripts bash para implantar e remover o aplicativo. Para executar o aplicativo, primeiro compile o aplicativo com gradle:

```bash
$ gradle
```

Isso produz um pacote de aplicativo Service Fabric que pode ser implantado usando Service Fabric CLI.

## <a name="deploy-the-application"></a>Implementar a aplicação

Depois de criada a aplicação, pode implementá-la no cluster local.

1. Ligue ao cluster do Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Execute o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

A implementação da aplicação criada é igual à de qualquer outra aplicação do Service Fabric. Veja a documentação sobre como [gerir uma aplicação do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros desses comandos encontram-se nos manifestos gerados dentro do pacote de aplicação.

Após a implementação da aplicação, abra um browser e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implantar o aplicativo em um cluster do Linux seguro no Azure, você precisa configurar um certificado para validar seu aplicativo com o tempo de execução de Service Fabric. Isso permite que seus serviços de Reliable Services se comuniquem com as APIs de tempo de execução de Service Fabric subjacentes. Para saber mais, confira [configurar um aplicativo Reliable Services para ser executado em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Passos seguintes

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
