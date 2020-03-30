---
title: Ambiente interativo PySpark com ferramentas Azure HDInsight
description: Aprenda a usar as Ferramentas Azure HDInsight para o Código do Estúdio Visual para criar e submeter consultas e scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73241555"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo PySpark para o Código do Estúdio Visual

Os seguintes passos mostram-lhe como configurar o ambiente interativo PySpark no Código VS.

Usamos comando **python/pip** para construir o ambiente virtual no seu caminho doméstico. Se pretender utilizar outra versão, tem de alterar manualmente a versão predefinida do comando **python/pip.** Mais detalhes ver [alternativas de atualização](https://linux.die.net/man/8/update-alternatives).

1. Instale [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).

   + Instale [https://www.python.org/downloads/](https://www.python.org/downloads/)Python a partir de .
   + Instale [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) pip a partir (se não estiver instalado a partir da instalação Python).
   + Valide que Python e pip são instalados com sucesso utilizando os seguintes comandos. (Opcional)

        ![Verifique o comando da versão python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Recomenda-se instalar manualmente python em vez de utilizar a versão padrão macOS.

2. Instale **o virtualenv** executando o comando abaixo.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

Se encontrar uma mensagem de erro, instale as embalagens necessárias executando os seguintes comandos:

   ![Instale pacote libkrb5 para python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Reiniciar o Código VS e, em seguida, voltar para o editor de scripts que está a executar **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração
* HDInsight para código VS: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilize a ferramenta Azure HDInsight para código de estúdio visual](hdinsight-for-vscode.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar e submeter aplicações Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através do SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através da VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter em um cluster Apache Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar os dados da Hive Apache com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utilizar o Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
