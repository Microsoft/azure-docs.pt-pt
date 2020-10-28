---
title: Resolução de problemas erros de ligação intermitente de saída no Serviço de Aplicações Azure
description: Resolução de problemas erros de ligação intermitentes e problemas de desempenho relacionados no Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 76b4408b2f8c631453281ecf6f214d49318252a3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785056"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Resolução de problemas erros de ligação intermitente de saída no Serviço de Aplicações Azure

Este artigo ajuda-o a resolver erros de ligação intermitentes e problemas de desempenho relacionados no [Azure App Service](./overview.md). Este tópico fornecerá mais informações sobre metodologias de resolução de problemas para.exaustão das portas de tradução da rede de endereços de origem (SNAT). Se necessitar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas do Azure nos [fóruns msdn Azure e stack overflow](https://azure.microsoft.com/support/forums/). Em alternativa, apresente um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio** .

## <a name="symptoms"></a>Sintomas

As aplicações e funções hospedadas no serviço Azure App podem apresentar um ou mais dos seguintes sintomas:

* Tempos de resposta lentos em todas ou em algumas das instâncias de um plano de serviço.
* Erros intermitentes de 5xx ou **Bad Gateway**
* Mensagens de erro de tempo limite
* Não foi possível ligar-se a pontos finais externos (como SQLDB, Service Fabric, outros serviços de Aplicação, etc.)

## <a name="cause"></a>Causa

Uma das principais causas destes sintomas é que a instância de aplicação não é capaz de abrir uma nova ligação ao ponto final externo porque atingiu um dos seguintes limites:

* Ligações TCP: Existe um limite para o número de ligações de saída que podem ser feitas. Isto está associado ao tamanho do trabalhador utilizado.
* Portas SNAT: Como discutido nas [ligações outbound em Azure,](../load-balancer/load-balancer-outbound-connections.md)a Azure utiliza tradução de endereço de rede de origem (SNAT) e um Balancer de Carga (não exposto aos clientes) para comunicar com pontos finais fora de Azure no espaço de endereço IP público, bem como pontos finais internos para a Azure que não estão a tirar partido do serviço/pontos finais privados. Cada instância no serviço Azure App é inicialmente dado um número pré-atribuído de **128** portas SNAT. Este limite afeta a abertura das ligações ao mesmo hospedeiro e à combinação de porta. Se a sua aplicação criar ligações a uma mistura de endereços e combinações de portas, não utilizará as portas SNAT. As portas SNAT são usadas quando tem chamadas repetidas para o mesmo endereço e combinação de portas. Uma vez que uma porta tenha sido liberada, o porto está disponível para reutilização conforme necessário. O equilibrador de carga da Rede Azure recupera a porta SNAT das ligações fechadas apenas após esperar 4 minutos.

Quando as aplicações ou funções abrem rapidamente uma nova ligação, podem esgotar rapidamente a sua quota pré-atribuída das 128 portas. São então bloqueados até que uma nova porta SNAT fique disponível, quer através da atribuição dinâmica de portas SNAT adicionais, quer através da reutilização de uma porta SNAT recuperada. As aplicações ou funções que estão bloqueadas devido a esta incapacidade de criar novas ligações começarão a experimentar uma ou mais das questões descritas na secção **Sintomas** deste artigo.

## <a name="avoiding-the-problem"></a>Evitando o problema

Se o seu destino for um serviço Azure que suporta pontos finais de serviço, pode evitar problemas de exaustão da porta SNAT utilizando pontos finais [regionais de Integração VNet](./web-sites-integrate-with-vnet.md) e serviços ou pontos finais privados. Quando utilizar a Integração Regional vNet e colocar pontos finais de serviço na sub-rede de integração, o tráfego de saída da sua aplicação para esses serviços não terá restrições de saída da porta SNAT. Da mesma forma, se utilizar a Integração Regional de VNet e pontos finais privados, não terá quaisquer problemas de saída da porta SNAT para esse destino. 

Evitar o problema da porta SNAT significa evitar a criação de novas ligações repetidamente ao mesmo hospedeiro e porto.

As estratégias gerais para mitigar a exaustão do porto SNAT são discutidas na [secção de resolução de problemas](../load-balancer/load-balancer-outbound-connections.md) das ligações de saída da documentação **do Azure.** Destas estratégias, as seguintes aplicam-se a apps e funções hospedadas no serviço Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modifique a aplicação para utilizar o pooling de ligação

* Para reunir ligações HTTP, reveja [as ligações Pool HTTP com httpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Para obter informações sobre a piscina de conexão SQL Server, reveja o Pooling de [Conexão do Servidor SQL (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Para implementar o pooling com aplicações-quadro de entidades, reveja [o pooling DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Aqui está uma coleção de links para implementar a conexão pooling por diferentes soluções.

#### <a name="node"></a>Nó

Por predefinição, as ligações para NodeJS não são mantidas vivas. Abaixo estão as bases de dados e pacotes populares para o agrupamento de ligações que contêm exemplos para como implementá-los.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Manter-se vivo

* [agentekeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 Documentação](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Abaixo estão as bibliotecas populares utilizadas para o pooling de conexão JDBC que contêm exemplos para como implementá-las: JDBC Connection Pooling.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pooling de conexão HTTP

* [Gestão de Conexões Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Classe PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Embora o PHP não suporte o agrupamento de ligações, pode tentar utilizar ligações persistentes de base de dados ao seu servidor back-end.
 
* Servidor MySQL

   * [Conexões MySQLi para versões](https://www.php.net/manual/mysqli.quickstart.connections.php) mais recentes
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) para versões mais antigas do PHP

* Outras fontes de dados

   * [Gestão de Conexões PHP](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Modificar a aplicação para reutilizar ligações

*  Para mais ponteiros e exemplos sobre a gestão de ligações em funções Azure, [reveja Gerir as ligações em Funções Azure](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modifique a aplicação para usar lógica de repetição menos agressiva

* Para obter orientações e exemplos adicionais, reveja [o padrão de Retry](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Use keepalives para redefinir o tempo limite de saída

* Para implementar keepalives para aplicações de Node.js, [reveja a minha aplicação nó está a fazer chamadas de saída excessivas](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Orientação adicional específica para o Serviço de Aplicações:

* Um [teste de carga](/azure/devops/test/load-test/app-service-web-app-performance-test) deve simular dados do mundo real numa velocidade de alimentação constante. Testar aplicações e funções sob o stress do mundo real pode identificar e resolver problemas de exaustão portuária SNAT com antecedência.
* Certifique-se de que os serviços de back-end podem devolver rapidamente as respostas. Para resolver problemas de desempenho com a Base de Dados Azure SQL, reveja [problemas de desempenho da Base de Dados Azure SQL com Insights Inteligentes](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Dimensione o plano do Serviço de Aplicações para mais instâncias. Para obter mais informações sobre o dimensionamento, consulte [Scale uma aplicação no Azure App Service](./manage-scale-up.md). Cada instância de trabalho num plano de serviço de aplicações é atribuída a uma série de portas SNAT. Se espalhar a sua utilização em mais instâncias, poderá obter a utilização da porta SNAT por exemplo abaixo do limite recomendado de 100 ligações de saída, por ponto final remoto único.
* Considere mudar-se para [o App Service Environment (ASE),](./environment/using-an-ase.md)onde lhe é atribuído um único endereço IP de saída, e os limites para ligações e portas SNAT são muito mais elevados. Numa ASE, o número de portos SNAT por exemplo baseia-se na [tabela de pré-alocação](../load-balancer/load-balancer-outbound-connections.md#snatporttable) do balançador de carga Azure - por exemplo, um ASE com 1-50 casos de trabalhadores tem 1024 portos pré-locados por exemplo, enquanto um ASE com 51-100 casos de trabalhadores tem 512 portas pré-locadas por instância.

Evitar os limites de saída da TCP é mais fácil de resolver, uma vez que os limites são definidos pelo tamanho do seu trabalhador. Pode ver os limites em [Limites Numéricos Cross VM da Sandbox - Conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrição|Pequeno (A1)|Meio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|Ligações|Número de ligações em todo o VM|1920|3968|8064|16 000|

Para evitar limites de TCP de saída, pode aumentar o tamanho dos seus trabalhadores ou escalonar horizontalmente.

## <a name="troubleshooting"></a>Resolução de problemas

Conhecer os dois tipos de limites de ligação de saída, e o que a sua aplicação faz, deve facilitar a resolução de problemas. Se sabe que a sua aplicação faz muitas chamadas para a mesma conta de armazenamento, poderá suspeitar de um limite de SNAT. Se a sua aplicação criar muitas chamadas para pontos finais em toda a internet, suspeitaria que está a atingir o limite de VM.

Se não conhece o comportamento da aplicação o suficiente para determinar a causa rapidamente, existem algumas ferramentas e técnicas disponíveis no Serviço de Aplicações para ajudar nessa determinação.

### <a name="find-snat-port-allocation-information"></a>Encontre informações sobre atribuição de portos SNAT

Pode utilizar [o App Service Diagnostics](./overview-diagnostics.md) para encontrar informações de atribuição de portas SNAT e observar a métrica de atribuição de portas SNAT de um site do Serviço de Aplicações. Para encontrar informações sobre a atribuição de porta SNAT, siga os seguintes passos:

1. Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua aplicação web do Serviço de Aplicações ou Para o Ambiente de Serviço de Aplicações no [portal Azure.](https://portal.azure.com/) Na navegação à esquerda, selecione **Diagnosticar e resolver problemas.**
2. Selecione a categoria de Disponibilidade e Desempenho
3. Selecione azulejos de exaustão do porto SNAT na lista de azulejos disponíveis na categoria. A prática é mantê-lo abaixo dos 128.
Se precisar, ainda pode abrir um bilhete de apoio e o engenheiro de suporte receberá a métrica de trás para si.

Note que, uma vez que a utilização da porta SNAT não está disponível como métrica, não é possível fazer uma autoestama com base na utilização da porta SNAT, ou configurar a escala automática com base na métrica de atribuição de portas SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Ligações TCP e Portas SNAT

As ligações TCP e as portas SNAT não estão diretamente relacionadas. Um detetor de utilização de ligações TCP está incluído na lâmina de diagnóstico e resolução de problemas de qualquer site do Serviço de Aplicações. Procure a frase "conexões TCP" para encontrá-la.

* As portas SNAT são utilizadas apenas para fluxos de rede externos, enquanto que as ligações TCP totais incluem ligações de backback locais.
* Uma porta SNAT pode ser partilhada por fluxos diferentes, se os fluxos forem diferentes em protocolo, endereço IP ou porta. A métrica das ligações TCP conta todas as ligações TCP.
* O limite de ligações TCP acontece ao nível da instância do trabalhador. O equilíbrio de carga de saída da Rede Azure não utiliza a métrica de ligações TCP para limitar a porta SNAT.
* Os limites das ligações TCP são descritos em [Limites Numéricos VM Cross Sandbox - Conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrição|Pequeno (A1)|Meio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|Ligações|Número de ligações em todo o VM|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>Ligações WebJobs e Base de Dados
 
Se as portas SNAT estiverem esgotadas, onde os WebJobs não conseguem ligar-se à Base de Dados SQL, não existe uma métrica que mostre quantas ligações são abertas por cada processo de aplicação web individual. Para encontrar o problemático WebJob, mova vários WebJobs para outro plano de Serviço de Aplicações para ver se a situação melhora ou se um problema permanece num dos planos. Repita o processo até encontrar o problemático WebJob.

### <a name="using-snat-ports-sooner"></a>Usando portas SNAT mais cedo

Não é possível alterar as definições de Azure para libertar as portas SNAT usadas mais cedo, uma vez que todas as portas SNAT serão libertadas de acordo com as condições abaixo e o comportamento é por design.
 
* Se o servidor ou o cliente enviarem FINACK, a [porta SNAT será libertada](../load-balancer/load-balancer-outbound-connections.md) após 240 segundos.
* Se for visto um RST, a porta SNAT será libertada após 15 segundos.
* Se o tempo de 30 minutos tiver sido atingido, a porta será libertada.
 
## <a name="additional-information"></a>Informações adicionais

* [SNAT com Serviço de Aplicações](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Resolução de problemas problemas de desempenho de aplicações lentas no Azure App Service](./troubleshoot-performance-degradation.md)
