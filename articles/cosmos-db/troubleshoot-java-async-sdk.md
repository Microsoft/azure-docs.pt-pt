---
title: Diagnosticar e resolver problemas relacionados com o SDK do Azure Cosmos DB Java assíncrono
description: Utilizar funcionalidades como o registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas do Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0a2bbb33182fcdef3cc6ed7ff213557f90be4544
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880080"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Resolver problemas ao utilizar o SDK de Async Java com contas do Azure Cosmos DB SQL API
Este artigo aborda problemas comuns, soluções alternativas, passos de diagnóstico e ferramentas, ao utilizar o [SDK do Java Async](sql-api-sdk-async-java.md) com contas de API de SQL do Azure Cosmos DB.
O SDK de Async Java fornece uma representação lógica do lado do cliente para acessar a API de SQL do Azure Cosmos DB. Este artigo descreve ferramentas e abordagens para ajudá-lo caso se depare com quaisquer problemas.

Começar com esta lista:

* Dê uma olhada a [problemas comuns e soluções alternativas] secção deste artigo.
* Examinar o SDK, que está disponível [código-fonte aberto no GitHub](https://github.com/Azure/azure-cosmosdb-java). Ele tem um [emite secção](https://github.com/Azure/azure-cosmosdb-java/issues) que está a ser monitorizado ativamente. Verifique se qualquer problema semelhante com uma solução alternativa é já o arquivou.
* Reveja os [sugestões de desempenho](performance-tips-async-java.md)e siga as práticas sugeridas.
* Leia o restante deste artigo, se não encontrar uma solução. Ficheiro, em seguida, um [problema do GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas, de rede Netty ler falha de tempo limite, baixo débito, latência elevada

#### <a name="general-suggestions"></a>Sugestões gerais
* Certificar-se de que a aplicação está em execução na mesma região que a sua conta do Azure Cosmos DB. 
* Verifique a utilização da CPU no anfitrião onde a aplicação está em execução. Se a utilização da CPU é igual ou superior a 90 por cento, execute a aplicação num anfitrião com uma configuração superior. Ou pode distribuir a carga em máquinas mais.

#### <a name="connection-throttling"></a>Limitação de conexão
Limitação de conexão pode acontecer devido a um [Limite de ligações numa máquina de anfitrião] ou [Exaustão de porta de SNAT (PAT) do Azure].

##### <a name="connection-limit-on-host"></a>Limite de ligações numa máquina de anfitrião
Alguns sistemas de Linux, como Red Hat, tem um limite superior no número total de ficheiros abertos. Sockets no Linux são implementados como arquivos, para que este número limita o número total de ligações, demasiado.
Execute o seguinte comando.

```bash
ulimit -a
```
O número de máximos permitidos ficheiros abertos, que são identificadas como "nofile", tem de ser double, pelo menos, o tamanho do conjunto de ligação. Para obter mais informações, consulte [sugestões de desempenho](performance-tips-async-java.md).

##### <a name="snat"></a>Exaustão de porta de SNAT (PAT) do Azure

Se a aplicação é implementada em máquinas de virtuais do Azure sem um endereço IP público, por predefinição [portas de SNAT de Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecer ligações para qualquer ponto de extremidade fora da sua VM. O número de ligações permitido da VM para o ponto de final do Azure Cosmos DB é limitado pelos [configuração do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Portas SNAT do Azure são utilizadas apenas quando a VM tem um endereço IP privado e um processo da VM tenta estabelecer ligação com um endereço IP público. Existem duas soluções alternativas para evitar a limitação de SNAT do Azure:

* Adicione o ponto de final de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de máquinas virtuais do Azure. Para obter mais informações, consulte [pontos finais de serviço de rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto final de serviço está ativado, os pedidos não são mais enviados de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade do sub-rede são enviados. Esta alteração poderá resultar em quedas de firewall se só são permitidos IPs públicos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicionar uma sub-rede para o firewall usando [ACLs de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à VM do Azure.

#### <a name="http-proxy"></a>Proxy HTTP

Se utilizar um proxy de HTTP, certificar-se de que pode suportar o número de ligações configuradas no SDK `ConnectionPolicy`.
Caso contrário, o que enfrenta problemas de ligação.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Padrão de codificação inválida: Bloquear o thread de e/s de Netty

O SDK utiliza o [Netty](https://netty.io/) biblioteca de e/s para comunicar com o Azure Cosmos DB. O SDK tem APIs de Async e utiliza as APIs de e/s de Netty de sem bloqueio. Trabalho de e/s do SDK é executado em threads de e/s Netty. O número de threads de e/s Netty está configurado para ser o mesmo que o número de núcleos de CPU da máquina de aplicação. 

Os threads de e/s de Netty se destinam a ser utilizado apenas para o trabalho de e/s de Netty sem bloqueio. O SDK retorna o resultado da invocação de API em um dos threads de e/s de Netty ao código da aplicação. Se a aplicação executa uma operação de longa duração, depois de receber os resultados no Netty thread, o SDK pode não ter suficiente threads de e/s para realizar o trabalho de e/s interno. Esse tipo de codificação de aplicação pode resultar em baixo débito, latência alta, e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução é mudar o thread quando sabe que a operação demora algum tempo.

Por exemplo, veja o seguinte trecho de código. Pode executar o trabalho de longa duração, que demora mais do que alguns milissegundos no Netty thread. Se assim for, eventualmente, pode obter para um Estado em que nenhum thread de e/s de Netty está presente para processar trabalho de e/s. Como resultado, ocorre uma falha de ReadTimeoutException.
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
   A solução alternativa é alterar o thread no qual executar o trabalho que demora algum tempo. Defina uma instância singleton do scheduler para a sua aplicação.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Poderá ter de fazer o trabalho que leva tempo, por exemplo, trabalho computacionalmente pesado ou e/s de bloqueio. Neste caso, mude o thread para uma função de trabalho fornecida pelo seu `customScheduler` utilizando o `.observeOn(customScheduler)` API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Ao utilizar `observeOn(customScheduler)`, o thread de e/s de Netty de versão e mude para o seu próprio thread personalizado fornecido pelo scheduler personalizado. Essa modificação resolve o problema. Não obterá um `io.netty.handler.timeout.ReadTimeoutException` deixa de poder falha.

### <a name="connection-pool-exhausted-issue"></a>Conjunto de ligação esgotado o problema

`PoolExhaustedException` é uma falha do lado do cliente. Esta falha indica que a sua carga de trabalho de aplicação é maior do que o que pode servir o conjunto de ligações de SDK. Aumentar o tamanho do conjunto de ligação ou distribuir a carga em várias aplicações.

### <a name="request-rate-too-large"></a>Taxa de pedido demasiado grande
Esta falha é uma falha do lado do servidor. Ele indica que consumido débito aprovisionado. Tente novamente mais tarde. Se obtiver, muitas vezes, esta falha, considere um aumento no débito de coleção.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha ao ligar ao emulador do Azure Cosmos DB

O certificado do Azure Cosmos DB emulator HTTPS está autoassinado. Para o SDK trabalhar com o emulador, importe o certificado do emulador para um TrustStore de Java. Para obter mais informações, consulte [certificados de emulador do Azure Cosmos DB exportar](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemas de conflito de dependência

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A exceção acima sugere que tem uma dependência numa versão mais antiga do RxJava lib (por exemplo, 1.2.2). O SDK depende RxJava 1.3.8 uma que tem APIs não disponíveis na versão anterior do RxJava. 

A solução alternativa para tais issuses é identificar quais outras dependências traz RxJava 1.2.2 e excluir a dependência transitiva de RxJava 1.2.2 e permitir que o SDK do cosmos DB trazer a versão mais recente.

Para identificar que biblioteca traz RxJava-1.2.2, execute o seguinte comando ao lado de seu arquivo de pom. XML do projeto:
```bash
mvn dependency:tree
```
Para obter mais informações, consulte a [guia de árvore de dependência maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Depois de identificar RxJava 1.2.2 é transitiva dependência do que outras dependências do seu projeto, pode modificar a dependência no lib no ficheiro pom e exclusão de dependência de transitiva RxJava:

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

Para obter mais informações, consulte a [excluir o guia de dependência transitiva](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sice-logging"></a>Ativar o registo do SDK de cliente

As utilizações de SDK do Java Async SLF4j como a fachada de registo que suporta o início de sessão em arquiteturas de registo populares, como log4j e logback.

Por exemplo, se pretender utilizar log4j como a arquitetura de registo, adicione as seguintes bibliotecas no caminho da sua classe Java.

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

Também adicione uma configuração log4j.
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

Para obter mais informações, consulte a [sfl4j registo manual](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Estatísticas de rede do SO
Execute o comando netstat para ter uma ideia de quantos ligações encontram-se nos Estados como `ESTABLISHED` e `CLOSE_WAIT`.

No Linux, pode executar o seguinte comando.
```bash
netstat -nap
```
Filtre o resultado para apenas as ligações para o ponto de final do Azure Cosmos DB.

O número de ligações para o ponto de final do Azure Cosmos DB no `ESTABLISHED` Estado não pode ser maior do que o tamanho do conjunto de ligação configurada.

Várias ligações para o ponto de final do Azure Cosmos DB podem estar a ser o `CLOSE_WAIT` estado. Podem existir mais do que 1.000. Um número que alta indica que as ligações são estabelecidas e subdivididas rapidamente. Esta situação potencialmente causa problemas. Para obter mais informações, consulte a [problemas comuns e soluções alternativas] secção.

 <!--Anchors-->
[Problemas comuns e soluções alternativas]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de ligações numa máquina de anfitrião]: #connection-limit-on-host
[Exaustão de porta de SNAT (PAT) do Azure]: #snat


