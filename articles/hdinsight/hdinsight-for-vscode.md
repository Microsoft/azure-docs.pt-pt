---
title: Azure HDInsight para Visual Studio Code
description: Saiba como usar as ferramentas do Spark & Hive (Azure HDInsight) para Visual Studio Code para criar e enviar consultas e scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 03d0d26a21e710c07019d3ffcb13a1482a96af50
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311727"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Use as ferramentas do Spark & Hive para Visual Studio Code

Saiba como usar as ferramentas do Spark & Hive para Visual Studio Code para criar e enviar trabalhos de Apache Hive lote, consultas interativas de Hive e scripts PySpark para Apache Spark. Primeiro, descreveremos como instalar as ferramentas do Spark & Hive no Visual Studio Code e, em seguida, veremos como enviar trabalhos para as ferramentas do Spark & Hive.  

As ferramentas do hive do Spark & podem ser instaladas em plataformas com suporte no Visual Studio Code, que incluem Windows, Linux e macOS. Observe os seguintes pré-requisitos para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster do Azure HDInsight. Para criar um cluster, consulte Introdução [ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Ou use um cluster Spark e Hive que dê suporte a um ponto de extremidade Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). O mono é necessário apenas para Linux e macOS.
- [Um ambiente PySpark interativo para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Um diretório local. Este artigo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalar as ferramentas do Spark & Hive

Depois de atender aos pré-requisitos, você pode instalar as ferramentas do Spark & Hive para Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue até **Exibir** **extensões**de > .

3. Na caixa de pesquisa, insira **Spark & Hive**.

4. Selecione **Ferramentas do hive do Spark &** nos resultados da pesquisa e selecione **instalar**:

   ![& Hive do Spark para Visual Studio Code instalação do Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abrir uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um arquivo em Visual Studio Code, siga estas etapas:

1. Na barra de menus, navegue até **arquivo** > **abrir pasta...**  > **C:\HD\HDexample**e, em seguida, selecione o botão **Selecionar pasta** . A pasta aparece no modo de exibição do **Explorer** à esquerda.

2. Na exibição do **Gerenciador** , selecione a pasta **HDexample** e, em seguida, selecione o ícone **novo arquivo** ao lado da pasta de trabalho:

   ![ícone de novo arquivo do Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nomeie o novo arquivo usando o `.hql` (consultas do hive) ou a extensão de arquivo `.py` (script do Spark). Este exemplo usa **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Definir o ambiente do Azure

Para um usuário de nuvem nacional, siga estas etapas para definir o ambiente do Azure primeiro e, em seguida, use o comando **Azure: entrar** para entrar no Azure:

1. Navegue até **arquivo** > **preferências** > **configurações**.
2. Pesquise na seguinte cadeia de caracteres: **Azure: Cloud**.
3. Selecione a nuvem nacional na lista:

   ![Definir configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Conectar-se a uma conta do Azure

Antes de enviar scripts para seus clusters de Visual Studio Code, você deve se conectar à sua conta do Azure ou vincular um cluster (usando as credenciais de nome de usuário e senha do Apache Ambari ou uma conta de domínio associado). Siga estas etapas para se conectar ao Azure:

1. Na barra de menus, navegue para **exibir** > **paleta de comandos...** e insira **Azure: entrar**:

    ![Ferramentas do hive do Spark & para o logon do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior da janela de Visual Studio Code.  

## <a name="link-a-cluster"></a>Vincular um cluster

### <a name="link-azure-hdinsight"></a>Link: Azure HDInsight

Você pode vincular um cluster normal usando um nome de usuário gerenciado pelo [Apache Ambari](https://ambari.apache.org/)ou pode vincular um cluster Hadoop seguro do pacote de segurança do Enterprise usando um nome de usuário de domínio (como: `user1@contoso.com`).

1. Na barra de menus, navegue para **exibir** > **paleta de comandos...** e insira **Spark/Hive: vincular um cluster**.

   ![Comando do cluster de link da paleta de comandos](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecionar tipo de cluster vinculado **Azure HDInsight**.

3. Insira a URL do cluster HDInsight.

4. Insira seu nome de usuário do Ambari; o padrão é **admin**.

5. Insira sua senha do Ambari.

6. Selecione o tipo de cluster.

7. Defina o nome de exibição do cluster (opcional).

8. Examine a exibição de **saída** para verificação.

   > [!NOTE]  
   > O nome de usuário e a senha vinculados serão usados se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.  

### <a name="link-generic-livy-endpoint"></a>Link: ponto de extremidade Livy genérico

1. Na barra de menus, navegue para **exibir** > **paleta de comandos...** e insira **Spark/Hive: vincular um cluster**.

2. Selecione o tipo de cluster vinculado **Livy ponto de extremidade genérico**.

3. Insira o ponto de extremidade Livy genérico. Por exemplo: http\://10.172.41.42:18080.

4. Selecione tipo de autorização **básico** ou **nenhum**.  Se você selecionar **básico**:  
    &emsp;um. Insira seu nome de usuário do Ambari; o padrão é **admin**.  
    &emsp;b. Insira sua senha do Ambari.

5. Examine a exibição de **saída** para verificação.

## <a name="list-clusters"></a>Listar clusters

1. Na barra de menus, navegue até **exibir** > **paleta de comandos...** e insira **Spark/Hive: listar cluster**.

2. Selecione a assinatura desejada.

3. Examine a exibição de **saída** . Essa exibição mostra o cluster (ou clusters) vinculado e todos os clusters em sua assinatura do Azure:

    ![Definir uma configuração de cluster padrão](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Definir o cluster padrão

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Selecione o arquivo **HelloWorld. HQL** que foi criado [anteriormente](#open-a-work-folder). Ele é aberto no editor de scripts.

3. Clique com o botão direito do mouse no editor de scripts e selecione **Spark/Hive: definir cluster padrão**.  

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Selecione um cluster como o cluster padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o **.** Arquivo de configuração do VSCode\settings.JSON:

   ![Definir configuração de cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Enviar consultas de Hive interativas e scripts de lote do hive

Com as ferramentas do Spark & Hive para Visual Studio Code, você pode enviar consultas interativas do hive e scripts de lote do hive para seus clusters.

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Selecione o arquivo **HelloWorld. HQL** que foi criado [anteriormente](#open-a-work-folder). Ele é aberto no editor de scripts.

3. Copie e cole o código a seguir em seu arquivo de Hive e, em seguida, salve-o:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no editor de scripts e selecione **Hive: interativo** para enviar a consulta ou use o atalho de teclado CTRL + ALT + I.  Selecione **Hive: lote** para enviar o script ou use o atalho de teclado CTRL + ALT + H.  

6. Se você não tiver especificado um cluster padrão, selecione um cluster. As ferramentas também permitem que você envie um bloco de código em vez do arquivo de script inteiro usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia:

   ![Resultado da consulta Apache Hive interativa](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel de **resultados** : você pode salvar o resultado inteiro como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

    - Painel de **mensagens** : quando você seleciona um número de **linha** , ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas do PySpark

Para enviar consultas PySpark interativas, siga estas etapas:

1. Reabra a pasta **HDexample** que foi discutida [anteriormente](#open-a-work-folder), se fechada.  

2. Crie um novo arquivo **HelloWorld.py** , seguindo as etapas [anteriores](#open-a-work-folder) .

3. Copie e cole o seguinte código no arquivo de script:

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

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Selecione todo o código, clique com o botão direito do mouse no editor de scripts e selecione **Spark: PySpark Interactive** para enviar a consulta. Ou use o atalho CTRL + ALT + I.

   ![menu de contexto interativo do pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecione o cluster, se você não tiver especificado um cluster padrão. Após alguns instantes, os resultados **interativos do Python** aparecem em uma nova guia. As ferramentas também permitem que você envie um bloco de código em vez do arquivo de script inteiro usando o menu de contexto:

   ![janela interativa do Python pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Insira **%% info**e pressione Shift + Enter para exibir as informações do trabalho (opcional):

   ![informações do trabalho de exibição interativa do pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. A ferramenta também dá suporte à consulta **SQL do Spark** :

   ![Resultado da exibição interativa do Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   O status de envio aparece à esquerda da barra de status inferior quando você está executando consultas. Não envie outras consultas quando o status for **kernel PySpark (ocupado)** .  

   > [!NOTE]
   >
   > Quando a **extensão do Python habilitada** é desmarcada nas configurações (ela é selecionada por padrão), os resultados da interação pyspark enviada usarão a janela antiga:
   >
   > ![extensão do Python interativa do pyspark desabilitada](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Enviar trabalho do lote PySpark

1. Reabra a pasta **HDexample** que você discutiu [anteriormente](#open-a-work-folder), se fechada.  

2. Crie um novo arquivo **BatchFile.py** seguindo as etapas [anteriores](#open-a-work-folder) .

3. Copie e cole o seguinte código no arquivo de script:

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

4. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no editor de scripts e selecione **Spark: PySpark batch**ou use o atalho de teclado CTRL + ALT + H.

6. Selecione um cluster para o qual enviar seu trabalho do PySpark:

   ![Enviar saída de resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Depois de enviar um trabalho do Python, os logs de envio aparecem na janela de **saída** no Visual Studio Code. A URL da interface do usuário do Spark e a URL da interface do usuário yarn também são mostradas Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="apache-livy-configuration"></a>Configuração do Apache Livy

Há suporte para a configuração do [Apache Livy](https://livy.incubator.apache.org/) . Você pode configurá-lo no **. Arquivo VSCode\settings.json** na pasta do espaço de trabalho. Atualmente, a configuração do Livy dá suporte apenas ao script Python. Para obter mais detalhes, consulte [LIVY README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como disparar a configuração do Livy**

Método 1  
1. Na barra de menus, navegue até **arquivo** > **preferências** > **configurações**.
2. Na caixa **configurações de pesquisa** , insira **envio de trabalho do HDInsight: Livy conf**.  
3. Selecione **Editar em Settings. JSON** para o resultado da pesquisa relevante.

O método 2 envia um arquivo e observa que a pasta. vscode é adicionada automaticamente à pasta de trabalho. Você pode ver a configuração Livy selecionando **. vscode\settings.JSON**.

+ As configurações do projeto:

    ![Configuração do Apache Livy do HDInsight](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Para as configurações de **driverMemory** e **executorMemory** , defina o valor e a unidade. Por exemplo: 1G ou 1024M.

+ Configurações de Livy com suporte:

    **Postar/batches** Corpo da solicitação

    | nome | descrição | tipo |
    | :- | :- | :- |
    | file | Arquivo que contém o aplicativo a ser executado | Caminho (obrigatório) |
    | proxyUser | Usuário a representar ao executar o trabalho | Cadeia |
    | className | Classe principal do aplicativo Java/Spark | Cadeia |
    | args | Argumentos de linha de comando para o aplicativo | Lista de cadeias de caracteres |
    | jars | Jars a serem usados nesta sessão | Lista de cadeias de caracteres | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de cadeias de caracteres |
    | files | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | driverMemory | Quantidade de memória a ser usada para o processo de driver | Cadeia |
    | driverCores | Número de núcleos a serem usados para o processo de driver | Int |
    | executorMemory | Quantidade de memória a ser usada por processo de executor | Cadeia |
    | executorCores | Número de núcleos a serem usados para cada executor | Int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | Int |
    | archives | Arquivos mortos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | fila | Nome da fila de YARN a ser enviada| Cadeia |
    | nome | Nome desta sessão | Cadeia |
    | conf | Propriedades de configuração do Spark | Mapa de chave = Val |

    Corpo da resposta o objeto do lote criado.

    | nome | descrição | tipo |
    | :- | :- | :- |
    | ID | ID da sessão | Int |
    | appId | ID do aplicativo desta sessão | Cadeia |
    | appInfo | Informações detalhadas do aplicativo | Mapa de chave = Val |
    | log | Linhas de log | Lista de cadeias de caracteres |
    | state |Estado do lote | Cadeia |

    > [!NOTE]
    > A configuração Livy atribuída é exibida no painel de saída quando você envia o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar com o Azure HDInsight do Explorer

Você pode visualizar a tabela do hive em seus clusters diretamente por meio do **Azure HDInsight** Explorer:

1. [Conecte-](#connect-to-an-azure-account) se à sua conta do Azure se ainda não tiver feito isso.

2. Selecione o ícone do **Azure** na coluna mais à esquerda.

3. No painel esquerdo, expanda **Azure: HDINSIGHT**. As assinaturas e os clusters disponíveis são listados.

4. Expanda o cluster para exibir o banco de dados de metadados do hive e o esquema de tabela.

5. Clique com o botão direito do mouse na tabela Hive. Por exemplo: **hivesampletable**. Selecione **Visualização**.

   ![Hive do Spark & para a tabela do hive do Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A janela **Visualizar resultados** é aberta:

   ![& Hive do Spark para Visual Studio Code janela de resultados da visualização](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Painel de resultados

   Você pode salvar todo o resultado como um arquivo CSV, JSON ou Excel em um caminho local ou apenas selecionar várias linhas.

- Painel de mensagens
   1. Quando o número de linhas na tabela for maior que 100, você verá a seguinte mensagem: "as primeiras 100 linhas são exibidas para a tabela Hive".
   2. Quando o número de linhas na tabela for menor ou igual a 100, você verá uma mensagem semelhante à seguinte: "60 linhas são exibidas para a tabela do hive".
   3. Quando não houver conteúdo na tabela, você verá a seguinte mensagem: "0 linhas são exibidas para a tabela do hive".

        >[!NOTE]
        >
        >No Linux, instale o XCLIP para habilitar os dados da tabela de cópia.
        >
        >![Spark & Hive para Visual Studio Code no Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Recursos adicionais

O Spark & Hive para Visual Studio Code também dá suporte aos seguintes recursos:

- **Preenchimento automático do IntelliSense**. Sugestões pop up para palavras-chave, métodos, variáveis e outros elementos de programação. Ícones diferentes representam tipos diferentes de objetos:

    ![Ferramentas do hive do Spark & para objetos Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha erros de edição no script do hive.     
- **Destaques da sintaxe**. O serviço de idioma usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e outros elementos de programação:

    ![Ferramentas do Spark & Hive para Visual Studio Code destaques da sintaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Função somente de leitor

Os usuários que recebem a função somente de leitor para o cluster não podem mais enviar trabalhos para o cluster HDInsight nem podem exibir o banco de dados do hive. Contate o administrador de cluster para atualizar sua função para o [**operador de cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) no [portal do Azure](https://ms.portal.azure.com/). Se você tiver credenciais Ambari válidas, poderá vincular manualmente o cluster usando as diretrizes a seguir.

### <a name="browse-the-hdinsight-cluster"></a>Procurar o cluster HDInsight  

Ao selecionar o Azure HDInsight Explorer para expandir um cluster HDInsight, você será solicitado a vincular o cluster se tiver a função somente de leitor para o cluster. Use o método a seguir para vincular ao cluster usando suas credenciais do Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Enviar o trabalho para o cluster HDInsight

Ao enviar o trabalho para um cluster HDInsight, você será solicitado a vincular o cluster se estiver na função somente de leitor para o cluster. Use as etapas a seguir para vincular ao cluster usando as credenciais do Ambari.

### <a name="link-to-the-cluster"></a>Link para o cluster

1. Insira um nome de usuário Ambari válido.
2. Insira uma senha válida.

   ![Ferramentas do hive do Spark & para Visual Studio Code nome de usuário](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Ferramentas do hive do Spark & para Visual Studio Code senha](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Você pode usar `Spark / Hive: List Cluster` para verificar o cluster vinculado:
  >
  >![Ferramentas do hive do Spark & para Visual Studio Code Reader vinculado](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Procurar uma conta de Data Lake Storage Gen2

Ao selecionar o Azure HDInsight Explorer para expandir uma conta de Data Lake Storage Gen2, você será solicitado a inserir a chave de acesso de armazenamento se sua conta do Azure não tiver acesso ao armazenamento Gen2. Depois que a chave de acesso for validada, a conta de Data Lake Storage Gen2 será expandida automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Enviar trabalhos para um cluster HDInsight com Data Lake Storage Gen2

Ao enviar um trabalho para um cluster HDInsight usando Data Lake Storage Gen2, você será solicitado a inserir a chave de acesso de armazenamento se sua conta do Azure não tiver acesso de gravação ao armazenamento Gen2. Depois que a chave de acesso for validada, o trabalho será enviado com êxito.

![Ferramentas do hive & Spark para Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Você pode obter a chave de acesso para a conta de armazenamento do portal do Azure. Para obter mais informações, consulte [Exibir e copiar chaves de acesso](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Desvincular cluster

1. Na barra de menus, acesse **exibir** > **paleta de comandos**e, em seguida, insira **Spark/Hive: Desvincular um cluster**.  

2. Selecione um cluster para desvincular.  

3. Consulte a exibição de **saída** para verificação.  

## <a name="sign-out"></a>Terminar sessão  

Na barra de menus, acesse **exibir** > **paleta de comandos**e, em seguida, insira **Azure: sair**.

## <a name="next-steps"></a>Passos seguintes

Para ver um vídeo que demonstra o uso do Spark & Hive para Visual Studio Code, consulte [spark & Hive para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
