---
title: Solucionar problemas com o cluster Apache Spark no Azure HDInsight
description: Saiba mais sobre os problemas relacionados a clusters Apache Spark no Azure HDInsight e como contorná-los.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 76b4f721135c6e34eebdc20268a76e84d86b0637
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575679"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos do cluster Apache Spark no HDInsight

Este documento controla todos os problemas conhecidos da visualização pública do HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Sessão interativa de vazamentos do Apache Livy
Quando o [Apache Livy](https://livy.incubator.apache.org/) reinicia (do [Apache Ambari](https://ambari.apache.org/) ou devido à reinicialização da máquina virtual cabeçalho 0) com uma sessão interativa ainda ativa, uma sessão de trabalho interativa é vazada. Como resultado, novos trabalhos podem ficar presos no estado aceito.

**Atenuação**

Use o procedimento a seguir para contornar o problema:

1. SSH em cabeçalho. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Execute o comando a seguir para localizar as IDs de aplicativo dos trabalhos interativos iniciados por meio de Livy.

        yarn application –list

    Os nomes de trabalho padrão serão Livy se os trabalhos tiverem sido iniciados com uma sessão interativa Livy sem nomes explícitos especificados. Para a sessão Livy iniciada pelo [Jupyter Notebook](https://jupyter.org/), o nome do trabalho `remotesparkmagics_*`começa com.

3. Execute o comando a seguir para eliminar esses trabalhos.

        yarn application –kill <Application ID>

Novos trabalhos começam a ser executados.

## <a name="spark-history-server-not-started"></a>O servidor de histórico do Spark não foi iniciado
O servidor de histórico do Spark não é iniciado automaticamente após a criação de um cluster.  

**Atenuação**

Inicie manualmente o servidor de histórico do Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de log do Spark
hdiuser Obtém o seguinte erro ao enviar um trabalho usando Spark-Submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

E nenhum log de driver é gravado.

**Atenuação**

1. Adicione hdiuser ao grupo do Hadoop.
2. Forneça permissões 777 no/var/log/Spark após a criação do cluster.
3. Atualize o local do log do Spark usando Ambari para ser um diretório com permissões 777.  
4. Execute Spark-Submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Não há suporte para o conector Spark-Phoenix

Os clusters HDInsight Spark não dão suporte ao conector Spark-Phoenix.

**Atenuação**

Em vez disso, você deve usar o conector Spark-HBase. Para obter instruções, consulte [como usar o conector Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados a blocos de anotações do Jupyter

A seguir estão alguns problemas conhecidos relacionados aos notebooks Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocos de anotações com caracteres não ASCII em nomes de

Não use caracteres não ASCII em nomes de Jupyter do bloco de anotações. Se você tentar carregar um arquivo por meio da interface do usuário do amJupyter, que tem um nome de arquivo não-ASCII, ele falhará sem nenhuma mensagem de erro. O Jupyter não permite que você carregue o arquivo, mas ele também não gera um erro visível.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar blocos de anotações de tamanhos maiores

Poderá ver um erro **`Error loading notebook`** quando carrega os blocos de notas que são maiores de tamanho.  

**Atenuação**

Se você receber esse erro, isso não significará que seus dados estão corrompidos ou perdidos.  Seus blocos de anotações ainda estão no disco `/var/lib/jupyter`e você pode fazer ssh no cluster para acessá-los. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de se conectar ao cluster usando o SSH, você poderá copiar seus blocos de anotações do cluster para o computador local (usando o SCP ou WinSCP) como um backup para evitar a perda de dados importantes no bloco de anotações. Em seguida, você pode realizar o túnel SSH em seu cabeçalho na porta 8001 para acessar o Jupyter sem passar pelo Gateway.  A partir daí, você pode limpar a saída do bloco de anotações e salvá-lo novamente para minimizar o tamanho do bloco de anotações.

Para evitar que esse erro ocorra no futuro, você deve seguir algumas práticas recomendadas:

* É importante manter o tamanho do notebook pequeno. Qualquer saída de seus trabalhos do Spark que é enviada de volta para Jupyter é mantida no bloco de anotações.  Trata-se de uma prática recomendada com Jupyter em geral para `.collect()` evitar a execução em grandes RDD ou quadros de molduras; em vez disso, se você quiser inspecionar o conteúdo de `.take()` um `.sample()` RDD, considere executar ou para que a saída não fique muito grande.
* Além disso, quando você salva um bloco de anotações, limpe todas as células de saída para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>A inicialização inicial do notebook demora mais do que o esperado

A primeira instrução de código no Jupyter Notebook usando o Spark Magic pode levar mais de um minuto.  

**Explica**

Isso acontece porque quando a primeira célula de código é executada. Em segundo plano, isso inicia a configuração da sessão e os contextos Spark, SQL e Hive são definidos. Depois que esses contextos forem definidos, a primeira instrução será executada e isso dará a impressão de que a instrução demorou muito tempo para ser concluída.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite do Jupyter Notebook na criação da sessão

Quando o cluster Spark está sem recursos, os kernels Spark e PySpark no notebook Jupyter atingirão o tempo limite ao tentar criar a sessão.

**Mitigações**

1. Libere alguns recursos em seu cluster Spark:

   * Parando outros blocos de anotações do Spark, vá para o menu fechar e parar ou clique em desligar no Gerenciador de notebook.
   * Interrompendo outros aplicativos Spark do YARN.

2. Reinicie o bloco de anotações que você estava tentando iniciar. Recursos suficientes devem estar disponíveis para que você crie uma sessão agora.

## <a name="see-also"></a>Consulte também

* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)