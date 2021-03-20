---
title: Crie o seu primeiro serviço de confiança em Java
description: Introdução à criação de uma aplicação Microsoft Azure Service Fabric com serviços apátridas e imponentes em Java.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535241"
---
# <a name="get-started-with-reliable-services-in-java"></a>Começar com serviços fiáveis em Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica os fundamentos da Azure Service Reliable Services e acompanha-o através da criação e implementação de uma simples aplicação de Serviço Fiável escrita em Java. 

## <a name="installation-and-setup"></a>Instalação e instalação
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Tecido de Serviço montado na sua máquina.
Se precisar de o configurar, comece [a trabalhar no Mac](service-fabric-get-started-mac.md) ou comece a trabalhar no [Linux.](service-fabric-get-started-linux.md)

## <a name="basic-concepts"></a>Conceitos básicos
Para começar com os Serviços Fiáveis, basta compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a sua implementação de serviço. É definido pela classe que escreve que se estende `StatelessService` e qualquer outro código ou dependências neles utilizados, juntamente com um nome e um número de versão.
* **Caso de serviço nomeado**: Para executar o seu serviço, cria instâncias nomeadas do seu tipo de serviço, tal como cria instâncias de objetos de tipo classe. As instâncias de serviço são, de facto, instantâneas de objetos da sua classe de serviço que escreve.
* **Anfitrião de serviço**: As instâncias de serviço nomeadas que cria precisam de ser executadas dentro de um hospedeiro. O anfitrião do serviço é apenas um processo em que os casos do seu serviço podem ser executados.
* **Registo de** serviço : As inscrições reúnem tudo. O tipo de serviço deve ser registado no tempo de funcionação do Tecido de Serviço num anfitrião de serviço para permitir que o Service Fabric crie instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço apátrida
Comece por criar uma aplicação De Tecido de Serviço. O Service Fabric SDK para Linux inclui um gerador Yeoman para fornecer o andaime para uma aplicação de Tecido de Serviço com um serviço apátrida. Comece por executar o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço Apátrida Fiável**. Para este tutorial, nomeie a aplicação "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui diretórios para o `HelloWorldApplication` e `HelloWorld` .

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
### <a name="service-registration"></a>Registo de serviço
Os tipos de serviço devem ser registados no tempo de funcionação do Tecido de Serviço. O tipo de serviço é definido na `ServiceManifest.xml` classe de serviço e da sua classe de serviço que `StatelessService` implementa. O registo de serviço é realizado no ponto de entrada principal do processo. Neste exemplo, o principal ponto de entrada do processo `HelloWorldServiceHost.java` é:

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

Abra **a HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para o seu código:

* Um método de ponto de entrada aberto, chamado `runAsync()` , onde você pode começar a executar quaisquer cargas de trabalho, incluindo cargas de trabalho de computação de longa duração.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Um ponto de entrada de comunicação onde pode ligar a sua pilha de comunicação de eleição. É aqui que pode começar a receber pedidos de utilizadores e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Este tutorial centra-se no método do `runAsync()` ponto de entrada. É aqui que pode começar imediatamente a executar o seu código.

### <a name="runasync"></a>RunAsync
A plataforma chama este método quando uma instância de um serviço é colocada e pronta para ser executada. Para um serviço apátrida, isto significa quando a instância de serviço é aberta. É fornecido um sinal de cancelamento para coordenar quando a sua instância de serviço precisa de ser encerrada. No Tecido de Serviço, este ciclo aberto/fechado de uma instância de serviço pode ocorrer muitas vezes ao longo da vida útil do serviço como um todo. Isto pode acontecer por várias razões, incluindo:

* O sistema move as suas instâncias de serviço para o equilíbrio de recursos.
* Falhas ocorrem no seu código.
* A aplicação ou sistema está atualizado.
* O hardware subjacente experimenta uma paragem.

Esta orquestração é gerida pela Service Fabric para manter o seu serviço altamente disponível e devidamente equilibrado.

`runAsync()` não deve bloquear sincronizadamente. A sua implementação de runAsync deve devolver um CompletableFuture para permitir que o tempo de execução continue. Se a sua carga de trabalho precisar de implementar uma tarefa de longa duração que deve ser feita dentro do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço de cooperação orquestrado pelo token de cancelamento fornecido. O sistema aguarda que a sua tarefa termine (com sucesso de conclusão, cancelamento ou falha) antes de seguir em frente. É importante honrar o token de cancelamento, terminar qualquer trabalho e sair `runAsync()` o mais rápido possível quando o sistema solicita cancelamento. O exemplo a seguir demonstra como lidar com um evento de cancelamento:

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

Neste exemplo de serviço apátrida, a contagem é armazenada numa variável local. Mas como este é um serviço apátrida, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou reinicia, o valor perde-se.

## <a name="create-a-stateful-service"></a>Criar um serviço imponente
A Service Fabric introduz um novo tipo de serviço que é imponente. Um serviço estatal pode manter o estado de forma fiável dentro do próprio serviço, co-localizado com o código que o está a usar. O Estado é altamente disponibilizado pela Service Fabric sem a necessidade de persistir em estado de uma loja externa.

