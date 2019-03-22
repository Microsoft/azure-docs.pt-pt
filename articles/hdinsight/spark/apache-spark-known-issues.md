---
title: Resolver problemas com o cluster do Apache Spark no Azure HDInsight
description: Saiba mais sobre problemas relacionados com clusters do Apache Spark no Azure HDInsight e como solucionar esses.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: fd2e253d88d6cddddd35dc75c8b746e906407cb5
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337269"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos para o cluster do Apache Spark no HDInsight

Este documento mantém um registo de todos os problemas conhecidos na pré-visualização pública do HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy vazamentos de sessão interativa
Quando [Apache Livy](https://livy.incubator.apache.org/) reinicia (partir [Apache Ambari](https://ambari.apache.org/) ou devido a reinício da máquina virtual de nó principal 0) com uma sessão interativa ainda existem, uma sessão de trabalho interativo é perdida. Como resultado, novas tarefas podem estar bloqueadas no Estado aceites.

**Atenuação:**

Utilize o procedimento seguinte para contornar o problema:

1. SSH no nó principal. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Execute o seguinte comando para localizar os IDs de aplicação das tarefas interativos iniciados por meio do Livy. 
   
        yarn application –list
   
    Os nomes de tarefa padrão será Livy se as tarefas foram iniciadas com uma sessão interativa do Livy com nenhuma explícitas nomes especificados. Para a sessão do Livy iniciada por [bloco de notas do Jupyter](https://jupyter.org/), o nome da tarefa começa com remotesparkmagics_ *. 
3. Execute o seguinte comando para eliminar essas tarefas. 
   
        yarn application –kill <Application ID>

Novas tarefas de iniciar a execução. 

## <a name="spark-history-server-not-started"></a>Não foi iniciada no servidor de histórico do Spark
Servidor de histórico do Spark não é iniciada automaticamente depois de um cluster é criado.  

**Atenuação:** 

Inicie manualmente o servidor de histórico do Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de registo do Spark
hdiuser obtém o seguinte erro ao submeter uma tarefa através do spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
E não é escrito nenhum registo de controlador. 

**Atenuação:**

1. Adicione hdiuser ao grupo de Hadoop. 
2. Fornece 777 permissões sobre /var/log/spark após a criação do cluster. 
3. Atualize a localização de registo do spark com o Ambari para ser um diretório com 777 permissões.  
4. Run spark-submit as sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Conector do Spark-Phoenix não é suportado

Os clusters do HDInsight Spark não suportam o conector do Spark-Phoenix.

**Atenuação:**

Em vez disso, tem de utilizar o conector do Spark-HBase. Para obter instruções, consulte [como utilizar o conector do Spark-HBase](https://web.archive.org/web/20190112153146/ https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados com blocos de notas do Jupyter
Seguem-se alguns problemas conhecidos relacionados com blocos de notas do Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocos de notas com caracteres não-ASCII em nomes de ficheiros
Não utilize carateres não ASCII em nomes de bloco de notas do Jupyter. Se tentar carregar um ficheiro através da IU Jupyter, que tem um nome de ficheiro não-ASCII, ocorre uma falha sem qualquer mensagem de erro. Jupyter não permitem-lhe carregar o ficheiro, mas ele não emite um erro de visível ou.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar os blocos de notas de tamanhos maiores
Poderá ver um erro **`Error loading notebook`** quando carrega os blocos de notas que são maiores de tamanho.  

**Atenuação:**

Se obtiver este erro, não significa que seus dados estão danificado ou perdido.  Os blocos de anotações são ainda em disco no `/var/lib/jupyter`, e pode encaminhar o SSH para o cluster para aceder aos mesmos. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de ter ligado ao cluster através de SSH, pode copiar seus blocos de notas do seu cluster no seu computador local (utilizando o SCP ou WinSCP) como uma cópia de segurança para evitar a perda de quaisquer dados importantes no bloco de notas. Pode, em seguida, túnel SSH no seu nó principal em porta 8001 para acessar o Jupyter sem passar através do gateway.  A partir daí, pode limpar a saída do seu bloco de notas e salvar novamente para minimizar o tamanho do bloco de notas.

Para impedir que este erro aconteça no futuro, deve seguir algumas práticas recomendadas:

* É importante manter o tamanho do bloco de notas menor. Qualquer saída de suas tarefas do Spark que é enviada de volta ao Jupyter é mantida no bloco de notas.  É melhor prática, com o Jupyter em geral para evitar `.collect()` do on grandes RDD ou dataframes; em vez disso, se quiser uma olhada no conteúdo de um RDD, considere a execução `.take()` ou `.sample()` para que sua saída não ficar demasiado grande.
* Além disso, quando guarda um bloco de notas, desmarque todos os resultados células para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Arranque inicial do bloco de notas demora mais tempo do que o esperado
Primeira instrução de código no bloco de notas do Jupyter com a mágica de Spark pode demorar mais de um minuto.  

**Explicação:**

Isso acontece porque quando a primeira célula do código é executada. Em segundo plano esta ação inicia a configuração de sessão e o Spark, o SQL e contextos de ramo de registo estão definidos. Depois desses contextos são definidos, a primeira instrução é executada, e isso dá a impressão de que a instrução demorava muito tempo a concluir.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite de bloco de notas do Jupyter na criação da sessão
Quando o cluster do Spark tem recursos insuficientes, os Spark e PySpark kernels no bloco de notas Jupyter irão tentar criar a sessão do tempo limite. 

**Atenuações:** 

1. Liberte alguns recursos no seu cluster do Spark por:
   
   * A parar outros blocos de notas do Spark vai para o menu de fechar e parar ou clicando em encerramento no Explorador do bloco de notas.
   * A parar a outras aplicações do Spark do YARN.
2. Reinicie o bloco de notas que estava a tentar iniciar. Recursos suficientes devem estar disponíveis para a criação de uma sessão agora.

## <a name="see-also"></a>Consulte também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

