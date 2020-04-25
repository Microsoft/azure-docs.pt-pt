---
title: Ambiente interativo PySpark com ferramentas Azure HDInsight
description: Aprenda a usar as Ferramentas Azure HDInsight para o Código do Estúdio Visual para criar e submeter consultas e scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: d9a3356ea18ccf4660d05b3fade9d9e6d6cbb5ee
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131336"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo PySpark para o Código do Estúdio Visual

Os seguintes passos mostram-lhe como configurar o ambiente interativo PySpark no Código VS.

Usamos comando **python/pip** para construir o ambiente virtual no seu caminho doméstico. Se pretender utilizar outra versão, tem de alterar manualmente a versão predefinida do comando **python/pip.** Mais detalhes ver [alternativas de atualização](https://linux.die.net/man/8/update-alternatives).

1. Instale [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).

   * Instale [https://www.python.org/downloads/](https://www.python.org/downloads/)Python a partir de .
   * Instale [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) pip a partir (se não estiver instalado a partir da instalação Python).
   * Valide que Python e pip são instalados com sucesso utilizando os seguintes comandos. (Opcional)

        ![Verifique o comando da versão python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Recomenda-se instalar manualmente python em vez de utilizar a versão padrão macOS.

2. Instale **o virtualenv** executando o comando abaixo.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

Se encontrar uma mensagem de erro, instale as embalagens necessárias executando os seguintes comandos:

   ![Instale pacote libkrb5 para python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Reiniciar o Código VS e, em seguida, voltar para o editor de scripts que está a executar **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração

* HDInsight para código VS: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize a ferramenta Azure HDInsight para código de estúdio visual](hdinsight-for-vscode.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar e submeter aplicações Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
