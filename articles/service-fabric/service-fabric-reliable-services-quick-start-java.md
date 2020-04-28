---
title: Crie o seu primeiro serviço confiável em Java
description: Introdução à criação de uma aplicação Microsoft Azure Service Fabric com serviços apátridas e apátridas.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 7855b92c90a9ccd208a25080c260437e6808d1b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184151"
---
# <a name="get-started-with-reliable-services-in-java"></a>Começar com Serviços Fiáveis em Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica os fundamentos dos Serviços Fiáveis de Tecido de Serviço Azure e percorre-o através da criação e implementação de uma simples aplicação de Serviço Fiável escrita em Java. 

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Tecido de Serviço configurado na sua máquina.
Se precisar de preparar, vá [começar no Mac](service-fabric-get-started-mac.md) ou começar no [Linux.](service-fabric-get-started-linux.md)

## <a name="basic-concepts"></a>Conceitos básicos
Para começar com serviços fiáveis, basta compreender alguns conceitos básicos:

* **Tipo**de serviço : Esta é a sua implementação de serviço. É definido pela classe que escreve `StatelessService` que se estende e qualquer outro código ou dependências nele utilizados, juntamente com um nome e um número de versão.
* **Caso de serviço nomeado**: Para executar o seu serviço, cria instâncias nomeadas do seu tipo de serviço, tal como cria situações de objetos de tipo de classe. As instâncias de serviço são, de facto, instantâneas objetivas da sua classe de serviço que escreve.
* **Anfitrião de serviço**: As instâncias de serviço nomeadas que cria precisam de ser executadas dentro de um hospedeiro. O anfitrião do serviço é apenas um processo onde as instâncias do seu serviço podem ser executadas.
* **Inscrição de serviço:** O registo reúne tudo. O tipo de serviço deve ser registado com o tempo de funcionar do Tecido de Serviço num hospedeiro de serviço para permitir que o Tecido de Serviço crie instâncias do mesmo.  

## <a name="create-a-stateless-service"></a>Criar um serviço apátrida
Comece por criar uma aplicação De Tecido de Serviço. O SDK de Tecido de Serviço para Linux inclui um gerador Yeoman para fornecer o andaime para uma aplicação de Tecido de Serviço com um serviço apátrida. Comece por executar o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço Fiável Apátrida**. Para este tutorial, nomeie a aplicação "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui diretórios para e `HelloWorldApplication` . `HelloWorld`

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
Os tipos de serviço devem ser registados no tempo de execução do Tecido de Serviço. O tipo de serviço `ServiceManifest.xml` é definido na `StatelessService`classe de serviço e na sua classe de serviço que implementa . A inscrição de serviço é realizada no ponto de entrada principal do processo. Neste exemplo, o ponto de `HelloWorldServiceHost.java`entrada principal do processo é:

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

Abra **helloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta classe define o tipo de serviço, e pode executar qualquer código. O serviço API fornece dois pontos de entrada para o seu código:

* Um método de ponto de `runAsync()`entrada aberto, chamado , onde você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa duração.

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
A plataforma chama a este método quando uma instância de um serviço é colocada e pronta a ser executada. Para um serviço apátrida, isso significa que quando a instância de serviço é aberta. É fornecido um símbolo de cancelamento para coordenar quando a sua instância de serviço precisa de ser fechada. No Tecido de Serviço, este ciclo aberto/próximo de uma instância de serviço pode ocorrer muitas vezes ao longo da vida do serviço como um todo. Isto pode acontecer por várias razões, incluindo:

* O sistema move as suas instâncias de serviço para o equilíbrio de recursos.
* As falhas ocorrem no seu código.
* A aplicação ou sistema é atualizado.
* O hardware subjacente experimenta uma paragem.

Esta orquestração é gerida pela Service Fabric para manter o seu serviço altamente disponível e devidamente equilibrado.

`runAsync()`não deve bloquear sincronizadamente. A sua implementação do runAsync deve devolver um CompletableFuture para permitir que o tempo de execução continue. Se a sua carga de trabalho precisar de implementar uma tarefa de longo prazo que deve ser feita dentro do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço de cooperação orquestrado pelo símbolo de cancelamento fornecido. O sistema aguarda que a sua tarefa termine (por conclusão, cancelamento ou falha com sucesso) antes de seguir em frente. É importante honrar o sinal de cancelamento, terminar `runAsync()` qualquer trabalho e sair o mais rápido possível quando o sistema solicitar o cancelamento. O exemplo que se segue demonstra como lidar com um evento de cancelamento:

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

Neste exemplo de serviço apátrida, a contagem é armazenada numa variável local. Mas como este é um serviço apátrida, o valor armazenado só existe para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou reinicia, o valor perde-se.

## <a name="create-a-stateful-service"></a>Criar um serviço imponente
A Service Fabric introduz um novo tipo de serviço que é imponente. Um serviço imponente pode manter o estado de forma fiável dentro do próprio serviço, co-localizado com o código que o está a usar. O Estado é altamente disponibilizado pela Service Fabric sem a necessidade de persistir em estado para uma loja externa.

