---
title: Diagnóstico e resolução de problemas Azure Cosmos DB Async Java SDK v2
description: Utilize funcionalidades como registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas problemas problemas com o Azure Cosmos DB em Async Java SDK v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 60d73f8b3eae21ab399853e8d05b67b7b431ee5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321059"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Problemas de resolução de problemas quando se utiliza o Azure Cosmos DB Async Java SDK v2 com contas API SQL

> [!div class="op_single_selector"]
> * [SDK v4 de Java](troubleshoot-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Você deve atualizar o seu projeto para [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) e, em seguida, ler o [guia de resolução de problemas](troubleshoot-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4 . Siga as instruções no [guia Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e [guia reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para atualizar. 
>
> Este artigo abrange a resolução de problemas apenas para Azure Cosmos DB Async Java SDK v2. Consulte as notas de [lançamento](sql-api-sdk-async-java.md)de Azure Cosmos DB Async Java SDK v2, [repositório de Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) e dicas de [desempenho](performance-tips-async-java.md) para obter mais informações.
>

Este artigo abrange questões comuns, soluções alternativas, passos de diagnóstico e ferramentas quando utiliza o [Java Async SDK](sql-api-sdk-async-java.md) com contas API API AZURE Cosmos DB SQL.
O SDK do Java Async fornece a representação lógica do lado do cliente para aceder à API SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e abordagens para o ajudar se encontrar problemas.

Comece com esta lista:

* Veja a secção [questões comuns e soluções alternativas] neste artigo.
* Veja o SDK, que está disponível [em fonte aberta no GitHub.](https://github.com/Azure/azure-cosmosdb-java) Tem uma [secção de problemas](https://github.com/Azure/azure-cosmosdb-java/issues) que é monitorizada ativamente. Verifique se algum problema semelhante com uma solução já está arquivado.
* Reveja as [dicas de desempenho](performance-tips-async-java.md)e siga as práticas sugeridas.
* Leia o resto deste artigo, se não encontrou uma solução. Em seguida, arquivar um [problema gitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha no tempo limite de leitura da Netty, baixa produção, alta latência

#### <a name="general-suggestions"></a>Sugestões gerais
* Certifique-se de que a aplicação está a funcionar na mesma região que a sua conta DB Azure Cosmos. 
* Verifique a utilização do CPU no anfitrião onde a aplicação está em funcionamento. Se a utilização do CPU for de 90% ou mais, execute a sua aplicação num anfitrião com uma configuração mais alta. Ou pode distribuir a carga em mais máquinas.

#### <a name="connection-throttling"></a>Estrangulamento de conexão
O estrangulamento da ligação pode ocorrer devido a um limite de [ligação numa máquina hospedeira] ou à [exaustão da porta Azure SNAT (PAT).]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de ligação numa máquina hospedeira
Alguns sistemas Linux, como o Red Hat, têm um limite superior no número total de ficheiros abertos. As tomadas no Linux são implementadas como ficheiros, pelo que este número também limita o número total de ligações.
Execute o seguinte comando.

```bash
ulimit -a
```
O número de ficheiros abertos máximos permitidos, que são identificados como "nofile", tem de ser pelo menos o dobro do tamanho da piscina de ligação. Para obter mais informações, consulte [as dicas de desempenho.](performance-tips-async-java.md)

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Exaustão portuária de Azure SNAT (PAT)

Se a sua aplicação for implementada em Máquinas Virtuais Azure sem endereço IP público, por padrão as [portas Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecem ligações a qualquer ponto final fora do seu VM. O número de ligações permitidas do VM ao ponto final DB Azure Cosmos é limitado pela [configuração Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 As portas Azure SNAT são utilizadas apenas quando o seu VM tem um endereço IP privado e um processo do VM tenta ligar-se a um endereço IP público. Existem duas soluções alternativas para evitar a limitação do Azure SNAT:

* Adicione o seu ponto final de serviço Azure Cosmos DB à sub-rede da sua rede virtual Azure Virtual Machines. Para mais informações, consulte [os pontos finais do serviço Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto final de serviço está ativado, os pedidos deixaram de ser enviados de um IP público para a Azure Cosmos DB. Em vez disso, a rede virtual e a identidade da sub-rede são enviadas. Esta alteração pode resultar em quedas de firewall se apenas os IPs públicos forem permitidos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicione uma sub-rede à firewall utilizando [ACLs de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público ao seu Azure VM.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Não consigo chegar ao Serviço - firewall
``ConnectTimeoutException`` indica que o SDK não pode chegar ao serviço.
Pode obter uma falha semelhante ao seguinte quando utilizar o modo direto:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Se tiver uma firewall a funcionar na sua máquina de aplicações, abra a gama de portas de 10.000 a 20.000 que são utilizadas pelo modo direto.
Siga também o [limite de Ligação numa máquina hospedeira.](#connection-limit-on-host)

#### <a name="http-proxy"></a>Procuração HTTP

Se utilizar um representante HTTP, certifique-se de que consegue suportar o número de ligações configuradas no SDK `ConnectionPolicy` .
Caso contrário, terá problemas de ligação.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Padrão de codificação inválido: Bloqueio da linha IO da Netty

O SDK usa a biblioteca [Netty](https://netty.io/) IO para comunicar com a Azure Cosmos DB. O SDK tem APIs async e utiliza APIs IO não bloqueando de Netty. O trabalho de IO da SDK é realizado em fios IO Netty. O número de fios IO Netty está configurado para ser o mesmo que o número de núcleos de CPU da máquina de aplicações. 

Os fios Netty IO destinam-se a ser utilizados apenas para trabalhos de IO Netty não bloqueados. O SDK devolve o resultado da invocação da API numa das linhas Netty IO ao código da aplicação. Se a aplicação realizar uma operação de longa duração depois de receber resultados na linha Netty, o SDK pode não ter fios IO suficientes para realizar o seu trabalho interno de IO. Tal codificação de aplicações pode resultar em baixa produção, alta latência e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução é mudar o fio quando souber que a operação leva tempo.

Por exemplo, dê uma olhada no seguinte corte de código. Pode realizar um trabalho duradouro que leva mais de alguns milissegundos na linha Netty. Em caso afirmativo, pode eventualmente entrar num estado em que nenhum fio Netty IO está presente para processar o trabalho de IO. Como resultado, obtém-se uma falha de ReadTimeoutException.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
A solução é mudar o fio em que executa um trabalho que leva tempo. Defina uma instância singleton do programador para a sua aplicação.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Você pode precisar de fazer um trabalho que leve tempo, por exemplo, trabalho computacionalmente pesado ou bloqueio de IO. Neste caso, mude o fio para um trabalhador fornecido pela sua `customScheduler` utilização da `.observeOn(customScheduler)` API.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Ao `observeOn(customScheduler)` utilizar, desbloqueie a linha Netty IO e mude para o seu próprio fio personalizado fornecido pelo programador personalizado. Esta modificação resolve o problema. Não vais ter mais um `io.netty.handler.timeout.ReadTimeoutException` fracasso.

### <a name="connection-pool-exhausted-issue"></a>Problema esgotado da piscina de conexão

`PoolExhaustedException` é uma falha do lado do cliente. Esta falha indica que a carga de trabalho da sua aplicação é superior à que o pool de conexão SDK pode servir. Aumente o tamanho da piscina de ligação ou distribua a carga em várias aplicações.

### <a name="request-rate-too-large"></a>Taxa de pedido demasiado grande
Esta falha é uma falha do lado do servidor. Indica que consumiu o seu rendimento. Tentar mais tarde. Se você receber esta falha com frequência, considere um aumento na produção de coleção.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha na ligação ao emulador Azure Cosmos DB

O certificado https do emulador Azure Cosmos DB é auto-assinado. Para que o SDK trabalhe com o emulador, importe o certificado de emulador para uma Loja Java TrustStore. Para mais informações, consulte [os certificados emuladores Export Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Questões de Conflito de Dependência

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A exceção acima indica que tem uma dependência de uma versão mais antiga do RxJava lib (por exemplo, 1.2.2). O nosso SDK conta com O RxJava 1.3.8 que não tem APIs disponíveis na versão anterior do RxJava. 

A solução para estas questões é identificar qual outra dependência traz em RxJava-1.2.2 e excluir a dependência transitiva de RxJava-1.2.2, e permitir que a CosmosDB SDK traga a versão mais recente.

Para identificar que biblioteca traz RxJava-1.2.2, executar o seguinte comando ao lado do seu projeto pom.xml ficheiro:
```bash
mvn dependency:tree
```
Para mais informações, consulte o [guia da árvore de dependência de maven.](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)

Uma vez que identifique o RxJava-1.2.2 é dependência transitiva de qual outra dependência do seu projeto, pode modificar a dependência dessa lib no seu ficheiro pom e excluir a dependência transitiva da RxJava:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Para mais informações, consulte o [guia de dependência transitiva.](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Ativar o registo SDK do cliente

O Java Async SDK usa o SLF4j como fachada de registo que suporta o registo em estruturas populares de registo, como log4j e logback.

Por exemplo, se pretender utilizar o log4j como estrutura de registo, adicione as seguintes libs no seu apêrece Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Adicione também um log4j config.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Para mais informações, consulte o [manual de registo sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Estatísticas da rede de OS
Executar o comando netstat para ter uma noção de quantas ligações existem em estados como `ESTABLISHED` e `CLOSE_WAIT` .

Em Linux, pode executar o seguinte comando.
```bash
netstat -nap
```
Filtrar o resultado apenas para ligações ao ponto final Azure Cosmos DB.

O número de ligações com o ponto final do Azure Cosmos DB no `ESTABLISHED` estado não pode ser maior do que o tamanho da piscina de conexão configurada.

Muitas ligações com o ponto final do Azure Cosmos DB podem estar no `CLOSE_WAIT` estado. Pode haver mais de 1.000. Um número tão alto indica que as ligações são estabelecidas e demolidas rapidamente. Esta situação pode causar problemas. Para mais informações, consulte a secção [Questões e Soluções Alternativas.]

 <!--Anchors-->
[Problemas comuns e soluções]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de ligação numa máquina hospedeira]: #connection-limit-on-host
[Exaustão portuária de Azure SNAT (PAT)]: #snat


