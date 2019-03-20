---
title: Ferramentas do HDInsight do Azure - utilizar o Visual Studio Code para Hive, LLAP ou PySpark | Documentos da Microsoft
description: Saiba como utilizar o Azure HDInsight Tools para Visual Studio Code para criar e submeter consultas e scripts.
Keywords: Visual Studio Code, ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interativas do Hive, Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 71e12e661c704af028ac4dc48f255bdee980619c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100331"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Utilizar ferramentas do Azure HDInsight para Visual Studio Code

Saiba como utilizar o Azure HDInsight Tools para Visual Studio Code para criar e submeter tarefas de lote do Apache Hive, consultas interativas do Hive e PySpark scripts para o Apache Spark. Em primeiro lugar, descreveremos como instalar as ferramentas do HDInsight no Visual Studio Code e, em seguida, vamos explicar como submeter tarefas do Hive e do Spark.  

Ferramentas do HDInsight do Azure pode ser instaladas em plataformas suportadas pelo Visual Studio Code, que incluem o Windows, Linux e macOS. Veja a seguir, encontrará os pré-requisitos para plataformas diferentes.


## <a name="prerequisites"></a>Pré-requisitos

Os itens seguintes são necessários para concluir os passos neste artigo:

- Um cluster do HDInsight. Para criar um cluster, veja [introdução ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e macOS.
- O [extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para Visual Studio Code.
- [Configurar o ambiente interativo do PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Um diretório local com o nome **HDexample**.  Este artigo usa **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Instalar as ferramentas do HDInsight do Azure

Depois de concluir os pré-requisitos, pode instalar o Azure HDInsight Tools para Visual Studio Code.  Conclua os seguintes passos para instalar as ferramentas do Azure HDInsight:

1. Abra o Visual Studio Code.

2. A partir da barra de menus, navegue para **View** > **extensões**.

3. Na caixa de pesquisa, introduza **HDInsight**.

4. Selecione **ferramentas do Azure HDInsight** partir da linha de resultados da pesquisa e, em seguida, selecione **instalar**.  

   ![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **recarregar** para ativar a **Azure HDInsight Tools** extensão após a instalação.


## <a name="open-hdinsight-work-folder"></a>Abrir pasta de trabalho do HDInsight

Conclua os seguintes passos para abrir uma pasta de trabalho e crie um ficheiro no Visual Studio Code:

1. A partir da barra de menus, navegue para **arquivo** > **Abrir pasta...**   >  **C:\HD\HDexample**, em seguida, selecione o **selecionar pasta** botão. A pasta é apresentado na **Explorer** vista à esquerda.

2. Do **Explorer** ver, selecione a pasta **HDexample**e, em seguida, o **novo ficheiro** ícone ao lado da pasta de trabalho.

   ![Novo ficheiro](./media/hdinsight-for-vscode/new-file.png)

3. Nomeie o novo arquivo com o .hql (consultas do Hive) ou a extensão de ficheiro. PY (script do Spark).  Este exemplo utiliza **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Ligar ao Cluster do HDInsight

Antes de pode enviar scripts para clusters do HDInsight a partir do código do Visual Studio, precisa ligar à sua conta do Azure ou ligar um cluster (com o Ambari nome de utilizador/palavra-passe ou domínio associado a um conta).  Conclua os seguintes passos para ligar ao Azure:

1. Na barra de menus, navegue até **View** > **paleta de comandos...** e introduza **HDInsight: Início de sessão**.

    ![Ferramentas do HDInsight para início de sessão do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de início de sessão no **saída** painel.
    + Para o ambiente global do Azure, **HDInsight: Início de sessão** comando irá acionar **iniciar sessão no Azure** ação no Explorador do HDInsight e vice-versa.

        ![Iniciar sessão nas instruções para o azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Para outros ambientes, siga as instruções de início de sessão.

        ![Inicie sessão no instruções para outro ambiente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Quando estiver ligado, o nome da sua conta do Azure é mostrado na barra de estado no canto inferior esquerdo da janela do Visual Studio Code.  
  

<h2 id="linkcluster">Crie ligação: Azure HDInsight</h2>

Pode ligar um cluster normal, utilizando uma [Apache Ambari](https://ambari.apache.org/) geridos pelo nome de utilizador ou ao ligar a um cluster de Hadoop seguro do pacote de segurança de Enterprise utilizando um nome de utilizador de domínio (como: user1@contoso.com).

1. Na barra de menus, navegue até **View** > **paleta de comandos...** e introduza **HDInsight: Ligar um Cluster**.

   ![comando de cluster de ligação](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecionar tipo de cluster ligado **do Azure HDInsight**.

3. Introduza o URL de cluster do HDInsight.

4. Introduza o nome de utilizador do Ambari, a predefinição é **administrador**.

5. Introduza a palavra-passe Ambari.

6. Selecione o tipo de cluster.

7. Revisão **saída** vista para verificação.

   > [!NOTE]  
   > O nome de utilizador ligado e a palavra-passe são utilizados se o cluster tanto registado na subscrição do Azure e ligadas um cluster.  


## <a name="create-link-generic-livy-endpoint"></a>Crie ligação: Ponto final do Livy genérico

1. Na barra de menus, navegue até **View** > **paleta de comandos...** e introduza **HDInsight: Ligar um Cluster**.

2. Selecionar tipo de cluster ligado **ponto de extremidade genérico do Livy**.

3. Introduza o ponto de extremidade genérico do Livy, por exemplo: http\:/ / 10.172.41.42:18080.

4. Selecione o tipo de autorização **básica** ou **nenhum**.  Se **básica**, em seguida:  
    &emsp;a. Introduza o nome de utilizador do Ambari, a predefinição é **administrador**.  
    &emsp;b. Introduza a palavra-passe Ambari.

5. Revisão **saída** vista para verificação.

## <a name="list-hdinsight-clusters"></a>Listar clusters do HDInsight

1. Na barra de menus, navegue até **View** > **paleta de comandos...** e introduza **HDInsight: Listar clusters**.

2. Selecione a subscrição pretendida.

3. Reveja os **saída** vista.  A vista mostrará os seus clusters do ligado e todos os clusters na sua subscrição do Azure.

    ![Definir uma configuração de cluster predefinido](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Conjunto predefinido do cluster

1. Volte a abrir a pasta **HDexample** criada [anteriores](#open-hdinsight-work-folder) se fechado.  

2. Selecione o ficheiro **HelloWorld.hql** criada [anteriores](#open-hdinsight-work-folder) e ele será aberto no editor de script.

3. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

4. O editor de scripts com o botão direito e selecione **HDInsight: Conjunto predefinido do Cluster**.  

5. Selecione um cluster do cluster predefinido para o ficheiro de script atual. As ferramentas de atualizar automaticamente o ficheiro de configuração **. VSCode\settings.json**. 

   ![Configuração de cluster de predefinida do conjunto](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Defina o ambiente do Azure

1. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

2. Na barra de menus, navegue até **View** > **paleta de comandos...** e introduza **HDInsight: Definir o ambiente do Azure**.

3. Selecione um ambiente como sua participação de início de sessão predefinido.

4. Enquanto isso, a ferramenta já salvou sua entrada de início de sessão predefinida na **. VSCode\settings.json**. Pode também atualizá-la diretamente nesse arquivo de configuração. 

   ![Configuração de entrada de início de sessão de predefinida do conjunto](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Submeter consultas interativas do Hive, scripts em lote de ramo de registo

Com as ferramentas do HDInsight para Visual Studio Code, pode submeter consultas interativas do Hive e scripts em lote o Hive para clusters do HDInsight.

1. Volte a abrir a pasta **HDexample** criada [anteriores](#open-hdinsight-work-folder) se fechado.  

2. Selecione o ficheiro **HelloWorld.hql** criada [anteriores](#open-hdinsight-work-folder) e ele será aberto no editor de script.

3. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

4. Copie e cole o seguinte código para o ficheiro do Hive e guarde-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. O editor de scripts com o botão direito, selecione **HDInsight: Hive interativo** para submeter a consulta ou utilize o atalho **Ctrl + Alt + I**.  Selecione **HDInsight: Ramo de registo de lote** para submeter o script, ou utilize o atalho **Ctrl + Alt + H**.  

6. Selecione o cluster se ainda não especificou um cluster de predefinição. As ferramentas permitem-lhe também submeter um bloco de código em vez de todo o ficheiro de script através do menu de contexto. Após alguns instantes, os resultados de consulta são apresentados num novo separador.

   ![Resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTADOS** painel: Pode guardar o resultado inteiro como ficheiro CSV, JSON ou o Excel para o caminho local ou, basta selecionar várias linhas.

    - **MENSAGENS** painel: Quando seleciona **linha** número, ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Submeter consultas interativas do PySpark

1. Volte a abrir a pasta **HDexample** criada [anteriores](#open-hdinsight-work-folder) se fechado.  

2. Criar um novo arquivo **HelloWorld.py** seguintes a [anteriores](#open-hdinsight-work-folder) passos.

3. Receberá uma caixa de diálogo de recomendação de extensão Python se não tiver instalado o Python para o pré-requisito.  Instalar e recarregar o Visual Studio Code, para concluir a instalação.

    >![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

5. Copie e cole o seguinte código para o ficheiro de script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

6. O editor de scripts com o botão direito, selecione **HDInsight: Interativo do PySpark** para submeter a consulta ou utilize o atalho **Ctrl + Alt + I**.  

7. Selecione o cluster se ainda não especificou um cluster de predefinição. As ferramentas permitem-lhe também submeter um bloco de código em vez de todo o ficheiro de script através do menu de contexto. Após alguns instantes, os resultados de consulta são apresentados num novo separador.

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. A ferramenta também suporta o **cláusula SQL** consulta.

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) o estado da submissão é apresentada à esquerda do estado na parte inferior da barra quando estiver a executar consultas. Não se submeta outras consultas quando o estado é **Kernel do PySpark (ocupado)**.  

>[!NOTE]  
>Os clusters podem manter informações de sessão. A variável definida, a função e a valores correspondentes são mantidas na sessão, pelo que pode ser referenciadas em várias chamadas de serviço para o mesmo cluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 não é suportado com Spark2.2/2.3

PySpark3 já não é suportado com o cluster do Spark 2.2 e do cluster de Spark2.3, apenas "PySpark" é suportado para o Python. Problema que envia para o spark 2.2/2.3 falhar com Python3 é conhecido.

   ![Submeter para erro de obtenção de python3](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Siga os passos para utilizar Python2.x: 

1. Instale o Python 2.7 para o computador local e adicioná-lo ao caminho do sistema.

2. Reinicie o Visual Studio Code.

3. Mudar para o Python 2 ao clicar no **Python XXX** o estado da barra, em seguida, escolha o destino de Python.

   ![Selecione a versão do python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Submeter a tarefa de lote do PySpark

1. Volte a abrir a pasta **HDexample** criada [anteriores](#open-hdinsight-work-folder) se fechado.  

2. Criar um novo arquivo **BatchFile.py** seguintes a [anteriores](#open-hdinsight-work-folder) passos.

3. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

4. Copie e cole o seguinte código para o ficheiro de script:

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

4. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: PySpark Batch**, ou utilize o atalho **Ctrl + Alt + H**. 

5. Selecione um cluster ao qual pretende submeter a tarefa de PySpark. 

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de submeter um trabalho do Python, registos de submissão constar da **saída** janela no Visual Studio Code. O **URL de interface do Usuário do Spark** e **URL de IU do Yarn** são mostrados também. É possível abrir o URL num navegador da web para controlar o estado da tarefa.

## <a name="apache-livy-configuration"></a>Configuração de Apache Livy

[Apache Livy](https://livy.incubator.apache.org/) a configuração é suportada, podem ser definido ao **. VSCode\settings.json** na pasta de espaço de trabalho. Atualmente, a configuração do livy só suporta script de Python. Obter mais detalhes, veja [Leiame do Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como acionar a configuração do livy**

Método 1  
1. A partir da barra de menus, navegue para **arquivo** > **preferências** > **definições**.  
2. Na **definições de pesquisa** caixa de texto introduza **Sumission de tarefa HDInsight: Livy Conf**.  
3. Selecione **editar no Settings** para o resultado de pesquisa relevantes.

Método 2   
Enviar um ficheiro, observe que a pasta de .vscode foi adicionada automaticamente para a pasta de trabalho. Pode encontrar a configuração do livy clicando **.vscode\settings.json**.

+ As configurações do projeto:

    ![Configuração do Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Para as definições **driverMomory** e **executorMomry**, defina o valor com a unidade, por exemplo, 1 GB ou 1024 m. 

+ As configurações suportadas do Livy:   

    **POST /batches**   
    Corpo do Pedido

    | nome | descrição | tipo | 
    | :- | :- | :- | 
    | ficheiro | Ficheiro que contém o aplicativo para executar | caminho (obrigatório) | 
    | proxyUser | Utilizador representar ao executar a tarefa | string | 
    | className | Classe principal do aplicativo Java/Spark | string |
    | args | Argumentos de linha de comandos para a aplicação | lista de cadeias de caracteres | 
    | jars | jars a ser utilizado nesta sessão | Lista de cadeia de caracteres | 
    | pyFiles | Arquivos de Python a serem usados nesta sessão | Lista de cadeia de caracteres |
    | ficheiros | arquivos a serem usados nesta sessão | Lista de cadeia de caracteres |
    | driverMemory | Quantidade de memória a utilizar para o processo de driver | string |
    | driverCores | Número de núcleos para utilizar para o processo de driver | int |
    | executorMemory | Quantidade de memória a utilizar por processo de executor | string |
    | executorCores | Número de núcleos para utilizar para cada executor | int |
    | numExecutors | Número de executores para iniciar a esta sessão | int |
    | arquivos compactados | Arquivos compactados a ser utilizado nesta sessão | Lista de cadeia de caracteres |
    | fila | O nome da fila YARN para o qual submetido | string |
    | nome | O nome desta sessão | string |
    | Conf | Propriedades de configuração de Spark | Mapa de chave = valor |

    Corpo da Resposta   
    O objeto criado do Batch.

    | nome | descrição | tipo | 
    | :- | :- | :- | 
    | ID | O id de sessão | int | 
    | appId | O id de aplicação desta sessão |  String |
    | appInfo | As informações detalhadas de aplicação | Mapa de chave = valor |
    | registo | As linhas de registo | lista de cadeias de caracteres |
    | state |   O estado de batch | string |

>[!NOTE]
>A configuração do livy atribuído serão apresentados no painel de resultados quando submeta o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar com o Azure HDInsight a partir do Explorador

**O Azure HDInsight** foi adicionado à vista do Explorador. Pode procurar e gerir as diretamente através de clusters **do Azure HDInsight**.

1. [Ligar](#connect-to-hdinsight-cluster) à sua conta do Azure, se ainda não tiver feito isso.

2. A partir da barra de menus, navegue para **View** > **Explorer**.

3. No painel à esquerda, expanda **do AZURE HDINSIGHT**.  Os clusters (Spark, Hadoop e HBase são suportados) e de subscrições disponíveis serão listadas. 

   ![Subscrição do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expanda o cluster para ver o esquema de base de dados e tabela de metadados do hive.

   ![Cluster de Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Funcionalidades adicionais

HDInsight para Visual Studio Code suporta as seguintes funcionalidades:

- **IntelliSense, conclusão automática**. Sugestões pop-up para palavra-chave, métodos, variáveis e assim por diante. Representam os ícones de diferentes tipos de objetos diferentes.

    ![Ferramentas do HDInsight para tipos de objeto do IntelliSense de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha os erros de edição para o script do Hive.     
- **Destaques de sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar as variáveis, palavras-chave, tipo de dados, as funções e assim por diante. 

    ![Ferramentas do HDInsight para Visual Studio Code sintaxe destaques](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Desassociar o cluster

1. Na barra de menus, navegue até **View** > **paleta de comandos...** e, em seguida, introduza **HDInsight: Desassociar um Cluster**.  

2. Selecione o cluster ao desassociar.  

3. Revisão **saída** vista para verificação.  


## <a name="logout"></a>Terminar sessão  

Na barra de menus, navegue até **View** > **paleta de comandos...** e, em seguida, introduza **HDInsight: Fim de sessão**.  Haverá um pop-up no canto direito da parte inferior que diz **fim de sessão com êxito!**.


## <a name="next-steps"></a>Passos Seguintes
Para um vídeo de demonstração da utilização do HDInsight para Visual Studio Code, consulte [HDInsight para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
