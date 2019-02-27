---
title: Ferramentas do HDInsight do Azure - configurar o ambiente interativo do PySpark para Visual Studio Code
description: Saiba como utilizar o Azure HDInsight Tools para Visual Studio Code para criar e submeter consultas e scripts.
keywords: VScode, ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interativas do Hive, Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: c5a8869b0619b7be506ead1b9d37053e9fa57bba
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880296"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para Visual Studio Code

Os passos seguintes mostram como configurar o ambiente interativo do PySpark no VS Code.

Usamos **pip do python** comando para criar o ambiente virtual no seu caminho de casa. Se quiser utilizar outra versão, terá de alterar a versão predefinida do **pip do python** comando manualmente. Mais detalhes, consulte [alternativas de atualização](https://linux.die.net/man/8/update-alternatives).

1. Instale [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Instalar o Python a partir [ https://www.python.org/downloads/ ](https://www.python.org/downloads/).
   + Instalar o pip partir [ https://pip.pypa.io/en/stable/installing ](https://pip.pypa.io/en/stable/installing/). (Se não instalado da instalação do Python)
   + Valide o Python e pip estão instalados com êxito com os comandos seguintes. (Opcional)
 
        ![Versão de pip do Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

    > [!NOTE]
    > Recomenda-se para instalar manualmente o Python em vez de utilizar a versão predefinida do MacOS.


2. Instale **virtualenv** ao executar o comando abaixo.
   
   ```
   pip install virtualenv
   ```

3. Para o Linux, instale os pacotes necessários ao executar os comandos abaixo se encontrar a mensagem de erro.
   
    ![Versão de pip do Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Reinicie o VS Code e, em seguida, voltar para o editor de script que está a executar **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passos Seguintes

### <a name="demo"></a>Demonstração
* HDInsight para o VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Utilizar o Azure Toolkit para IntelliJ para criar e submeter aplicações do Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para criar aplicações do Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter num cluster do Apache Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
