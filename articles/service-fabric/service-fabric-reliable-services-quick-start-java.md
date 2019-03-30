---
title: Criar seu primeiro serviço fiável do Azure Service Fabric em Java | Documentos da Microsoft
description: Introdução à criação de um aplicativo do Microsoft Azure Service Fabric com os serviços com e sem estado.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 6bf8c632a7513d018745bc74aa0a1db95a39af8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663198"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica as noções básicas do Azure Service Fabric Reliable Services e explica como criar e implementar uma aplicação de Reliable Service simple escrita em Java. 

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric configurar no seu computador.
Se precisar de configurá-lo, aceda a [de introdução no Mac](service-fabric-get-started-mac.md) ou [introdução no Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar com o Reliable Services, só precisa de compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a sua implementação de serviço. Ele é definido pela classe que escreve que expande `StatelessService` e qualquer outro código ou dependências utilizadas nele, juntamente com um nome e um número de versão.
* **Instância de serviço com o nome**: Para executar seu serviço, vai criar instâncias nomeadas do tipo de serviço, bem como criar instâncias de objeto de um tipo de classe. Instâncias de serviço são, na verdade, instanciações de objeto da sua classe de serviço que escreve.
* **Anfitrião do serviço**: As instâncias de serviço com nome que cria tem de ser executado dentro de um anfitrião. O anfitrião do serviço é apenas um processo em que as instâncias do seu serviço podem executar.
* **Registo do serviço**: Registo reúne tudo. O tipo de serviço tem de ser registado com o runtime do Service Fabric num host de serviço para permitir que o Service Fabric criar instâncias do mesmo para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Comece por criar uma aplicação do Service Fabric. O SDK do Service Fabric para Linux inclui um Yeoman gerador para fornecer o scaffolding para uma aplicação do Service Fabric com um serviço sem estado. Inicie o executando o Yeoman seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar uma **Reliable Services sem monitorização de estado**. Para este tutorial, dê o nome "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui os diretórios para o `HelloWorldApplication` e `HelloWorld`.

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
### <a name="service-registration"></a>Registo do serviço
Tipos de serviço tem de ser registados com o tempo de execução do Service Fabric. O tipo de serviço é definido no `ServiceManifest.xml` e sua classe de serviço que implementa `StatelessService`. Registo do serviço é executado no ponto de entrada principal de processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

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

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para o código:

* Um método de ponto de entrada aberta, chamado `runAsync()`, onde pode começar a executar quaisquer cargas de trabalho, incluindo cargas de trabalho de computação de execução longa.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Um ponto de entrada de comunicação onde é possível conectar sua pilha de comunicação preferencial. Isso é onde pode começar a receber pedidos de utilizadores e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Este tutorial concentra-se no `runAsync()` método de ponto de entrada. Isso é onde pode começar a execução do seu código.

### <a name="runasync"></a>RunAsync
A plataforma chama esse método quando uma instância de um serviço é colocá-la e pronto para ser executado. Para um serviço sem estado, o que significa quando a instância do serviço é aberta. É fornecido um token de cancelamento para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo aberto/fechamento de uma instância de serviço pode ocorrer, como um todo, muitas vezes ao longo do tempo de vida do serviço. Isto pode acontecer por várias razões, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recurso.
* As falhas ocorrem no seu código.
* O aplicativo ou o sistema está atualizado.
* O hardware subjacente sofre um período de indisponibilidade.

Esta orquestração é gerenciada pelo Service Fabric para manter o seu serviço de elevada disponibilidade e corretamente equilibrada.

`runAsync()` não deve bloquear sincronicamente. Sua implementação de runAsync deverá devolver um CompletableFuture para permitir que o tempo de execução continuar. Se a carga de trabalho tem de implementar uma tarefa de longa execução que deve ser feita no interior do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguarda terminar (por conclusão com êxito, cancelamento ou falha) antes de passar sua tarefa. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `runAsync()` mais rápido possível quando o sistema pede cancelamento. O exemplo seguinte demonstra como processar um evento de cancelamento:

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

Neste exemplo de serviço sem estado, a contagem é armazenada numa variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida da sua instância de serviço atual. Quando o serviço muda ou for reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
Service Fabric introduz um novo tipo de serviço de monitorização de estado. Um serviço com estado pode manter o estado de forma fiável dentro do serviço em si, localizado conjuntamente com o código que está a utilizar. Estado fica elevada disponibilidade ao Service Fabric sem a necessidade de persistir o estado de um armazenamento externo.

Para converter um valor de contador de sem monitoração de estado para elevada disponibilidade e persistente, mesmo quando move ou reinicia o serviço, terá de um serviço com estado.

No mesmo diretório que o aplicativo de HelloWorld, pode adicionar um novo serviço, executando o `yo azuresfjava:AddService` comando. Escolha "Fiável com estado serviço" para sua estrutura e dê o nome "HelloWorldStateful". 

A aplicação deve agora ter dois serviços: o serviço sem estado HelloWorld e com o estado do serviço HelloWorldStateful.

Um serviço com estado tem os mesmos pontos de entrada como um serviço sem estado. A principal diferença é a disponibilidade de um provedor de estado que pode armazenar o estado de forma fiável. Service Fabric é fornecido com uma implementação de fornecedor de estado chamada Reliable Collections, permite-lhe criar estruturas de dados replicados através do Gestor de estado fiável. Um serviço fiável com estado usa esse provedor de estado por predefinição.

Abrir HelloWorldStateful.java na **HelloWorldStateful -> src**, que contém o seguinte método RunAsync:

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
`RunAsync()` funciona da mesma forma nos serviços de com e sem estado. No entanto, num serviço com estado, a plataforma executa trabalho adicional em seu nome antes de ser executada `RunAsync()`. Este trabalho pode incluir a garantir que o Reliable State Manager e das Reliable Collections são pronto a utilizar.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de estado fiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) é uma implementação de dicionário que pode utilizar para armazenar de forma fiável Estado no serviço. Com o Service Fabric e HashMaps confiável, pode armazenar dados diretamente no seu serviço sem a necessidade de um armazenamento persistente externo. HashMaps fiáveis tornar os dados de elevada disponibilidade. Service Fabric faz isso ao criar e gerir múltiplos *réplicas* do seu serviço para. Ele também fornece uma API que abstrai as complexidades de gerenciamento essas réplicas e de suas transições de estado.

