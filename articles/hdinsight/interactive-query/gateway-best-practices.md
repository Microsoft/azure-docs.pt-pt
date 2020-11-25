---
title: Gateway mergulho profundo e boas práticas para a Colmeia Apache em Azure HDInsight
description: Saiba como navegar nas melhores práticas para executar consultas de Colmeia sobre o gateway Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 3db411df69a754857220867865522f8e4fa24030
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011494"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Gateway mergulho profundo e boas práticas para a Colmeia Apache em Azure HDInsight

O gateway Azure HDInsight (Gateway) é o frontend HTTPS para clusters HDInsight. O Gateway é responsável por: autenticação, resolução de anfitriões, descoberta de serviços e outras funcionalidades úteis necessárias para um sistema distribuído moderno. As funcionalidades fornecidas pelo Gateway resultam em algumas despesas gerais para as quais este documento descreverá as melhores práticas para navegar. As técnicas de resolução de problemas do Gateway também são discutidas.

## <a name="the-hdinsight-gateway"></a>O gateway HDInsight

O gateway HDInsight é a única parte de um cluster HDInsight que é acessível ao público através da internet. O serviço Gateway pode ser acedido sem passar pela internet pública utilizando o `clustername-int.azurehdinsight.net` ponto final interno do gateway. O ponto final interno do gateway permite estabelecer ligações ao serviço gateway sem sair da rede virtual do cluster. O Gateway trata de todos os pedidos enviados para o cluster, e remete esses pedidos para os componentes corretos e anfitriões de cluster.

O diagrama seguinte fornece uma ilustração áspera de como o Gateway fornece uma abstração em frente a todas as diferentes possibilidades de resolução do anfitrião dentro do HDInsight.

![Diagrama de Resolução de Anfitriões](./media/gateway-best-practices/host-resolution-diagram.png "Diagrama de Resolução de Anfitriões")

## <a name="motivation"></a>Motivação

A motivação para colocar uma porta de entrada na frente dos clusters HDInsight é fornecer uma interface para a descoberta de serviço e autenticação do utilizador. Os mecanismos de autenticação fornecidos pelo gateway são especialmente relevantes para os clusters ativados pelo ESP.

Para a descoberta do serviço, a vantagem do gateway é que cada componente dentro do cluster pode ser acedido como ponto final diferente sob o site gateway ( `clustername.azurehdinsight.net/hive2` ), em oposição a uma infinidade de `host:port` pares.

Para a autenticação, o Gateway permite que os utilizadores autentem a autenticação utilizando um `username:password` par de credenciais. Para os clusters ativados pelo ESP, esta credencial seria o nome de utilizador e palavra-passe do utilizador. A autenticação para clusters HDInsight através do Gateway não requer que o cliente adquira um bilhete kerberos. Uma vez que o Gateway aceita `username:password` credenciais e adquire o bilhete Kerberos do utilizador em nome do utilizador, podem ser feitas ligações seguras ao Gateway a partir de qualquer anfitrião do cliente, incluindo clientes unidos a diferentes domínios AA-DDS do que o cluster (ESP).

## <a name="best-practices"></a>Melhores práticas

O Gateway é um serviço único (carga equilibrada em dois anfitriões) responsável pelo pedido de reencaminhamento e autenticação. O Gateway pode tornar-se um estrangulamento de produção para consultas de Colmeia que excedam um determinado tamanho. A degradação do desempenho da consulta pode ser observada quando consultas muito grandes **SELECT** são executadas no Gateway via ODBC ou JDBC. "Muito grande" significa consultas que compõem mais de 5 GB de dados em linhas ou colunas. Esta consulta pode incluir uma longa lista de linhas e, ou uma ampla contagem de colunas.

A degradação do desempenho do Gateway em torno de consultas de grande dimensão é porque os dados devem ser transferidos da loja de dados subjacente (ADLS Gen2) para: o HDInsight Hive Server, o Gateway e, finalmente, através dos controladores JDBC ou ODBC para o anfitrião do cliente.

O diagrama seguinte ilustra os passos envolvidos numa consulta SELECT.

![Diagrama de resultados](./media/gateway-best-practices/result-retrieval-diagram.png "Diagrama de resultados")

Apache Hive é uma abstração relacional em cima de um sistema de ficheiros compatível com HDFS. Esta abstração significa que as declarações **SELECT** na Hive correspondem às operações **de READ** no sistema de ficheiros. As operações **DE LER** são traduzidas para o esquema apropriado antes de comunicação ao utilizador. A latência deste processo aumenta com o tamanho dos dados e o lúpulo total necessário para chegar ao utilizador final.

