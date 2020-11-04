---
title: Tutorial - Spark & Hive Tools for VSCode (aplicação Spark)
description: Tutorial - Utilize as Ferramentas de Colmeia spark & para VSCode para desenvolver aplicações Spark, que são escritas em Python, e submetê-las a uma piscina Apache Spark sem servidor (pré-visualização).
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: 99b2b04d0f29d92b503cc0bed2460b79cfa6c354
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315654"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Tutorial: Criar aplicações Apache Spark com VSCode utilizando um espaço de trabalho Synapse

Aprenda a usar Apache Spark & Hive Tools para Código de Estúdio Visual. Use as ferramentas para criar e submeter trabalhos de lote apache Hive, consultas interativas de Hive e scripts PySpark para Apache Spark. Primeiro, descreveremos como instalar o Spark & Hive Tools no Código do Estúdio Visual. Depois vamos passar por como submeter empregos à Spark & Hive Tools.

Spark & Hive Tools podem ser instalados em plataformas que são suportadas pelo Código do Estúdio Visual. Note os seguintes pré-requisitos para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes itens para completar as etapas deste artigo:

- Uma piscina Apache Spark sem servidor. Para criar uma piscina Apache Spark sem servidor, consulte [a piscina Create Apache Spark utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono.](https://www.mono-project.com/docs/getting-started/install/) Mono é necessário apenas para Linux e macOS.
- [Um ambiente interativo PySpark para Código de Estúdio Visual.](../../hdinsight/set-up-pyspark-interactive-environment.md)
- Um diretório local. Este artigo utiliza  **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Instalar ferramentas de colmeia & faísca

Depois de conhecer os pré-requisitos, pode instalar Spark & Hive Tools for Visual Studio Code seguindo estes passos:

1. Abra o Visual Studio Code.

2. A partir da barra de menu, navegue para **ver**  >  **extensões.**

3. Na caixa de pesquisa, **insira Spark & Hive**.

4. Selecione **Spark & Hive Tools** a partir dos resultados da pesquisa e, em seguida, selecione **Instalar** :

     ![Spark & Hive para instalação visual Studio Code Python](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Selecione **Recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abrir uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um ficheiro no Código do Estúdio Visual, siga estes passos:

1. A partir da barra de menu, navegue para **a** pasta  >  **'Arquivo' ...**  >  **C:\HD\Synaseexample** , e, em seguida, selecione o botão **Select Folder.** A pasta aparece na vista **Explorer** à esquerda.

2. Na visualização **do Explorer,** selecione a pasta **Synaseexample** e, em seguida, selecione o ícone **Novo Ficheiro** ao lado da pasta de trabalho:

     ![ícone de arquivo novo código de estúdio visual](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Nomeie o novo ficheiro utilizando a extensão do `.py` ficheiro (Script Spark). Este exemplo utiliza **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Conecte-se às suas piscinas Spark

Inscreva-se na subscrição da Azure para ligar às suas piscinas Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua assinatura Azure

Siga estes passos para ligar a Azure:

1. A partir da barra de menu, navegue para **ver**  >  **paleta de comando...** e insira **Azure: Iniciar Sedura** :

     ![Ferramentas de hive spark & para login visual studio code](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de inscrição para iniciar seduca. Depois de conectado, o nome da sua conta Azure aparece na barra de estado na parte inferior da janela Visual Studio Code.

## <a name="set-the-default-spark-pool"></a>Desaperte a piscina de faíscas predefinido

1. Reabra a pasta **Synaseexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Selecione o ficheiro **HelloWorld.py** que foi criado [anteriormente](#open-a-work-folder). Abre no editor de guiões.

3. Clique com o botão direito no editor de script e, em seguida, **selecione Synapse: set default Spark pool**.  

4. [Ligue-se](#connect-to-your-spark-pools) à sua conta Azure se ainda não o fez.

5. Selecione uma piscina Spark como a piscina padrão spark para o ficheiro de script atual. As ferramentas atualizam automaticamente o **.VSCode\settings.jsno** ficheiro de configuração:

     ![Definir configuração de cluster padrão](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Submeta consultas interativas synapse PySpark para a piscina Spark

Os utilizadores podem realizar a interativa Synapse PySpark na piscina Spark das seguintes formas:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Utilizando o comando interativo Synapse PySpark em ficheiro PY
Utilizando o comando interativo PySpark para submeter as consultas, siga estes passos:

1. Reabra a pasta **Synaseexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Crie um novo ficheiro **HelloWorld.py,** seguindo os passos [anteriores.](#open-a-work-folder)

3. Copiar e colar o seguinte código no ficheiro do script:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. O pedido de instalação do núcleo PySpark/Synapse Pyspark é apresentado no canto inferior direito da janela. Pode clicar no botão **Instalar** para proceder às instalações PySpark/Synapse Pyspark; ou clique no botão **Saltar** para saltar este passo.

     ![instalar o núcleo de pyspark](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Se precisar de o instalar mais tarde, pode navegar para definições de preferência de **ficheiro**  >  **Preference**  >  , em seguida, **desmarque** **a visão Hdin: Ative a instalação Skip Pyspark** nas definições. 
    
     ![permitir a instalação de pyspark](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Se a instalação for bem sucedida no passo 4, a caixa de mensagens "PySpark/Synapse Pyspark instalada com sucesso" é exibida no canto inferior direito da janela. Clique no botão **Recarregar** para recarregar a janela.

     ![pyspark instalado com sucesso](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. A partir da barra de menu, navegue para **ver**  >  **paleta de comando...** ou use o atalho de teclado **Shift + Ctrl + P** e **introduza Python: Selecione Intérprete para iniciar o Jupyter Server**.

     ![selecione intérprete para iniciar o servidor jupyter](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Selecione a opção python abaixo.

     ![escolher a opção abaixo](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. A partir da barra de menu, navegue para **ver**  >  **paleta de comando...** ou use o atalho de teclado **Shift + Ctrl + P** e **introduza Developer: Reload Window**.

     ![janela de recarga](./media/vscode-tool-synapse/reload-window.png)

10. [Ligue-se](#connect-to-your-spark-pools) à sua conta Azure se ainda não o fez.

11. Selecione todo o código, clique com o direito no editor de scripts e **selecione Synapse: Pyspark Interactive** para submeter a consulta. 

     ![menu de contexto interativo pyspark](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Selecione a piscina Spark, se não tiver especificado uma piscina spark predefinida. Após alguns momentos, os resultados da **Python Interactive** aparecem num novo separador. Clique no PySpark para mudar o núcleo para **Synapse PySpark** , em seguida, submeter novamente o código selecionado e o código será executado com sucesso. As ferramentas também permitem submeter um bloco de código em vez de todo o ficheiro de script, utilizando o menu de contexto:

     ![interativo](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Realize consulta interativa em ficheiro PY usando um comentário #%%

1. Adicione **#%%** antes do código para obter experiência de caderno.

     ![adicionar #%%](./media/vscode-tool-synapse/run-cell.png)

2. Clique em **Run Cell**. Após alguns momentos, os resultados da Python Interactive aparecem num novo separador. Clique no PySpark para mudar o núcleo para **Synapse PySpark** , em seguida, clique novamente em **Run Cell** e o código será executado com sucesso. 

     ![executar resultados celulares](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Alavancagem suporte IPYNB da extensão Python

1. Pode criar um Bloco de Notas Jupyter por comando a partir da Paleta de Comando ou criando um novo ficheiro .ipynb no seu espaço de trabalho. Para mais informações, consulte [Trabalhar com cadernos Jupyter no Código do Estúdio Visual](https://code.visualstudio.com/docs/python/jupyter-support)

2. Clique no botão **de célula Executar,** siga as instruções para **definir a piscina de faíscas predefinitivamente** (encoraje fortemente a definir o cluster/piscina predefinido sempre antes de abrir um portátil) e, em seguida, **recarregar** a janela.

     ![definir a piscina de faísca padrão e recarregar](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Clique no PySpark para mudar o kernel para **Synapse Pyspark** , e, em seguida, clique em **Run Cell** , depois de um tempo, o resultado será exibido.

     ![executar resultados ipynb](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. A versão >da Senhora Deputada =2020.5.78807 não é apoiada nesta extensão é uma [questão conhecida.](#known-issues)
>  
>2. Mude para o núcleo de Synapse Pyspark, desativando as definições automáticas no Portal Azure é encorajado. Caso contrário, pode demorar muito tempo a acordar o cluster e a definir o núcleo de sinapse pela primeira vez. 
>
>    ![definições automáticas](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Submeta o trabalho de lote PySpark para a piscina Spark

1. Reabra a pasta **Synaseexample** que discutiu [anteriormente,](#open-a-work-folder)se fechado.  

2. Crie um novo ficheiro **BatchFile.py** seguindo os passos [anteriores.](#open-a-work-folder)

3. Copiar e colar o seguinte código no ficheiro do script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Ligue-se](#connect-to-your-spark-pools) à sua conta Azure se ainda não o fez.

5. Clique com o botão direito no editor de script e, em seguida, **selecione Synapse: PySpark Batch**.

6. Selecione uma piscina Spark para submeter o seu trabalho PySpark para:

     ![Submeter saída de resultado de trabalho python](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Depois de submeter um trabalho de lote para faíscar piscina, os registos de submissão aparecem na janela **OUTPUT** no Código do Estúdio Visual. O **URL spark UI** e **o URL de aplicação de emprego de faísca** também são mostrados. Pode abrir o URL num navegador web para rastrear o estado do trabalho.

## <a name="access-and-manage-synapse-workspace"></a>Acesso e gestão do Espaço de Trabalho Da Sinapse

Pode executar diferentes operações no Azure Explorer dentro de ferramentas Spark & Hive para VSCode. Do Explorador Azure.

![imagem azul](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Espaço de trabalho de lançamento

1. Do Azure Explorer, navegue até **à SYNAPSE,** expanda-o e apresente a lista de Assinaturas Synapse.

     ![explorador de sinapse](./media/vscode-tool-synapse/synapse-explorer.png)

2. Clique na Subscrição do espaço de trabalho synapse, expanda-o e exiba a lista de espaço de trabalho.

3. Clique com o botão direito num espaço de trabalho e, em seguida, selecione **Ver aplicações Apache Spark** , a página de aplicação Apache Spark no site do Synapse Studio será aberta.

     ![clique direito no espaço de trabalho](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![aplicação de estúdio sinapse](./media/vscode-tool-synapse/synapse-studio-application.png)

4. São apresentados espaços de trabalho, **armazenamento predefinido** e **piscinas de faíscas.**

5. Clique com o botão direito no **Armazenamento Predefinido,** o **Copy Full Path** e Open in **Synapse Studio são apresentados.** 

     ![clique direito no armazenamento predefinido](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Clique em **Copy Full Path** , o URL de conta ADLS Gen2 primário será copiado, pode colar onde precisar。

     - Clique **em Open in Synapse Studio,** a Conta de Armazenamento Primário será aberta no Synapse Studio.

     ![armazenamento padrão no estúdio sinapse](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Expanda o **Armazenamento Predefinido,** é apresentada a Conta de Armazenamento Primário.

7. Expanda as **Piscinas Spark,** todas as piscinas de faíscas no espaço de trabalho são exibidas.

     ![expandir piscina de armazenamento](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Problemas conhecidos

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>ms-python >=versão 2020.5.78807 não é suportado nesta extensão 

"Falhou em ligar-me ao caderno do Jupyter." é uma edição conhecida para a versão python >=2020.5.78807. Recomenda-se que os utilizadores utilizem a versão **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** da ms-python para evitar este problema.

![questões conhecidas](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](../overview-what-is.md)
- [Crie uma nova piscina Apache Spark para um espaço de trabalho Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
