---
title: Diagnóstico e resolução de problemas Azure Cosmos DB Java SDK v4
description: Utilize funcionalidades como registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas problemas problemas com o Azure Cosmos DB em Java SDK v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596473"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Problemas de resolução de problemas quando utiliza Azure Cosmos DB Java SDK v4 com contas API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v4 de Java](troubleshoot-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Este artigo abrange apenas a resolução de problemas para Azure Cosmos DB Java SDK v4. Por favor, consulte as notas de [lançamento](sql-api-sdk-java-v4.md)do Azure Cosmos DB Java SDK v4, [repositório de Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e dicas de [desempenho](performance-tips-java-sdk-v4-sql.md) para obter mais informações. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

Este artigo abrange questões comuns, soluções alternativas, passos de diagnóstico e ferramentas quando utiliza a Azure Cosmos DB Java SDK v4 com contas Azure Cosmos DB SQL API.
Azure Cosmos DB Java SDK v4 fornece representação lógica do lado do cliente para aceder à API AZURE Cosmos DB SQL. Este artigo descreve as ferramentas e abordagens para o ajudar se encontrar problemas.

Comece com esta lista:

* Veja a secção [questões comuns e soluções alternativas] neste artigo.
* Veja o Java SDK no Azure Cosmos DB central repo, que está disponível [fonte aberta no GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) Tem uma [secção de problemas](https://github.com/Azure/azure-sdk-for-java/issues) que é monitorizada ativamente. Verifique se algum problema semelhante com uma solução já está arquivado. Uma dica útil é filtrar as questões pela etiqueta *cosmos:v4 item.*
* Reveja as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md) para Azure Cosmos DB Java SDK v4, e siga as práticas sugeridas.
* Leia o resto deste artigo, se não encontrou uma solução. Em seguida, arquivar um [problema gitHub](https://github.com/Azure/azure-sdk-for-java/issues). Se houver uma opção para adicionar tags à sua edição do GitHub, adicione uma etiqueta *cosmos:v4 item.*

### <a name="retry-logic"></a>Lógica de Retíria <a id="retry-logics"></a>
Em qualquer falha de E/S, o SDK do Cosmos DB tentará repetir a operação falhada se for viável realizar a repetição no SDK. Ter uma nova agem para qualquer falha é uma boa prática, mas especificamente lidar/tentar falhar a escrita é uma obrigação. Recomenda-se usar o mais recente SDK, uma vez que a lógica de retíria está continuamente a ser melhorada.

1. As falhas de IO de leitura e consulta serão novamente julgadas pelo SDK sem as deixar em cima do utilizador final.
2. As escritas (Criar, Aumentar, Substituir, Excluir) são "não" idempotentes e, portanto, a SDK nem sempre pode voltar a tentar cegamente as operações de escrita falhadas. É necessário que a lógica de aplicação do utilizador lide com a falha e relemque.
3. [Problemas de tiro sdk disponibilidade](troubleshoot-sdk-availability.md) explica retréis para contas de Cosmos DB multi-região.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha no tempo limite de leitura da Netty, baixa produção, alta latência

#### <a name="general-suggestions"></a>Sugestões gerais
Para melhor desempenho:
* Certifique-se de que a aplicação está a funcionar na mesma região que a sua conta DB Azure Cosmos. 
* Verifique a utilização do CPU no anfitrião onde a aplicação está em funcionamento. Se a utilização do CPU for de 50% ou mais, execute a sua aplicação num anfitrião com uma configuração mais alta. Ou pode distribuir a carga em mais máquinas.
    * Se estiver a executar a sua aplicação no Serviço Azure Kubernetes, pode [utilizar o Azure Monitor para monitorizar a utilização do CPU](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Estrangulamento de conexão
O estrangulamento da ligação pode ocorrer devido a um limite de [ligação numa máquina hospedeira] ou à [exaustão da porta Azure SNAT (PAT).]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de ligação numa máquina hospedeira
Alguns sistemas Linux, como o Red Hat, têm um limite superior no número total de ficheiros abertos. As tomadas no Linux são implementadas como ficheiros, pelo que este número também limita o número total de ligações.
Execute o seguinte comando.

```bash
ulimit -a
```
O número de ficheiros abertos máximos permitidos, que são identificados como "nofile", tem de ser pelo menos o dobro do tamanho da piscina de ligação. Para mais informações, consulte as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)V4 da Azure Cosmos DB Java SDK v4 .

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Exaustão portuária de Azure SNAT (PAT)

Se a sua aplicação for implementada em Máquinas Virtuais Azure sem endereço IP público, por padrão as [portas Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecem ligações a qualquer ponto final fora do seu VM. O número de ligações permitidas do VM ao ponto final DB Azure Cosmos é limitado pela [configuração Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 As portas Azure SNAT são utilizadas apenas quando o seu VM tem um endereço IP privado e um processo do VM tenta ligar-se a um endereço IP público. Existem duas soluções alternativas para evitar a limitação do Azure SNAT:

* Adicione o seu ponto final de serviço Azure Cosmos DB à sub-rede da sua rede virtual Azure Virtual Machines. Para mais informações, consulte [os pontos finais do serviço Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto final de serviço está ativado, os pedidos deixaram de ser enviados de um IP público para a Azure Cosmos DB. Em vez disso, a rede virtual e a identidade da sub-rede são enviadas. Esta alteração pode resultar em quedas de firewall se apenas os IPs públicos forem permitidos. Se utilizar uma firewall, quando ativar o ponto final de serviço, adicione uma sub-rede à firewall utilizando [ACLs de rede virtual](/previous-versions/azure/virtual-network/virtual-networks-acl).
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

O SDK usa a biblioteca [Netty](https://netty.io/) IO para comunicar com a Azure Cosmos DB. O SDK tem uma API assídua e utiliza APIs IO não bloqueando da Netty. O trabalho de IO da SDK é realizado em fios IO Netty. O número de fios IO Netty está configurado para ser o mesmo que o número de núcleos de CPU da máquina de aplicações. 

Os fios Netty IO destinam-se a ser utilizados apenas para trabalhos de IO Netty não bloqueados. O SDK devolve o resultado da invocação da API numa das linhas Netty IO ao código da aplicação. Se a aplicação realizar uma operação de longa duração depois de receber resultados na linha Netty, o SDK pode não ter fios IO suficientes para realizar o seu trabalho interno de IO. Tal codificação de aplicações pode resultar em baixa produção, alta latência e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução é mudar o fio quando souber que a operação leva tempo.

Por exemplo, veja o seguinte corte de código que adiciona itens a um contentor (procure [aqui](create-sql-api-java.md) orientação sobre a criação da base de dados e do contentor.) Pode realizar um trabalho duradouro que leva mais de alguns milissegundos na linha Netty. Em caso afirmativo, pode eventualmente entrar num estado em que nenhum fio Netty IO está presente para processar o trabalho de IO. Como resultado, obtém-se uma falha de ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

A solução é mudar o fio em que executa um trabalho que leva tempo. Defina uma instância singleton do programador para a sua aplicação.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Você pode precisar de fazer um trabalho que leve tempo, por exemplo, trabalho computacionalmente pesado ou bloqueio de IO. Neste caso, mude o fio para um trabalhador fornecido pela sua `customScheduler` utilização da `.publishOn(customScheduler)` API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Ao `publishOn(customScheduler)` utilizar, desbloqueie a linha Netty IO e mude para o seu próprio fio personalizado fornecido pelo programador personalizado. Esta modificação resolve o problema. Não vais ter mais um `io.netty.handler.timeout.ReadTimeoutException` fracasso.

### <a name="request-rate-too-large"></a>Taxa de pedido demasiado grande
Esta falha é uma falha do lado do servidor. Indica que consumiu o seu rendimento. Tentar mais tarde. Se você receber esta falha com frequência, considere um aumento na produção de coleção.

* **Implementar backoff nos intervalos getRetryAfterInMilliseconds**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar para o intervalo de repetição especificado pelo servidor. Respeitar o recuo garante que passa o mínimo de tempo à espera entre as retrações.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha na ligação ao Emulador Azure Cosmos DB

O certificado HTTPS do Azure Cosmos DB é auto-assinado. Para que o SDK trabalhe com o emulador, importe o certificado de emulador para uma Loja Java TrustStore. Para mais informações, consulte [os certificados emuladores Export Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Questões de Conflito de Dependência

O Azure Cosmos DB Java SDK puxa uma série de dependências; de um modo geral, se a sua árvore de dependência do projeto incluir uma versão mais antiga de um artefacto do que a Azure Cosmos DB Java SDK depende, isso pode resultar em erros inesperados que são gerados quando executar a sua aplicação. Se está a depurar porque é que a sua aplicação inesperadamente abre uma exceção, é uma boa ideia verificar duas vezes que a sua árvore de dependência não está a puxar acidentalmente uma versão mais antiga de uma ou mais dependências da Azure Cosmos DB Java SDK.

A solução para tal problema é identificar qual das dependências do seu projeto traz na versão antiga e excluir a dependência transitiva dessa versão mais antiga, e permitir que a Azure Cosmos DB Java SDK traga a versão mais recente.

Para identificar qual das dependências do seu projeto traz uma versão mais antiga de algo de que a Azure Cosmos DB Java SDK depende, executar o seguinte comando contra o seu projeto pom.xml ficheiro:
```bash
mvn dependency:tree
```
Para mais informações, consulte o [guia da árvore de dependência de maven.](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)

Uma vez que você sabe qual a dependência do seu projeto depende de uma versão mais antiga, você pode modificar a dependência dessa lib no seu arquivo pom e excluir a dependência transitiva, seguindo o exemplo abaixo (que assume que o *núcleo do reator* é a dependência ultrapassada):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Para mais informações, consulte o [guia de dependência transitiva.](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Ativar o registo SDK do cliente

Azure Cosmos DB Java SDK v4 usa SLF4j como fachada de registo que suporta o registo em estruturas populares de registo, como log4j e logback.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

No Windows, pode executar o mesmo comando com diferentes bandeiras de argumento:
```bash
netstat -abn
```

Filtrar o resultado apenas para ligações ao ponto final Azure Cosmos DB.

O número de ligações com o ponto final do Azure Cosmos DB no `ESTABLISHED` estado não pode ser maior do que o tamanho da piscina de conexão configurada.

Muitas ligações com o ponto final do Azure Cosmos DB podem estar no `CLOSE_WAIT` estado. Pode haver mais de 1.000. Um número tão alto indica que as ligações são estabelecidas e demolidas rapidamente. Esta situação pode causar problemas. Para mais informações, consulte a secção [Questões e Soluções Alternativas.]

 <!--Anchors-->
[Problemas comuns e soluções]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de ligação numa máquina hospedeira]: #connection-limit-on-host
[Exaustão portuária de Azure SNAT (PAT)]: #snat