As coleções fiáveis podem armazenar qualquer tipo de Java, incluindo seus tipos personalizados, com algumas limitações:

* O Service Fabric garante o seu estado altamente disponível por *replicar* estado por nós e fiável HashMap armazena seus dados para disco local em todas as réplicas. Isso significa que tudo o que é armazenado no HashMaps confiável deve estar *serializável*. 
* Objetos são replicados para elevada disponibilidade ao consolidar transações no HashMaps fiável. Objetos armazenados na HashMaps confiáveis são mantidos na memória local no seu serviço. Isso significa que tem uma referência local para o objeto.
  
   É importante que modifica as instâncias locais desses objetos sem efetuar uma operação de atualização na coleção fiável numa transação. Isto acontece porque as alterações para as instâncias locais de objetos não serão replicadas automaticamente. Deve reinserir o objeto de volta no dicionário ou utilize um da *atualizar* métodos no dicionário.

O Reliable State Manager gerencia HashMaps confiável para. Pode pedir a Reliable State Manager para uma coleção fiável pelo nome em qualquer altura e em qualquer lugar no seu serviço. O Gestor de estado confiável garante que obtém uma referência. Não recomendamos que guarde referências a instâncias de coleção fiável em variáveis de membro de classe ou propriedades. Especial deve ter cuidado para garantir que a referência é definida para uma instância durante todo o tempo do ciclo de vida do serviço. O Reliable State Manager processa esse trabalho para e está otimizado para visitas repetidas.


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

Operações em HashMaps confiáveis são assíncronas. Isto acontece porque as operações de escrita com as Reliable Collections executam operações de e/s para replicar e manter os dados no disco.

As operações de HashMap fiável *transacional*, para que possa manter estado consistente em vários HashMaps fiável e operações. Por exemplo, pode obter um item de trabalho de um dicionário fiável, realizar uma operação nele e salvar o resultado no outro HashMap confiáveis, tudo numa única transação. Isso é tratado como uma operação Atómica, e esta ação garante que toda a operação será efetuada com êxito ou toda a operação será revertida. Se ocorrer um erro depois de remover da fila do item, mas antes de guardar o resultado, toda a transação é revertida e o item permanecerá na fila para processamento.


## <a name="build-the-application"></a>Criar a aplicação

O Yeoman scaffolding inclui um script do gradle para criar a aplicação e scripts para implementar e remover a aplicação de bash. Para executar a aplicação, crie primeiro a aplicação com o gradle:

```bash
$ gradle
```

Isso produz um pacote de aplicação do Service Fabric que pode ser implementado com a CLI do Service Fabric.

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
> Para implementar a aplicação a um cluster Linux seguro no Azure, terá de configurar um certificado para validar a sua aplicação com o tempo de execução do Service Fabric. Se o fizer, permite que os seus serviços de Reliable Services comunicar com o tempo de execução do Service Fabric subjacente APIs. Para obter mais informações, consulte [configurar uma aplicação de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Passos Seguintes

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
