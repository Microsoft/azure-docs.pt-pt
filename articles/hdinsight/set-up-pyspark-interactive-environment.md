---
title: Ambiente interativo PySpark com Ferramentas Azure HDInsight
description: Aprenda a usar as Ferramentas Azure HDInsight para Código do Estúdio Visual para criar e submeter consultas e scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Consulta Interativa
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 14f0c7177953e43b0c58b4f5432d0c08c5f03f45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876619"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo PySpark para o Código do Estúdio Visual

Os passos a seguir mostram como configurar o ambiente interativo PySpark em VSCode. Este passo destina-se apenas a utilizadores não Windows.

Usamos o comando **python/pip** para construir um ambiente virtual no seu caminho home. Se quiser utilizar outra versão, tem de alterar manualmente a versão padrão do comando **Python/pip.** Mais detalhes ver [atualização-alternativas](https://linux.die.net/man/8/update-alternatives).

1. Instale [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).

   * Instale python de [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Instale o pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (se não for instalado a partir da instalação Python).
   * Valide que Python e pip são instalados com sucesso utilizando os seguintes comandos. (Opcional)

        ![Verifique o comando da versão python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > É aconselhável instalar manualmente o Python em vez de utilizar a versão padrão do macOS.

2. Instale **virtualenv** executando o comando abaixo.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

No Linux, se encontrar a mensagem de erro abaixo, instale as embalagens necessárias executando os dois comandos seguintes.

   ![Instale pacote libkrb5 para python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Reinicie o VSCode e, em seguida, volte ao editor VSCode e execute o comando **Spark: PySPark Interactive.**

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração

* HDInsight para vs Código: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use a ferramenta Azure HDInsight para código de estúdio visual](hdinsight-for-vscode.md)
* [Use o Azure Toolkit para o IntelliJ criar e submeter aplicações Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
