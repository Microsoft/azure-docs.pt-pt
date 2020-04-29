---
title: Mergulho profundo gateway e boas práticas para Apache Hive em Azure HDInsight
description: Saiba como navegar nas melhores práticas para executar consultas da Hive sobre o gateway Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80586980"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Mergulho profundo gateway e boas práticas para Apache Hive em Azure HDInsight

O gateway Azure HDInsight (Gateway) é o frontend HTTPS para clusters HDInsight. O Gateway é responsável por: autenticação, resolução de hospedeiros, descoberta de serviços e outras funcionalidades úteis necessárias para um sistema distribuído moderno. As funcionalidades fornecidas pelo Gateway resultam em algumas despesas gerais para as quais este documento descreverá as melhores práticas de navegação. Também são discutidas técnicas de resolução de problemas de gateway.

## <a name="the-hdinsight-gateway"></a>O portal HDInsight

O portal HDInsight é a única parte de um cluster HDInsight que é acessível ao público através da internet. O serviço Gateway pode ser acedido sem `clustername-int.azurehdinsight.net` passar pela internet pública utilizando o ponto final interno do gateway. O ponto final interno do gateway permite estabelecer ligações ao serviço gateway sem sair da rede virtual do cluster. O Gateway lida com todos os pedidos enviados para o cluster, e reencaminha esses pedidos para os componentes corretos e hospedeiros de cluster.

O diagrama seguinte fornece uma ilustração áspera de como o Gateway fornece uma abstração em frente a todas as diferentes possibilidades de resolução do anfitrião dentro do HDInsight.

![Diagrama de resolução de anfitrião](./media/gateway-best-practices/host-resolution-diagram.png "Diagrama de resolução de anfitrião")

## <a name="motivation"></a>Motivação

A motivação para colocar uma porta de entrada na frente dos clusters HDInsight é fornecer uma interface para a descoberta do serviço e autenticação do utilizador. Os mecanismos de autenticação fornecidos pelo portal são especialmente relevantes para os clusters ativados pelo ESP.

Para a descoberta do serviço, a vantagem do gateway é que cada componente dentro `clustername.azurehdinsight.net/hive2`do cluster pode ser `host:port` acedido como um ponto final diferente sob o site gateway ( ), em oposição a uma multiplicidade de pares.

Para autenticação, o Gateway permite que `username:password` os utilizadores autentiem usando um par credencial. Para os clusters ativados pela ESP, esta credencial seria o nome de utilizador e a palavra-passe do utilizador. A autenticação aos clusters HDInsight através do Gateway não requer que o cliente adquira um bilhete kerberos. Uma vez que `username:password` o Gateway aceita credenciais e adquire o bilhete Kerberos do utilizador em nome do utilizador, podem ser feitas ligações seguras ao Gateway de qualquer anfitrião de clientes, incluindo clientes que se juntaram a diferentes domínios AA-DDS do que o cluster (ESP).

## <a name="best-practices"></a>Melhores práticas

O Gateway é um serviço único (carga equilibrada entre dois anfitriões) responsável pelo pedido de reencaminhamento e autenticação. O Gateway pode tornar-se um estrangulamento de entrada para consultas da Colmeia que excedam um determinado tamanho. A degradação do desempenho da consulta pode ser observada quando consultas muito grandes do **SELECT** são executadas no Gateway via ODBC ou JDBC. "Muito grande", consultas que compõem mais de 5 GB de dados através de linhas ou colunas. Esta consulta pode incluir uma longa lista de linhas e, ou uma ampla contagem de colunas.

A degradação do desempenho do Gateway em torno de consultas de grande dimensão deve-se ao facto de os dados terem de ser transferidos da loja de dados subjacente (ADLS Gen2) para: o HDInsight Hive Server, o Gateway, e, finalmente, através dos condutores da JDBC ou da ODBC para o anfitrião do cliente.

O diagrama seguinte ilustra os passos envolvidos numa consulta SELECT.

![Diagrama de resultados](./media/gateway-best-practices/result-retrieval-diagram.png "Diagrama de resultados")

Apache Hive é uma abstração relacional em cima de um sistema de ficheiros compatível com HDFS. Esta abstração significa que as declarações **SELECT** na Hive correspondem às operações **de READ** no sistema de ficheiros. As operações **DE READ** são traduzidas no esquema apropriado antes de serem comunicadas ao utilizador. A latência deste processo aumenta com o tamanho dos dados e o lúpulo total necessário para chegar ao utilizador final.

