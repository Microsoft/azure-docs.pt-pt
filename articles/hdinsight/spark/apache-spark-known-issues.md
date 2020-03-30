---
title: Problemas de resolução de problemas com cluster Apache Spark em Azure HDInsight
description: Conheça questões relacionadas com os clusters Apache Spark no Azure HDInsight e como trabalhar em torno deles.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494527"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Questões conhecidas para cluster Apache Spark no HDInsight

Este documento acompanha todas as questões conhecidas para a pré-visualização pública da HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy divulga sessão interativa
Quando [apache Livy](https://livy.incubator.apache.org/) recomeça (de [Apache Ambari](https://ambari.apache.org/) ou por causa do reinicialização da máquina virtual do headnode 0) com uma sessão interativa ainda viva, uma sessão de trabalho interativa é vazada. Como resultado, novos empregos podem ficar presos no Estado aceite.

**Atenuação:**

Utilize o seguinte procedimento para contornar a questão:

1. Ssh no nó de cabeça. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Executar o seguinte comando para encontrar as identificações de aplicação dos trabalhos interativos iniciados através da Livy.

        yarn application –list

    Os nomes de emprego padrão serão Livy se os trabalhos foram iniciados com uma sessão interativa da Livy sem nomes explícitos especificados. Para a sessão livy iniciada por [Jupyter Notebook,](https://jupyter.org/)o nome do trabalho começa com `remotesparkmagics_*`.

3. Mande o seguinte comando para matar os empregos.

        yarn application –kill <Application ID>

Novos empregos começam a funcionar.

## <a name="spark-history-server-not-started"></a>Spark History Server não começou
O Spark History Server não é iniciado automaticamente após a criação de um cluster.  

**Atenuação:**

Inicie manualmente o servidor de histórico de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Emissão de permissão no diretório de log Spark
Hdiuser obtém o seguinte erro ao submeter um trabalho usando spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

E nenhum registo de motorista está escrito.

**Atenuação:**

1. Adicione hdiuser ao grupo Hadoop.
2. Forneça 777 permissões em /var/log/faísca após a criação do cluster.
3. Atualize a localização do registo de faíscas usando Ambari para ser um diretório com 777 permissões.  
4. Executar spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>O conector Spark-Phoenix não é suportado

Os clusters HDInsight Spark não suportam o conector Spark-Phoenix.

**Atenuação:**

Em vez disso, deve utilizar o conector Spark-HBase. Para obter as instruções, consulte Como utilizar o [conector Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Questões relacionadas com cadernos jupyter

Seguem-se algumas questões conhecidas relacionadas com os cadernos jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Cadernos com caracteres não-ASCII em nomes de ficheiros

Não utilize caracteres não-ASCII em nomes de ficheiros de cadernos Jupyter. Se tentar fazer o upload de um ficheiro através do Jupyter UI, que tem um nome de ficheiro não-ASCII, falha sem qualquer mensagem de erro. Jupyter não permite carregar o ficheiro, mas também não lança um erro visível.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar cadernos de tamanhos maiores

Pode ver um **`Error loading notebook`** erro quando carrega cadernos de maior dimensão.  

**Atenuação:**

Se tiver este erro, não significa que os seus dados sejam corruptos ou perdidos.  Os seus cadernos ainda `/var/lib/jupyter`estão em disco e pode entrar no cluster para aceder aos mesmos. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez ligado ao cluster utilizando SSH, pode copiar os seus cadernos do seu cluster para a sua máquina local (utilizando SCP ou WinSCP) como cópia de segurança para evitar a perda de quaisquer dados importantes no caderno. Em seguida, você pode então túnel SSH para o seu headnode no porto 8001 para aceder a Jupyter sem passar pelo portal.  A partir daí, pode limpar a saída do seu caderno e reguardá-lo para minimizar o tamanho do caderno.

Para evitar que este erro aconteça no futuro, deve seguir algumas boas práticas:

* É importante manter o tamanho do caderno pequeno. Qualquer saída dos seus trabalhos spark que é enviado de volta para Jupyter é persistiu no caderno.  É uma boa prática com jupyter `.collect()` em geral para evitar correr em grandes RDD's ou dataframes; em vez disso, se quiser espreitar o conteúdo `.take()` de `.sample()` um DDR, considere correr ou para que a sua saída não fique muito grande.
* Além disso, quando guardar um caderno, limpe todas as células de saída para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Startup inicial do caderno demora mais tempo do que o esperado

A primeira declaração de código no caderno Jupyter usando a magia spark pode demorar mais de um minuto.  

**Explicação:**

Isto acontece porque quando a primeira célula de código é executada. Em segundo plano, esta inicia a configuração da sessão e os contextos Spark, SQL e Hive são definidos. Depois destes contextos serem definidos, a primeira declaração é executada e isso dá a impressão de que a declaração demorou muito tempo a ser completada.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo livre do caderno jupyter na criação da sessão

Quando o cluster Spark estiver sem recursos, os núcleos Spark e PySpark no caderno Jupyter vão cronometrar para tentar criar a sessão.

**Atenuações:**

1. Liberte alguns recursos no seu cluster Spark por:

   * Parar outros cadernos Spark indo para o menu Close and Halt ou clicando em Shutdown no explorador de cadernos.
   * Parar outras aplicações spark da ARN.

2. Reinicie o caderno que estava a tentar ligar. Recursos suficientes devem estar disponíveis para criar uma sessão agora.

## <a name="see-also"></a>Consulte também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)