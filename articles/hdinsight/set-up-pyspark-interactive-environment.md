---
title: Ferramentas do Azure HDInsight – ambiente interativo do PySpark para Visual Studio Code
description: Saiba como usar as ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
keywords: VScode, ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive interativo, consulta interativa
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: d220d81b8dc57541113f7ef1e477bb77d394e206
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879284"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para Visual Studio Code

As etapas a seguir mostram como configurar o ambiente interativo do PySpark no VS Code.

Usamos o comando **Python/Pip** para criar um ambiente virtual em seu caminho inicial. Se você quiser usar outra versão, será necessário alterar manualmente a versão padrão do comando **Python/Pip** . Para obter mais detalhes [, consulte Atualização-alternativas](https://linux.die.net/man/8/update-alternatives).

1. Instale o [Python](https://www.python.org/downloads/) e o [Pip](https://pip.pypa.io/en/stable/installing/).
   
   + Instale o Python [https://www.python.org/downloads/](https://www.python.org/downloads/)do.
   + Instale o Pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) do (se ele não estiver instalado na instalação do Python).
   + Valide se o Python e o Pip foram instalados com êxito usando os comandos a seguir. Adicional
 
        ![Versão de Pip do Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > É recomendável instalar o Python manualmente em vez de usar a versão padrão do macOS.


2. Instale o **virtualenv** executando o comando a seguir.
   
   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

Se você encontrar uma mensagem de erro, instale os pacotes necessários executando os seguintes comandos:

   ![pacote libkrb5](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Reinicie vs Code e, em seguida, volte para o editor de script **que está executando o HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passos Seguintes

### <a name="demo"></a>Demonstração
* HDInsight para VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar a ferramenta Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Usar Azure Toolkit for IntelliJ para criar e enviar Apache Spark aplicativos escalares](spark/apache-spark-intellij-tool-plugin.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter notebook em um cluster Apache Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados de Apache Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utilizar o Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