Comportamento semelhante pode ocorrer ao executar declarações **CREATE** ou **INSERT** de grandes dados, uma vez que estes comandos corresponderão às operações **de WRITE** no sistema de ficheiros subjacente. Considere escrever dados, tais como ORC cru, para o sistema de ficheiros/datalake em vez de carregá-los usando **INSERT** ou **LOAD**.

Em clusters ativados por Pack de Segurança Empresarial, as políticas de Apache Ranger suficientemente complexas podem causar um abrandamento no tempo de compilação de consultas, o que pode levar a um tempo de saída. Se um intervalo de entrada for notado num aglomerado de ESP, considere reduzir ou combinar o número de políticas de rangers.

## <a name="troubleshooting-techniques"></a>Técnicas de resolução de problemas

Existem vários locais para atenuar e compreender as questões de desempenho reunidas como parte do comportamento acima referido. Utilize a seguinte lista de verificação ao experimentar a degradação do desempenho da consulta sobre o gateway HDInsight:

* Utilize a cláusula **LIMIT** ao executar grandes consultas **SELECT.** A cláusula **LIMIT** reduzirá o total das linhas reportadas ao anfitrião do cliente. A cláusula **LIMIT** só afeta a geração de resultados e não altera o plano de consulta. Para aplicar a cláusula **LIMIT** ao plano `hive.limit.optimize.enable`de consulta, utilize a configuração . **O LIMIT** pode ser combinado com uma contrapartida utilizando o formulário de argumento **LIMIT x,y**.

* Nomeie as suas colunas de interesse ao executar consultas **SELECT** em vez de utilizar **SELECT \* **. A seleção de menos colunas reduzirá a quantidade de dados lidos.

* Tente fazer a consulta de interesse através de Apache Beeline. Se a recuperação do resultado através da Apache Beeline demorar um longo período de tempo, espere atrasos na recuperação dos mesmos resultados através de ferramentas externas.

* Teste uma consulta básica da Hive para garantir que uma ligação ao HDInsight Gateway pode ser estabelecida. Tente executar uma consulta básica a partir de duas ou mais ferramentas externas para se certificar de que nenhuma ferramenta individual está a ter problemas.

* Reveja quaisquer Alertas Apache Ambari para se certificar de que os serviços HDInsight são saudáveis. Os Alertas Ambari podem ser integrados com o Monitor Azure para reporte e análise.

* Se estiver a utilizar uma Metastore de Colmeia externa, verifique se o DTU DB Azure SQL para a Metaloja da Hive não atingiu o seu limite. Se o DTU se aproximar do seu limite, terá de aumentar o tamanho da base de dados.

* Certifique-se de que quaisquer ferramentas de terceiros, como PBI ou Tableau, estão a usar paginação para visualizar tabelas ou bases de dados. Consulte os seus parceiros de apoio para estas ferramentas de assistência na paginação. A ferramenta principal utilizada para a paginação é o parâmetro JDBC. `fetchSize` Um pequeno tamanho de busca pode resultar em desempenho de gateway degradado, mas um tamanho de busca muito grande pode resultar em um tempo de entrada. A afinação do tamanho da busca deve ser feita numa base de carga de trabalho.

* Se o seu pipeline de dados envolver a leitura de uma grande quantidade de dados do armazenamento subjacente do cluster HDInsight, considere utilizar uma ferramenta que intereça diretamente com o Armazenamento Azure, como a Azure Data Factory

* Considere utilizar a Apache Hive LLAP ao executar cargas de trabalho interativas, uma vez que llap pode fornecer uma experiência mais suave para resultados de consulta de retorno rápido

* Considere aumentar o número de fios disponíveis para `hive.server2.thrift.max.worker.threads`o serviço Hive Metastore utilizando . Esta definição é especialmente relevante quando um elevado número de utilizadores simultâneos estão a submeter consultas ao cluster

* Reduza o número de repetições utilizadas para chegar ao Gateway a partir de quaisquer ferramentas externas. Se várias tentativas forem usadas, considere seguir uma política exponencial de back off back off

* Considere permitir a compressão da `hive.exec.compress.output` Colmeia `hive.exec.compress.intermediate`utilizando as configurações e .

## <a name="next-steps"></a>Passos seguintes

* [Apache Beeline em HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Passos de resolução de problemas de gateway HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Redes Virtuais para HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight com rota expressa](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)