Para converter um valor de contra-valor de apátrida para altamente disponível e persistente, mesmo quando o serviço se move ou reinicia, precisa de um serviço estatal.

No mesmo diretório que a aplicação HelloWorld, pode adicionar um novo serviço executando o `yo azuresfjava:AddService` comando. Escolha o "Serviço Stateful Fiável" para a sua estrutura e nomeie o serviço "HelloWorldStateful". 

A sua aplicação deverá agora ter dois serviços: o serviço apátrida HelloWorld e o serviço stateful HelloWorldStateful.

Um serviço estatal tem os mesmos pontos de entrada que um serviço apátrida. A principal diferença é a disponibilidade de um fornecedor estatal que pode armazenar o Estado de forma fiável. O Service Fabric vem com uma implementação de fornecedor estatal chamada Reliable Collections, que permite criar estruturas de dados replicadas através do Gestor estatal fiável. Um Serviço Fidedigna imponente utiliza este fornecedor estatal por defeito.

Abra a .java HelloWorldStateful em **HelloWorldStateful -> src**, que contém o seguinte método RunAsync:

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
`RunAsync()` opera da mesma forma em serviços apátridas e apátridas. No entanto, num serviço imponente, a plataforma realiza trabalhos adicionais em seu nome antes de executar `RunAsync()` . Este trabalho pode incluir garantir que o Gestor de Estado Fiável e as Coleções Fiáveis estão prontas a usar.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de Estado Fiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) é uma implementação de dicionário que pode usar para armazenar de forma fiável o estado no serviço. Com o Service Fabric e o Reliable HashMaps, pode armazenar dados diretamente no seu serviço sem a necessidade de uma loja externa persistente. HashMaps fidedignas tornam os seus dados altamente disponíveis. O Service Fabric realiza-o criando e gerindo *múltiplas réplicas* do seu serviço para si. Também fornece uma API que retira as complexidades da gestão dessas réplicas e as suas transições estatais.

As Coleções Fiáveis podem armazenar qualquer tipo de Java, incluindo os seus tipos personalizados, com algumas ressalvas:

* O Service Fabric torna o seu estado altamente disponível *replicando* o estado através dos nós, e o HashMap fiável armazena os seus dados para o disco local em cada réplica. Isto significa que tudo o que está armazenado em HashMaps fiável deve ser *serializável*. 
* Os objetos são replicados para uma elevada disponibilidade quando efetivos transações em HashMaps Fidedignas. Os objetos armazenados em HashMaps fidedignas são mantidos na memória local no seu serviço. Isto significa que tem uma referência local ao objeto.
  
   É importante que não mutação de instâncias locais desses objetos sem realizar uma operação de atualização sobre a coleção fiável numa transação. Isto porque as alterações nas instâncias locais de objetos não serão replicadas automaticamente. Deve voltar a inserir o objeto no dicionário ou utilizar um dos métodos de *atualização* do dicionário.

O Gestor de Estado Fiável gere hasmaps fiáveis para si. Pode pedir ao Gestor de Estado Fiável uma recolha fiável pelo nome a qualquer momento e em qualquer lugar do seu serviço. O Gestor de Estado Fidedigna garante que recebe uma referência de volta. Não recomendamos que guarde referências a casos de recolha fiáveis em variáveis ou propriedades de membros da classe. Deve-se ter especial cuidado para garantir que a referência seja sempre definida num caso no ciclo de vida do serviço. O Gestor de Estado Confiável trata deste trabalho para si, e está otimizado para visitas repetidas.


### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncroas
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

As operações em HashMaps fiáveis são assíncronas. Isto porque as operações de escrita com Coleções Fiáveis executam operações de E/S para replicar e persistir dados em disco.

As operações hashMap fiáveis são *transacionais,* para que possa manter o estado consistente em vários HashMaps e operações fiáveis. Por exemplo, você pode obter um item de trabalho de um Dicionário Fiável, realizar uma operação nele, e guardar o resultado em outro HashMap fiável, tudo dentro de uma única transação. Isto é tratado como uma operação atómica, e garante que toda a operação terá sucesso ou toda a operação irá recuar. Se ocorrer um erro depois de descodionar o item, mas antes de guardar o resultado, toda a transação é recorrida e o item permanece na fila para processamento.


## <a name="build-the-application"></a>Criar a aplicação

O andaime Yeoman inclui um script gradle para construir a aplicação e bater scripts para implementar e remover a aplicação. Para executar a aplicação, primeiro construa a aplicação com gradle:

```bash
$ gradle
```

Isto produz um pacote de aplicação de Tecido de Serviço que pode ser implementado usando o Service Fabric CLI.

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

Após a implementação da aplicação, abra um browser e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em `http://localhost:19080/Explorer`. Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implementar a aplicação num cluster Linux seguro em Azure, é necessário configurar um certificado para validar a sua aplicação com o tempo de execução do Service Fabric. Ao fazê-lo, os seus serviços Reliable Services comunicam com as APIs de execução do tecido de serviço subjacente. Para saber mais, consulte [configurar uma aplicação Reliable Services para executar em clusters Linux.](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)  
>

## <a name="next-steps"></a>Passos seguintes

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
