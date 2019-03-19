---
title: Utilizar o modo de exibição do Apache Ambari Tez com o HDInsight - Azure
description: Saiba como utilizar o modo de exibição do Apache Ambari Tez para depurar tarefas do Tez no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 7d8a654b6b2f429119323fa5d18eb0596f63e6f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105272"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Utilizar vistas do Apache Ambari para depurar tarefas do Apache Tez no HDInsight

O [Apache Ambari](https://ambari.apache.org/) IU da Web para o HDInsight contém um [Apache TEZ](https://tez.apache.org/) vista que pode ser utilizada para compreender e depurar tarefas do que utilizar Tez. O modo de exibição do Tez permite-lhe visualizar a tarefa como um gráfico de itens ligados, detalhar cada item e obter estatísticas e informações de registo.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões de componente de HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter passos sobre como criar um cluster, consulte [começar a utilizar o HDInsight baseado em Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Um browser moderno que suporte HTML5.

## <a name="understanding-apache-tez"></a>Noções básicas sobre o Apache Tez

Tez é uma estrutura extensível para processamento de dados no Apache Hadoop que fornece maior 1000x maiores do que o processamento de MapReduce tradicional. Para os clusters do HDInsight baseado em Linux, é o mecanismo de padrão para o Hive.

Tez cria um direcionado acíclicos Graph (DAG) que descreve a ordem das ações necessárias por tarefas. Ações individuais são chamadas de vértices e executar uma parte da tarefa geral. A execução real do trabalho descrito por um vértice é chamada de uma tarefa e pode ser distribuída por vários nós no cluster.

### <a name="understanding-the-tez-view"></a>Noções básicas sobre o modo de exibição do Tez

O modo de exibição do Tez fornece informações históricas e informações sobre processos em execução. Estas informações mostram a forma como uma tarefa é distribuída entre clusters. Também apresenta contadores utilizados por tarefas e vértices e as informações de erro relacionados com a tarefa. Ele poderá oferecer informações úteis nos seguintes cenários:

* Monitorização de execução longa processa, ver o progresso do mapa e reduzir as tarefas.
* Análise de dados históricos para processos com êxito ou falhados saber como o processamento pode ser melhorado ou por que falhou.

## <a name="generate-a-dag"></a>Gerar um DAG

O modo de exibição do Tez conterá dados somente se uma tarefa que utiliza o mecanismo de Tez está atualmente em execução ou tiver sido executada anteriormente. Consultas do Hive simples podem ser resolvidas sem utilizar Tez. Consulta o mais complexo do que fazer filtragem, agrupamento, pedidos, associações, etc. Utilize o mecanismo de Tez.

Utilize os seguintes passos para executar uma consulta do Hive que utiliza Tez:

1. Num browser, navegue até https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster do HDInsight.

2. No menu na parte superior da página, selecione o **vistas** ícone. Este ícone é semelhante a uma série de quadrados. Na lista pendente que é apresentada, selecione **Hive view**.

    ![Selecionar vista do Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando a vista do Hive for carregada, cole a seguinte consulta no Editor de consultas e, em seguida, clique em **executar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Depois de concluída a tarefa, deverá ver a saída exibida na **resultados do processo de consulta** secção. Os resultados devem ser semelhantes ao seguinte texto:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Selecione o **Log** separador. Verá informações semelhantes ao seguinte texto:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guardar a **id da aplicação** valor, como este valor é utilizado na secção seguinte.

## <a name="use-the-tez-view"></a>Utilize a vista do Tez

1. No menu na parte superior da página, selecione o **vistas** ícone. Na lista pendente que é apresentada, selecione **Tez vista**.

    ![Selecionar vista do Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando carrega a vista de Tez, verá uma lista de consultas do hive que estão atualmente em execução, ou que foram executados no cluster.

    ![Todos os DAGS](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Se tiver apenas uma entrada, é para a consulta que tenha sido executada na secção anterior. Se tiver várias entradas, pode pesquisar ao utilizar os campos na parte superior da página.

4. Selecione o **ID de consulta** para uma consulta do Hive. São apresentadas informações sobre a consulta.

    ![Detalhes do DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Os separadores nesta página permitem-lhe ver as seguintes informações:

   * **Detalhes das consultas**: Detalhes sobre a consulta do Hive.
   * **Linha cronológica**: Informações sobre cada estágio do processamento de quanto tempo demorou.
   * **Configurações**: A configuração utilizada para esta consulta.

     Partir __detalhes de consulta__ pode utilizar as ligações para obter informações sobre o __aplicativo__ ou o __DAG__ para esta consulta.
    
   * O __aplicativo__ ligação apresenta informações sobre a aplicação YARN para esta consulta. Aqui pode acessar os registos da aplicação YARN.
   * O __DAG__ ligação apresenta informações sobre os gráficos acíclicos direcionados para esta consulta. Aqui pode ver uma representação gráfica do DAG. Também pode encontrar informações sobre os vértices dentro do DAG.

## <a name="next-steps"></a>Próximos Passos

Agora que aprendeu como utilizar o modo de exibição do Apache Tez, saiba mais sobre [utilizando o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md).

Para obter mais informações técnicas sobre o Apache Tez, consulte a [página, Apache Tez em Hortonworks](https://hortonworks.com/hadoop/tez/).

Para obter mais informações sobre como utilizar o Apache Ambari com o HDInsight, consulte [HDInsight gerir clusters com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