Para converter um contra-valor de apátrida para altamente disponível e persistente, mesmo quando o serviço se move ou reinicia, precisa de um serviço imponente.

No mesmo diretório que a aplicação HelloWorld, pode `yo azuresfjava:AddService` adicionar um novo serviço executando o comando. Escolha o "Reliable Stateful Service" para a sua estrutura e nomeie o serviço "HelloWorldStateful". 

A sua aplicação deverá agora ter dois serviços: o serviço apátrida HelloWorld e o serviço imponente HelloWorldStateful.

Um serviço imponente tem os mesmos pontos de entrada que um serviço apátrida. A principal diferença é a disponibilidade de um provedor do Estado que possa armazenar o Estado de forma fiável. O Service Fabric vem com uma implementação do fornecedor estatal chamada Reliable Collections, que permite criar estruturas de dados replicadas através do Reliable State Manager. Um serviço fiável e audato utiliza este fornecedor estatal por defeito.

Abra HelloWorldStateful.java em **HelloWorldStateful -> src,** que contém o seguinte método RunAsync:

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
`RunAsync()`opera da mesma forma em serviços apátridas e apátridas. No entanto, num serviço imponente, a plataforma realiza trabalhoadicional `RunAsync()`em seu nome antes de ser executada. Este trabalho pode incluir garantir que o Gestor de Estado Fiável e Coleções Fiáveis estão prontos a ser utilizados.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de Estado Confiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) é uma implementação de dicionário que pode usar para armazenar o estado de forma fiável no serviço. Com tecido de serviço e HashMaps fiáveis, pode armazenar dados diretamente no seu serviço sem a necessidade de uma loja externa persistente. O HashMaps fiável disponibiliza os seus dados. O Service Fabric consegue isso criando e gerindo *múltiplas réplicas* do seu serviço para si. Também fornece uma API que abstrata as complexidades da gestão dessas réplicas e as suas transições estatais.

As Coleções Fiáveis podem armazenar qualquer tipo Java, incluindo os seus tipos personalizados, com um par de ressalvas:

* O Service Fabric disponibiliza o seu estado *replicando* o estado através de nós, e o Reliable HashMap armazena os seus dados em disco local em cada réplica. Isto significa que tudo o que está armazenado em HashMaps fiável deve ser *serializável.* 
* Os objetos são replicados para alta disponibilidade quando comete transações em HashMaps fiáveis. Os objetos armazenados em HashMaps fiáveis são mantidos na memória local ao seu serviço. Isto significa que tem uma referência local ao objeto.
  
   É importante que não sofram mutações locais desses objetos sem realizar uma operação de atualização sobre a recolha fiável numa transação. Isto porque as alterações às instâncias locais dos objetos não serão replicadas automaticamente. Deve voltar a inserir o objeto no dicionário ou utilizar um dos métodos de *atualização* do dicionário.

O Gestor de Estado Confiável gere hashMaps fiáveis para si. Você pode pedir ao Gestor de Estado fiável uma coleção fiável pelo nome a qualquer momento e em qualquer lugar ao seu serviço. O Gestor de Estado confiável garante que obtém uma referência de volta. Não recomendamos que guarde referências a casos de recolha fiáveis em variáveis ou propriedades de membros da classe. Há que ter especial cuidado para garantir que a referência seja definida em todos os momentos do ciclo de vida de serviço. O Gestor de Estado confiável lida com este trabalho para si, e está otimizado para visitas repetidas.


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

As operações em HashMaps fiáveis são assíncronas. Isto porque as operações de escrita com Coleções Fiáveis realizam operações de I/S para replicar e persistir dados em disco.

As operações de HashMap fiáveis são *transacionais,* para que possa manter o estado consistente em vários HashMaps e operações fiáveis. Por exemplo, pode obter um item de trabalho de um Dicionário Fiável, executar uma operação nele e guardar o resultado em outro HashMap fiável, tudo dentro de uma única transação. Isto é tratado como uma operação atómica, e garante que ou toda a operação terá sucesso ou toda a operação irá reverter. Se ocorrer um erro após a desfilar o item, mas antes de guardar o resultado, toda a transação é relançada e o item permanece na fila para processamento.


## <a name="build-the-application"></a>Criar a aplicação

O andaime Yeoman inclui um roteiro de gradle para construir a aplicação e bater scripts para implementar e remover a aplicação. Para executar a aplicação, construa primeiro a aplicação com gradle:

```bash
$ gradle
```

Isto produz um pacote de aplicações Service Fabric que pode ser implantado usando o ClI de Tecido de Serviço.

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
> Para implementar a aplicação num cluster Linux seguro em Azure, é necessário configurar um certificado para validar a sua aplicação com o tempo de execução do Tecido de Serviço. Ao fazê-lo, os seus serviços fiáveis comunicam com as APIs de tempo de execução do Tecido de Serviço subjacentes. Para saber mais, consulte [a Configure uma aplicação De serviços fiáveis para executar em clusters Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Passos seguintes

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
