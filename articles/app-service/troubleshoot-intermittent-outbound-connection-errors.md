---
title: Resolução de erros intermitentes de ligação de saída no Serviço de Aplicações Azure
description: Problemas de ligação intermitente supressor e problemas de desempenho relacionados no Serviço de Aplicações Azure
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367554"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Resolução de erros intermitentes de ligação de saída no Serviço de Aplicações Azure

Este artigo ajuda-o a resolver erros de ligação intermitentes e problemas de desempenho relacionados no Serviço de [Aplicações Azure](https://docs.microsoft.com/azure/app-service/overview). Este tópico fornecerá mais informações sobre e metodologias de resolução de problemas para, esgotamento das portas de tradução de rede de endereços de origem (SNAT). Se necessitar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas do Azure no [MSDN Azure e nos fóruns stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, apresente um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Suporte**.

## <a name="symptoms"></a>Sintomas

As aplicações e funções hospedadas no serviço Azure App podem apresentar um ou mais dos seguintes sintomas:

* Tempos de resposta lentos em todos ou alguns dos casos de um plano de serviço.
* Erros intermitentes 5xx ou **Bad Gateway**
* Mensagens de erro de timeout
* Não podia ligar-se a pontos finais externos (como SQLDB, Service Fabric, outros serviços de Aplicação, etc.)

## <a name="cause"></a>Causa

Uma das principais causas destes sintomas é que a instância de aplicação não é capaz de abrir uma nova ligação ao ponto final externo porque atingiu um dos seguintes limites:

* Ligações TCP: Existe um limite para o número de ligações de saída que podem ser feitas. Isto está associado ao tamanho do trabalhador utilizado.
* Portas SNAT: Conforme discutido nas [ligações de saída em Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)o Azure utiliza a tradução de endereços de rede fonte (SNAT) e um Balancer de Carga (não exposto aos clientes) para comunicar com pontos finais fora do Azure no espaço público de endereços IP. Cada instância no serviço Azure App é inicialmente dada um número pré-atribuído de **128** portas SNAT. Este limite afeta as ligações de abertura à mesma combinação de hospedeiro e porta. Se a sua aplicação criar ligações a uma mistura de endereços e combinações de portas, não utilizará as portas SNAT. As portas SNAT são usadas quando tem chamadas repetidas para o mesmo endereço e combinação de porta. Uma vez libertada uma porta, a porta está disponível para reutilização conforme necessário. O equilibrador de carga da Rede Azure recupera a porta SNAT das ligações fechadas apenas depois de esperar 4 minutos.

Quando as aplicações ou funções abrem rapidamente uma nova ligação, podem esgotar rapidamente a sua quota pré-atribuída das 128 portas. São então bloqueados até que uma nova porta SNAT fique disponível, quer através da atribuição dinâmica de portas SNAT adicionais, quer através da reutilização de uma porta SNAT recuperada. Aplicações ou funções bloqueadas devido a esta incapacidade de criar novas ligações começarão a experimentar uma ou mais das questões descritas na secção **sintomas** deste artigo.

## <a name="avoiding-the-problem"></a>Evitando o problema

Evitar o problema da porta SNAT significa evitar a criação de novas ligações repetidamente ao mesmo hospedeiro e porto.

As estratégias gerais para atenuar a exaustão portuária sNAT são discutidas na [secção de resolução de problemas](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) das ligações de saída da documentação **Azure.** Destas estratégias, aplicam-se as seguintes aplicações e funções hospedadas no serviço Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificar a aplicação para utilizar o pooling de ligação

* Para juntar ligações HTTP, reveja [as ligações Pool HTTP com httpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Para obter informações sobre o pooling de ligação do Servidor SQL, reveja o [Pooling de Ligação do Servidor SQL (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Para implementar o agrupamento com aplicações-quadro da entidade, reveja o [agrupamento DbContext.](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)

Aqui está uma coleção de links para implementar o pool de Ligação por diferentes soluçãostack.

#### <a name="node"></a>Nó

Por defeito, as ligações para o NodeJS não são mantidas vivas. Abaixo estão as bases de dados e pacotes populares para o agrupamento de ligações que contêm exemplos de como implementá-los.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Manter-se vivo

* [agentemantervivo](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 Documentação](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Abaixo estão as bibliotecas populares utilizadas para o agrupamento de ligações JDBC que contêm exemplos de como implementá-las: JDBC Connection Pooling.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP Connection Pooling

* [Gestão de Conexão Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Class PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Embora a PHP não suporte o pool de ligação, pode tentar utilizar ligações persistentes de base de dados ao seu servidor back-end.
 
* Servidor MySQL

   * [Ligações MySQLi](https://www.php.net/manual/mysqli.quickstart.connections.php) para versões mais recentes
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) versões mais antigas de PHP

* Outras Fontes de Dados

   * [Gestão de Ligações PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [Servidor SQL](https://github.com/tediousjs/node-mssql#connection-pools) (NOTA: SQLAlchemy pode ser usado com outras bases de dados além do Servidor MicrosoftSQL)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive é automático ao utilizar [sessões de sessões de objetos de sessão).](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)

Para outros ambientes, fornecedor de revisão ou documentos específicos do condutor para a implementação do agrupamento de ligações nas suas aplicações.

### <a name="modify-the-application-to-reuse-connections"></a>Modificar a aplicação para reutilizar ligações

*  Para obter indicações adicionais e exemplos sobre a gestão de ligações em funções Azure, reveja [gerir as ligações em Funções Azure](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificar a aplicação para usar uma lógica de retry menos agressiva

* Para obter orientações e exemplos adicionais, reveja o [padrão de retry](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Use vidas de manutenção para repor o tempo de saída inativo

* Para implementar as vidas de keepalives para aplicações Node.js, reveja a minha aplicação de [nó está a fazer chamadas de saída excessivas.](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)

### <a name="additional-guidance-specific-to-app-service"></a>Orientação adicional específica ao Serviço de Aplicações:

* Um [teste de carga](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) deve simular dados do mundo real numa velocidade constante de alimentação. Testar aplicações e funções sob o stress do mundo real pode identificar e resolver problemas de exaustão da porta SNAT com antecedência.
* Certifique-se de que os serviços de back-end podem devolver respostas rapidamente. Para problemas de resolução de problemas de desempenho com base de dados Azure SQL, reveja os problemas de [desempenho da Troubleshoot Azure SQL Database com Insights Inteligentes](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Esforce o plano do Serviço de Aplicações para mais casos. Para obter mais informações sobre escala, consulte [Scale uma aplicação no Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up). Cada instância de trabalhador num plano de serviço de aplicações é atribuída a várias portas SNAT. Se espalhar o seu uso por mais casos, poderá obter o uso da porta SNAT por exemplo abaixo do limite recomendado de 100 ligações de saída, por ponto final remoto único.
* Considere mudar-se para [o App Service Environment (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)onde lhe é atribuído um único endereço IP de saída, e os limites para ligações e portas SNAT são muito mais elevados.

Evitar os limites de TCP de saída é mais fácil de resolver, uma vez que os limites são definidos pelo tamanho do seu trabalhador. Pode ver os limites em [Limites Numéricos Sandbox Cross VM - Ligações TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome-limite|Descrição|Pequeno (A1)|Médio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|Ligações|Número de ligações em toda a VM|1920|3968|8064|16 000|

Para evitar limites de TCP de saída, pode aumentar o tamanho dos seus trabalhadores ou escalar horizontalmente.

## <a name="troubleshooting"></a>Resolução de problemas

Conhecer os dois tipos de limites de ligação de saída e o que a sua aplicação faz, deve facilitar a resolução de problemas. Se souber que a sua aplicação faz muitas chamadas para a mesma conta de armazenamento, pode suspeitar de um limite de SNAT. Se a sua aplicação criar muitas chamadas para pontos finais por toda a internet, suspeita-se que está a atingir o limite de VM.

Se desconhece o comportamento da aplicação o suficiente para determinar rapidamente a causa, existem algumas ferramentas e técnicas disponíveis no Serviço de Aplicações para ajudar nessa determinação.

### <a name="find-snat-port-allocation-information"></a>Encontre informações sobre atribuição de portas SNAT

Você pode usar diagnósticos de serviço de [aplicativos](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para encontrar informações de atribuição de portas SNAT, e observar a métrica de atribuição de portas SNAT de um site de Serviço de Aplicações. Para encontrar informações sobre a atribuição de portas SNAT, siga os seguintes passos:

1. Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua aplicação web do App Service ou para o App Service Environment no [portal Azure](https://portal.azure.com/). Na navegação à esquerda, selecione **Diagnosticar e resolver problemas**.
2. Selecione Categoria de Disponibilidade e Desempenho
3. Selecione azulejos de exaustão por porta SNAT na lista de azulejos disponíveis na categoria. A prática é mantê-lo abaixo de 128.
Se precisar, ainda pode abrir um bilhete de apoio e o engenheiro de apoio receberá a métrica do back-end para si.

Note que, uma vez que o uso da porta SNAT não está disponível como métrica, não é possível nem automaticamente com base na utilização da porta SNAT, nem configurar a escala automática com base na métrica de atribuição de portas SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Ligações TCP e Portas SNAT

As ligações TCP e as portas SNAT não estão diretamente relacionadas. Um detetor de utilização de ligações TCP está incluído na lâmina de diagnóstico e resolução de problemas de qualquer site do Serviço de Aplicações. Procure a frase "ligações TCP" para encontrá-la.

* As portas SNAT são utilizadas apenas para fluxos de rede externos, enquanto as ligações tcp totais incluem ligações de retorno local.
* Uma porta SNAT pode ser partilhada por diferentes fluxos, se os fluxos forem diferentes em protocolo, endereço IP ou porta. A métrica de Ligações TCP conta todas as ligações TCP.
* O limite de ligações TCP acontece ao nível da instância do trabalhador. O equilíbrio de carga de saída da Rede Azure não utiliza a métrica de ligações TCP para a limitação da porta SNAT.
* Os limites de ligações TCP são descritos nos [limites numéricos Sandbox Cross VM - Ligações TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome-limite|Descrição|Pequeno (A1)|Médio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|Ligações|Número de ligações em toda a VM|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>WebJobs e ligações de base de dados
 
Se as portas SNAT estiverem esgotadas, onde o WebJobs não conseguir ligar-se à base de dados Azure SQL, não existe uma métrica que demonstre quantas ligações são abertas por cada processo de aplicação web individual. Para encontrar o WebJob problemático, mova vários WebJobs para outro plano de Serviço de Aplicações para ver se a situação melhora, ou se um problema permanece num dos planos. Repita o processo até encontrar o WebJob problemático.

### <a name="using-snat-ports-sooner"></a>Usando portas SNAT mais cedo

Não é possível alterar quaisquer configurações do Azure para libertar as portas SNAT usadas mais cedo, uma vez que todas as portas SNAT serão libertadas de acordo com as condições abaixo e o comportamento é por design.
 
* Se um servidor ou cliente enviar finack, a [porta SNAT será libertada](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) após 240 segundos.
* Se for visto um RST, a porta SNAT será libertada após 15 segundos.
* Se o tempo de paragem inativa tiver sido atingido, a porta é libertada.
 
## <a name="additional-information"></a>Informações adicionais

* [SNAT com Serviço de Aplicações](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Problemas de desempenho lento de aplicações no Serviço de Aplicações Azure](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
