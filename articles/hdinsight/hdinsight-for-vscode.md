---
title: Azure HDInsight para Código de Estúdio Visual
description: Saiba como utilizar as Ferramentas Spark & Hive (Azure HDInsight) para O Código do Estúdio Visual. Utilize as ferramentas para criar e submeter consultas e scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: de433d85c2f04a7140fbcb918730218ac3a05e54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878634"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Use ferramentas de colmeia de faíscas & para código de estúdio visual

Aprenda a usar Apache Spark & Ferramentas de Colmeia para Código de Estúdio Visual. Use as ferramentas para criar e submeter trabalhos de lote apache hive, consultas interativas da Hive e scripts PySpark para Apache Spark. Primeiro vamos descrever como instalar a Spark & Hive Tools no Visual Studio Code. Depois vamos passar por como submeter empregos à Spark & Hive Tools.  

A Spark & As Ferramentas Hive podem ser instaladas em plataformas que são suportadas pelo Visual Studio Code. Note os seguintes pré-requisitos para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes itens para completar os passos deste artigo:

- Um cluster do Azure HDInsight. Para criar um cluster, consulte [Iniciar com HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Ou use um aglomerado de faíscas e colmeias que suporte um ponto final apache Livy.
- [Código de estúdio visual.](https://code.visualstudio.com/)
- [Mono.](https://www.mono-project.com/docs/getting-started/install/) Mono é necessário apenas para Linux e macOS.
- [Um ambiente interativo PySpark para código](set-up-pyspark-interactive-environment.md)de estúdio visual.
- Um diretório local. Este artigo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instale ferramentas de colmeia de faíscas &

Depois de cumprir os pré-requisitos, pode instalar ferramentas spark & hive para código de estúdio visual seguindo estes passos:

1. Abra o Visual Studio Code.

2. A partir da barra de menus, navegue até **ver** > **extensões.**

3. Na caixa de pesquisa, introduza **Spark & Hive**.

4. Selecione **Ferramentas spark & hiv e,** em seguida, selecione **Instalar:**

   ![Spark & Hive para instalação de Código Python de estúdio visual](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **Recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abra uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um ficheiro no Código do Estúdio Visual, siga estes passos:

1. Da barra de menus, navegue **até** > **file Open Folder...**  >  **C:\HD\HDexemplo**, e, em seguida, selecione o botão **Select Folder.** A pasta aparece na vista **Explorer** à esquerda.

2. Na vista **Explorer,** selecione a pasta **HDexample** e, em seguida, selecione o ícone **New File** ao lado da pasta de trabalho:

   ![ícone de novo código de estúdio visual](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nomeie o novo `.hql` ficheiro utilizando as (consultas `.py` da Hive) ou a extensão do ficheiro (Spark script). Este exemplo utiliza **helloWorld.hql**.

## <a name="set-the-azure-environment"></a>Definir o ambiente Azure

Para um utilizador nacional de nuvem, siga estes passos para definir primeiro o ambiente Azure e, em seguida, use o **Azure: Assine no** comando para iniciar sessão no Azure:

1. Navegar para**definições**de**preferências** > de **ficheiros** > .
2. Pesquise na seguinte corda: **Azure: Cloud**.
3. Selecione a nuvem nacional da lista:

   ![Definir configuração de entrada de login predefinido](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Ligue-se a uma conta Azure

Antes de poder submeter scripts aos seus clusters a partir do Código do Estúdio Visual, deve ligar-se à sua conta Azure ou ligar um cluster. Utilize o nome de utilizador Apache Ambari e credenciais de senha ou uma conta unida pelo domínio. Siga estes passos para ligar ao Azure:

1. A partir da barra de menus, navegue para **ver** > a Paleta de**Comando...** e **introduza Azure: Inscreva-se:**

    ![Spark & Ferramentas de colmeia para login de código de estúdio visual](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de inscrição para iniciar sessão no Azure. Depois de conectado, o seu nome de conta Azure aparece na barra de estado na parte inferior da janela Visual Studio Code.  

## <a name="link-a-cluster"></a>Ligar um cluster

### <a name="link-azure-hdinsight"></a>Link: Azure HDInsight

Pode ligar um cluster normal utilizando um nome de utilizador gerido por [Apache Ambari,](https://ambari.apache.org/)ou pode ligar um cluster Hadoop seguro da Enterprise Security Pack utilizando um nome de utilizador de domínio (como: `user1@contoso.com`).

1. A partir da barra de menus, navegue para **ver** > a Paleta de**Comando...** e introduza **Spark / Hive: Link a Cluster**.

   ![Comando de cluster de ligação de paleta de comando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecione cluster ligado tipo **Azure HDInsight**.

3. Introduza o URL do cluster HDInsight.

4. Introduza o seu nome de utilizador Ambari; o padrão é **administrador.**

5. Introduza a sua senha Ambari.

6. Selecione o tipo de cluster.

7. Detete o nome de exibição do cluster (opcional).

8. Rever a visão **OUTPUT** para verificação.

   > [!NOTE]  
   > O nome de utilizador e a palavra-passe ligados são utilizados se o cluster iniciar sessão na subscrição do Azure e ligar um cluster.  

### <a name="link-generic-livy-endpoint"></a>Link: Ponto final genérico da Livy

1. A partir da barra de menus, navegue para **ver** > a Paleta de**Comando...** e introduza **Spark / Hive: Link a Cluster**.

2. Selecione cluster ligado tipo de cluster **Genérico Livy Endpoint**.

3. Entre no ponto final genérico da Livy. Por exemplo:\:http //10.172.41.42:18080.

4. Selecione tipo de autorização **Básico** ou **Nenhum**.  Se selecionar **Basic:**  
    &emsp;a. Introduza o seu nome de utilizador Ambari; o padrão é **administrador.**  
    &emsp;b. Introduza a sua senha Ambari.

5. Rever a visão **OUTPUT** para verificação.

## <a name="list-clusters"></a>Aglomerados de listas

1. A partir da barra de menus, navegue para **ver** > a Paleta de**Comando...** e introduza **Spark / Hive: List Cluster**.

2. Selecione a subscrição que deseja.

3. Reveja a visão **OUTPUT.** Esta visão mostra o seu cluster ligado (ou clusters) e todos os clusters sob a sua subscrição Azure:

    ![Definir uma configuração de cluster padrão](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Definir o cluster predefinido

1. Reabra a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Selecione o ficheiro **HelloWorld.hql** que foi criado [anteriormente](#open-a-work-folder). Abre no editor de guiões.

3. Clique no editor de scripts e, em seguida, selecione **Spark / Hive: set Default Cluster**.  

4. [Ligue-se](#connect-to-an-azure-account) à sua conta Azure, ou ligue um cluster se ainda não o tiver feito.

5. Selecione um cluster como o cluster predefinido para o ficheiro de script atual. As ferramentas atualizam automaticamente o **. Ficheiro de configuração VSCode\settings.json:**

   ![Definir configuração de cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Submeta consultas interativas da Colmeia e scripts de lote de Colmeia

Com a Spark & Hive Tools for Visual Studio Code, pode submeter consultas interativas de Hive e scripts de lote de Hive para os seus clusters.

1. Reabra a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Selecione o ficheiro **HelloWorld.hql** que foi criado [anteriormente](#open-a-work-folder). Abre no editor de guiões.

3. Copie e cole o seguinte código no seu ficheiro Hive e, em seguida, guarde-o:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Ligue-se](#connect-to-an-azure-account) à sua conta Azure, ou ligue um cluster se ainda não o tiver feito.

5. Clique no editor de scripts e selecione **Hive: Interactive** para submeter a consulta, ou use o atalho de teclado Ctrl+Alt+I.  Selecione **Hive: Batch** para submeter o script ou utilizar o atalho do teclado Ctrl+Alt+H.  

6. Se não especificou um cluster predefinido, selecione um cluster. As ferramentas também permitem submeter um bloco de código em vez de todo o ficheiro script utilizando o menu de contexto. Após alguns momentos, os resultados da consulta aparecem num novo separador:

   ![Resultado interativo da consulta da Colmeia Apache](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Painel RESULTADOS:** Pode guardar todo o resultado como um ficheiro CSV, JSON ou Excel para um caminho local ou apenas selecionar várias linhas.

    - **Painel MENSAGENS:** Quando seleciona um número **de linha,** salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Submeter consultas interativas pySpark

Para submeter consultas interativas pySpark, siga estes passos:

1. Reabra a pasta **HDexample** que foi discutida [anteriormente,](#open-a-work-folder)se fechada.  

2. Crie um novo ficheiro **HelloWorld.py,** seguindo os passos [anteriores.](#open-a-work-folder)

3. Copiar e colar o seguinte código no ficheiro script:

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

4. [Ligue-se](#connect-to-an-azure-account) à sua conta Azure, ou ligue um cluster se ainda não o tiver feito.

5. Selecione todo o código, clique no editor de scripts e selecione **Spark: PySpark Interactive** para submeter a consulta. Ou, use o atalho CTRL+Alt+I.

   ![menu de contexto interativo pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecione o cluster, se não tiver especificado um cluster predefinido. Após alguns momentos, os resultados **da Python Interactive** aparecem num novo separador. As ferramentas também permitem submeter um bloco de código em vez de todo o ficheiro script utilizando o menu de contexto:

   ![janela interativa pitão pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Insira **%%info,** e, em seguida, prima Shift+Enter para ver a informação de trabalho (opcional):

   ![pyspark informação de trabalho interativo](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. A ferramenta também suporta a consulta **Spark SQL:**

   ![Resultado da vista interativa pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   O estado de submissão aparece à esquerda da barra de estado inferior quando está a fazer consultas. Não submeta outras consultas quando o estado for **PySpark Kernel (ocupado)**.  

   > [!NOTE]
   >
   > Quando a **extensão Python Ativada** for desobstruída nas definições (é selecionada por padrão), os resultados de interação de pyspark submetidos usarão a janela antiga:
   >
   > ![extensão pitão interativa pyspark desativado](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Submeta o trabalho de lote PySpark

1. Reabra a pasta **HDexample** que discutiu [anteriormente,](#open-a-work-folder)se fechada.  

2. Crie um novo **ficheiro BatchFile.py** seguindo os passos [anteriores.](#open-a-work-folder)

3. Copiar e colar o seguinte código no ficheiro script:

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

4. [Ligue-se](#connect-to-an-azure-account) à sua conta Azure, ou ligue um cluster se ainda não o tiver feito.

5. Clique no editor de scripts e, em seguida, selecione **Spark: PySpark Batch,** ou use o atalho do teclado Ctrl+Alt+H.

6. Selecione um cluster para submeter o seu trabalho pySpark a:

   ![Submeta a produção de resultados do trabalho python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Depois de submeter um trabalho python, os registos de submissão aparecem na janela **OUTPUT** no Código do Estúdio Visual. O URL Spark UI e o URL Yarn UI também são mostrados. Você pode abrir o URL em um navegador web para rastrear o estado de trabalho.

## <a name="apache-livy-configuration"></a>Configuração Apache Livy

A configuração [Apache Livy](https://livy.incubator.apache.org/) é suportada. Pode configurá-lo no **. VSCode\definições.json** ficheiro na pasta espaço de trabalho. Atualmente, a configuração livy apenas suporta o script Python. Para mais informações, consulte [Livy README.](https://github.com/cloudera/livy/blob/master/README.rst )

<a id="triggerlivyconf"></a>**Como desencadear a configuração da Livy**

Método 1  
1. A partir da barra de menus, navegue até**definições**de**preferências** > de **ficheiros** > .
2. Na caixa de **definições de Pesquisa,** introduza a Submissão de **Trabalho HDInsight: Livy Conf**.  
3. **Selecione Editar em definições.json** para obter o resultado de pesquisa relevante.

Método 2 Envie um ficheiro `.vscode` e note que a pasta é adicionada automaticamente à pasta de trabalho. Pode ver a configuração Livy selecionando **.vscode\settings.json**.

+ As definições do projeto:

    ![Configuração HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Para as definições de memória e **executordo** do **controlador,** detete o valor e a unidade. Por exemplo: 1g ou 1024m.

+ Configurações de Livy suportadas:

    **POST /Lotes** Solicitar corpo

    | nome | descrição | tipo |
    | --- | --- | --- |
    | file | Ficheiro contendo o pedido de execução | Caminho (obrigatório) |
    | proxyUser | Utilizador a personificar ao executar o trabalho | String |
    | className | Candidatura Java/Faísca classe principal | String |
    | args | Argumentos de linha de comando para a aplicação | Lista de cordas |
    | jarros | Jarros para serem usados nesta sessão | Lista de cordas | 
    | pyFiles | Ficheiros Python a serem usados nesta sessão | Lista de cordas |
    | ficheiros | Ficheiros a serem usados nesta sessão | Lista de cordas |
    | driverMemory | Quantidade de memória para usar para o processo do condutor | String |
    | driverCores | Número de núcleos a utilizar para o processo do condutor | int |
    | executorMemória | Quantidade de memória para usar por processo de executor | String |
    | executorCores | Número de núcleos a utilizar para cada executor | int |
    | numExecutors | Número de executores a lançar para esta sessão | int |
    | arquivos | Arquivos a serem usados nesta sessão | Lista de cordas |
    | fila | Nome da fila yARN a submeter| String |
    | nome | Nome desta sessão | String |
    | conf | Propriedades de configuração de faíscas | Mapa da chave=val |

    Corpo de resposta O objeto de lote criado.

    | nome | descrição | tipo |
    | --- | ---| --- |
    | ID | ID de sessão | int |
    | appId | Id de inscrição desta sessão | String |
    | appInfo | Informações detalhadas sobre aplicações | Mapa da chave=val |
    | log | Linhas de log | Lista de cordas |
    | state |Estado de lote | String |

    > [!NOTE]
    > O config Livy atribuído é exibido no painel de saída quando submete o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar com Azure HDInsight do Explorer

Pode visualizar a Tabela de Colmeias nos seus clusters diretamente através do explorador **Azure HDInsight:**

1. [Ligue-se](#connect-to-an-azure-account) à sua conta Azure se ainda não o fez.

2. Selecione o ícone **Azure** da coluna mais à esquerda.

3. Do painel esquerdo, expanda **o AZURE: HDINSIGHT**. As subscrições e clusters disponíveis estão listados.

4. Expanda o cluster para ver a base de dados de metadados da Hive e o esquema de mesa.

5. Clique na mesa da Colmeia. Por exemplo: **hivesampletable**. Selecione **Pré-visualização**.

   ![Spark & Hive para visual studio código de visualização mesa de colmeia](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A janela de resultados de **pré-visualização** abre:

   ![Spark & Hive para visual studio código de visualização janela de resultados](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Painel RESULTADOS

   Pode guardar todo o resultado como um ficheiro CSV, JSON ou Excel para um caminho local, ou apenas selecionar várias linhas.

- Painel de MENSAGES
   1. Quando o número de filas na mesa é superior a 100, vê-se a seguinte mensagem: "As primeiras 100 filas são exibidas para a mesa da Colmeia."
   2. Quando o número de filas na tabela é inferior ou igual a 100, vê-se a seguinte mensagem: "60 linhas são exibidas para a mesa da Colmeia."
   3. Quando não há conteúdo na mesa, vê-se`0 rows are displayed for Hive table.`a seguinte mensagem: "

        >[!NOTE]
        >
        >No Linux, instale o xclip para ativar os dados da tabela de cópias.
        >
        >![Spark & Hive para código estúdio visual em Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Características adicionais

Spark & Hive for Visual Studio Code também suporta as seguintes funcionalidades:

- **IntelliSense autocompleto**. Sugestões surgem para palavras-chave, métodos, variáveis e outros elementos de programação. Ícones diferentes representam diferentes tipos de objetos:

    ![Spark & Hive Tools para objetos de código de estúdio visual IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marcador de erro IntelliSense**. O serviço de idiomas sublinha erros de edição no script da Colmeia.     
- **Destaques de sintaxe.** O serviço linguístico utiliza diferentes cores para diferenciar variáveis, palavras-chave, tipo de dados, funções e outros elementos de programação:

    ![Spark & Hive Tools para destaques de sintaxe de código de estúdio visual](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Papel só para leitores

Os utilizadores a quem seja atribuída a função apenas para o cluster não podem submeter postos de trabalho ao cluster HDInsight, nem ver a base de dados da Hive. Contacte o administrador do cluster para atualizar a sua função para [**o Operador de Cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) no portal [Azure](https://ms.portal.azure.com/). Se tiver credenciais Ambari válidas, pode ligar manualmente o cluster utilizando as seguintes orientações.

### <a name="browse-the-hdinsight-cluster"></a>Navegue no cluster HDInsight  

Quando selecionar o explorador Azure HDInsight para expandir um cluster HDInsight, é-lhe pedido que ligue o cluster se tiver a função apenas para o cluster. Utilize o seguinte método para ligar ao cluster utilizando as suas credenciais Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Submeter o trabalho ao cluster HDInsight

Ao submeter o trabalho a um cluster HDInsight, é solicitado a ligar o cluster se estiver no papel apenas para o leitor para o cluster. Utilize os seguintes passos para ligar ao cluster utilizando credenciais Ambari.

### <a name="link-to-the-cluster"></a>Ligação ao cluster

1. Introduza um nome de utilizador ambari válido.
2. Introduza uma senha válida.

   ![Spark & Ferramentas de colmeia para o nome de utilizador do código do estúdio visual](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Ferramentas de hiv de spark & para senha de código de estúdio visual](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Pode utilizar `Spark / Hive: List Cluster` para verificar o cluster ligado:
  >
  >![Spark & Ferramentas de colmeia para leitor de código de estúdio visual ligado](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Navegue numa conta Gen2 de Armazenamento de Data Lake

Selecione o explorador Azure HDInsight para expandir uma conta Data Lake Storage Gen2. É-lhe pedido que introduza a chave de acesso ao armazenamento se a sua conta Azure não tiver acesso ao armazenamento gen2. Após a validação da chave de acesso, a conta Data Lake Storage Gen2 é expandida automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Submeta empregos a um cluster HDInsight com Data Lake Storage Gen2

Envie um trabalho para um cluster HDInsight usando Data Lake Storage Gen2. É-lhe pedido que introduza a chave de acesso ao armazenamento se a sua conta Azure não tiver acesso por escrito ao armazenamento gen2. Após a validação da chave de acesso, o trabalho será submetido com sucesso.

![Ferramentas de colmeia de & de faíscas para acesso ao código do estúdio visual](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Pode obter a chave de acesso para a conta de armazenamento do portal Azure. Para mais informações, consulte Gerir as chaves de [acesso à conta](../storage/common/storage-account-keys-manage.md)de armazenamento .

## <a name="unlink-cluster"></a>Cluster unlink

1. A partir da barra de menus, vá para **ver** > paleta de**comando,** e depois **introduza Spark / Hive: Unlink a Cluster**.  

2. Selecione um cluster para desligar.  

3. Consulte a vista **OUTPUT** para verificação.  

## <a name="sign-out"></a>Terminar sessão  

A partir da barra de menus, vá **ver** > a Paleta de**Comando,** e depois **introduza Azure: Sign out**.

## <a name="next-steps"></a>Passos seguintes

Para um vídeo que demonstre o uso de Spark & Hive para Visual Studio Code, consulte [Spark & Hive para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