Um comportamento semelhante pode ocorrer ao executar demonstrações **DE CRIAR** ou **INSERIR** de grandes dados, uma vez que estes comandos corresponderão às operações **WRITE** no sistema de ficheiros subjacente. Considere escrever dados, como ORC bruto, para o sistema de ficheiros/datalake em vez de os carregar utilizando **INSERT** ou **LOAD**.

Nos clusters ativados pelo Pack de Segurança empresarial, as políticas apache ranger suficientemente complexas podem causar um abrandamento no tempo de compilação de consultas, o que pode levar a um tempo limite de gateway. Se um tempo limite de gateway for notado num cluster ESP, considere reduzir ou combinar o número de políticas ranger.

## <a name="troubleshooting-techniques"></a>Técnicas de resolução de problemas

Existem vários locais para atenuar e compreender questões de desempenho satisfeitas como parte do comportamento acima. Utilize a seguinte lista de verificação ao experimentar a degradação do desempenho da consulta sobre o gateway HDInsight:

* Utilize a cláusula **LIMIT** ao executar grandes consultas **SELECT.** A cláusula **LIMIT** reduzirá as filas totais comunicadas ao anfitrião do cliente. A cláusula **LIMIT** só afeta a geração de resultados e não altera o plano de consulta. Para aplicar a cláusula **LIMIT** ao plano de consulta, utilize a configuração `hive.limit.optimize.enable` . **O LIMIT** pode ser combinado com uma compensação utilizando o formulário de argumento **LIMIT x,y**.

* Nomeie as suas colunas de interesse ao executar consultas **SELECT** em vez de utilizar **SELECT \** _. Selecionar menos colunas diminuirá a quantidade de dados lidos.

_ Tente executar a consulta de interesse através da Apache Beeline. Se a recuperação do resultado através do Apache Beeline demorar um longo período de tempo, espere atrasos na recuperação dos mesmos resultados através de ferramentas externas.

* Teste uma consulta básica da Colmeia para garantir que uma ligação ao Gateway HDInsight pode ser estabelecida. Tente executar uma consulta básica a partir de duas ou mais ferramentas externas para se certificar de que nenhuma ferramenta individual está a ter problemas.

* Reveja quaisquer Alertas Apache Ambari para se certificar de que os serviços HDInsight estão saudáveis. Os Alertas Ambari podem ser integrados com o Azure Monitor para reportagem e análise.

* Se estiver a utilizar uma Metástaria de Colmeia externa, verifique se o DTU DB DB Azure SQL para a Metástore da Colmeia não atingiu o seu limite. Se o DTU está a aproximar-se do seu limite, terás de aumentar o tamanho da base de dados.

* Certifique-se de que quaisquer ferramentas de terceiros, como PBI ou Tableau, estão a usar paginação para visualizar tabelas ou bases de dados. Consulte os seus parceiros de apoio para obter estas ferramentas para obter assistência na paginação. A ferramenta principal utilizada para a paginação é o `fetchSize` parâmetro JDBC. Um pequeno tamanho de busca pode resultar em desempenho de gateway degradado, mas um tamanho de busca demasiado grande pode resultar em um tempo limite de gateway. A afinação do tamanho da procura deve ser feita numa base de carga de trabalho.

* Se o seu pipeline de dados envolver a leitura de uma grande quantidade de dados a partir do armazenamento subjacente do cluster HDInsight, considere usar uma ferramenta que interage diretamente com o Azure Storage, como a Azure Data Factory

* Considere usar Apache Hive LLAP ao executar cargas de trabalho interativas, pois LLAP pode fornecer uma experiência mais suave para resultados de consulta de retorno rápido

* Considere aumentar o número de fios disponíveis para o serviço Hive Metastore utilizando `hive.server2.thrift.max.worker.threads` . Esta definição é especialmente relevante quando um número elevado de utilizadores simultâneos estão a enviar consultas para o cluster

* Reduza o número de retrós assim que cheguem ao Gateway a partir de ferramentas externas. Se várias retróplias forem usadas, considere seguir uma política exponencial de retrocesso

* Considere ativar a colmeia de compressão utilizando as configurações `hive.exec.compress.output` e `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Passos seguintes

* [Apache Beeline em HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Passos de resolução de problemas do gateway HDInsight](./troubleshoot-gateway-timeout.md)
* [Redes Virtuais para HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight com Rota Expressa](../connect-on-premises-network.md)