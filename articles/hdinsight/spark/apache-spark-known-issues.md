---
title: Problemas de resolução de problemas com o cluster Apache Spark em Azure HDInsight
description: Conheça as questões relacionadas com os clusters Apache Spark em Azure HDInsight e como trabalhar em torno deles.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 65163225853037ac4cb97a02c0bdaf554b509fb1
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822204"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos para o cluster Apache Spark em HDInsight

Este documento acompanha todos os problemas conhecidos para a pré-visualização pública do HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy vaza sessão interativa
Quando [a Apache Livy](https://livy.incubator.apache.org/) recomeçar (de [Apache Ambari](https://ambari.apache.org/) ou por causa do reboot da máquina virtual headnode 0) com uma sessão interativa ainda viva, uma sessão de trabalho interativa é vazada. Como resultado, novos empregos podem ficar presos no Estado Aceite.

**Mitigação:**

Utilize o seguinte procedimento para contornar a questão:

1. Ssh em cabeçanode. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Executar o seguinte comando para encontrar as identificações de aplicação dos trabalhos interativos iniciados através da Livy.

   ```bash
   yarn application –list
   ```

    Os nomes de emprego predefinidos serão Livy se os trabalhos forem iniciados com uma sessão interativa livy sem nomes explícitos especificados. Para a sessão Livy iniciada por [Jupyter Notebook,](https://jupyter.org/)o nome do trabalho começa por `remotesparkmagics_*` .

3. Executar o seguinte comando para matar aqueles trabalhos.

   ```bash
   yarn application –kill <Application ID>
   ```

Novos empregos começam a funcionar.

## <a name="spark-history-server-not-started"></a>Spark History Server ainda não começou
O Spark History Server não é iniciado automaticamente após a criação de um cluster.  

**Mitigação:**

Inicie manualmente o servidor de história de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Emissão de permissão no diretório de registo spark
hdiuser obtém o seguinte erro ao submeter um trabalho usando spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

E nenhum registo de motorista está escrito.

**Mitigação:**

1. Adicione hdiuser ao grupo Hadoop.
2. Fornecer 777 permissões em /var/log/faísca após a criação do cluster.
3. Atualize a localização do registo de faíscas usando Ambari para ser um diretório com 777 permissões.  
4. Executar spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix conector não é suportado

Os clusters HDInsight Spark não suportam o conector Spark-Phoenix.

**Mitigação:**

Em vez disso, deve utilizar o conector Spark-HBase. Para obter as instruções, consulte [como utilizar Spark-HBase conector](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Questões relacionadas com cadernos jupyter

Seguem-se algumas questões conhecidas relacionadas com os Cadernos Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Cadernos com caracteres não ASCII em apelidos de ficheiros

Não utilize caracteres não ASCII em ficheiros Jupyter Notebook. Se tentar fazer o upload de um ficheiro através do Jupyter UI, que tem um nome de ficheiro não ASCII, falha sem qualquer mensagem de erro. O Jupyter não te deixa carregar o ficheiro, mas também não lança um erro visível.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar cadernos de tamanhos maiores

Pode ver um erro **`Error loading notebook`** quando carregar cadernos com tamanho maior.  

**Mitigação:**

Se cometer este erro, não significa que os seus dados são corruptos ou perdidos.  Os seus cadernos ainda estão no `/var/lib/jupyter` disco, e pode entrar no cluster para aceder aos mesmos. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez ligado ao cluster utilizando o SSH, pode copiar os seus cadernos do seu cluster para a sua máquina local (utilizando o SCP ou o WinSCP) como uma cópia de segurança para evitar a perda de quaisquer dados importantes no caderno. Em seguida, pode entrar no túnel SSH na sua cabeçanode no porto 8001 para aceder a Jupyter sem passar pelo portal.  A partir daí, pode limpar a saída do seu caderno e resserí-lo para minimizar o tamanho do caderno.

Para evitar que este erro aconteça no futuro, deve seguir algumas boas práticas:

* É importante manter o tamanho do caderno pequeno. Qualquer produção dos seus trabalhos de Faísca que seja enviada de volta para Jupyter é persistiu no caderno.  É uma boa prática com a Jupyter em geral para evitar correr `.collect()` em grandes rdd's ou dataframes; em vez disso, se quiser espreitar o conteúdo de um RDD, considere correr `.take()` ou para que a sua saída não fique muito `.sample()` grande.
* Além disso, quando guardar um caderno, limpe todas as células de saída para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Startup inicial de blocos de notas demora mais do que o esperado

A primeira declaração de código no Jupyter Notebook usando magia Spark pode demorar mais de um minuto.  

**Explicação:**

Isto acontece porque quando a primeira célula de código é executada. No fundo, esta configuração de sessão inicia e os contextos Spark, SQL e Hive são definidos. Depois de definidos estes contextos, a primeira declaração é executada e isso dá a impressão de que a declaração demorou muito tempo a ser concluída.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite de Jupyter Notebook na criação da sessão

Quando o cluster Spark estiver sem recursos, os kernels Spark e PySpark no Caderno Jupyter vão ficar parando para tentar criar a sessão.

**Mitigações:**

1. Liberte alguns recursos no seu cluster Spark por:

   * Parar outros cadernos Spark indo para o menu Close and Halt ou clicando em Desligar no explorador de portátil.
   * Parar outras aplicações spark da YARN.

2. Reinicie o caderno que estava a tentar ligar. Recursos suficientes devem estar disponíveis para que crie uma sessão agora.

## <a name="see-also"></a>Veja também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight Plugin para IntelliJ IDEA para depurar as aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
