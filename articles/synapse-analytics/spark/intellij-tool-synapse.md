---
title: Tutorial - Kit de Ferramentas Azure para IntelliJ (aplicação Spark)
description: Tutorial - Utilize o Kit de Ferramentas Azure para o IntelliJ para desenvolver aplicações Spark, que estão escritas em Scala, e submeta-as a uma piscina Apache Spark (pré-visualização).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422656"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Tutorial: Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para piscinas Spark (pré-visualização)

Este tutorial demonstra como usar o Kit de Ferramentas Azure para o plug-in IntelliJ para desenvolver aplicações Apache Spark, que são escritas em [Scala,](https://www.scala-lang.org/)e depois submetê-las a uma piscina spark (pré-visualização) diretamente do ambiente integrado de desenvolvimento intelliJ (IDE). Pode utilizar o plug-in de algumas formas:

- Desenvolva e submeta uma aplicação Scala Spark numa piscina spark.
- Aceda aos seus recursos de piscinas Spark.
- Desenvolva e execute uma aplicação Scala Spark localmente.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Utilize o Kit de Ferramentas Azure para plug-in IntelliJ
> - Desenvolver aplicações Apache Spark
> - Enviar candidatura a piscinas Spark

## <a name="prerequisites"></a>Pré-requisitos

- [IntelliJ IDEA Versão Comunitária](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Plugin 3.27.0-2019.2 – Instalação do [repositório IntelliJ Plugin](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (versão 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin – Instalação do [repositório IntelliJ Plugin](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Este pré-requisito é apenas para utilizadores do Windows.

  Enquanto estiver a executar a aplicação Spark Scala local num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque winUtils.exe está desaparecido no Windows.
  Para resolver este erro, descarregue os [WinUtils executáveis](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\WinUtils\bin**. Em seguida, adicione a variável ambiental **HADOOP_HOME,** e detetete o valor da variável para **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Crie uma aplicação Spark Scala para uma piscina de faíscas

1. Inicie o IntelliJ IDEA e selecione **Create New Project** para abrir a janela new **project.**
2. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.
3. Selecione **Projeto Spark com Amostras (Scala)** a partir da janela principal.
4. A partir da lista de drop-down da **ferramenta Build,** selecione um dos seguintes tipos:

   - **Maven** para o apoio ao assistente de criação de projetos scala.
   - **SBT** para gerir as dependências e construção para o projeto Scala.

    ![Caixa de diálogo IntelliJ IDEA Novo Projeto](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecione **Seguinte**.
6. Na janela **New Project,** forneça as seguintes informações:

    | Propriedade | Descrição |
    | ----- | ----- |
    |Nome do projeto| Insira um nome. Este tutorial utiliza `myApp`.|
    |Localização&nbsp;do projeto| Insira o local procurado para salvar o seu projeto.|
    |Projeto SDK| Pode estar em branco na sua primeira utilização do IDEA. Selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Synapse só suporta **a Faísca 2.4.0**.|

    ![Selecionando o Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecione **Concluir**. Pode levar alguns minutos até que o projeto fique disponível.
8. O projeto Spark cria automaticamente um artefacto para si. Para visualizar o artefacto, faça o seguinte funcionamento:

   a. Da barra de menus, navegue até a Estrutura do Projeto **arquivo...** > **Project Structure...**.

   b. A partir da janela Estrutura do **Projeto,** selecione **Artefactos**.

   c. **Selecione Cancelar** depois de ver o artefacto.

    ![Informações de artefactos na caixa de diálogo](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Encontre **logquery** a partir da**amostra**> **principal** > de**scala**> do **myApp** > **SRC** > **LogQuery**. Este tutorial usa **logQuery** para executar.

   ![Comandos para criar uma classe Scala do Projeto](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Conecte-se às suas piscinas spark

Inscreva-se na subscrição do Azure para se conectar às suas piscinas Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua subscrição Azure

1. A partir da barra de menus, navegue para **ver** > **ferramentas Windows** > **Azure Explorer**.

   ![IntelliJ IDEA mostra Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Do Azure Explorer, clique no nó **Azure** e, em seguida, selecione **Sign In**.

   ![IntelliJ IDEA explorador direito clique azul](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Na caixa de diálogo **'Sinal Azul',** escolha **Iniciar sessão do dispositivo**e, em seguida, selecione Iniciar **sessão**.

    ![IntelliJ IDEA azure sign-in](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Na caixa de diálogo de login do **dispositivo Azure,** clique em **Copiar&Abrir**.

   ![IntelliJ IDEA azure dispositivo login](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Na interface do navegador, colhe o código e, em seguida, clique **em Next**.

   ![Microsoft introduz diálogo de código para HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Introduza as suas credenciais Azure e, em seguida, feche o navegador.

   ![Microsoft introduz diálogo de e-mail para HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Depois de se ter inscrito, a caixa de diálogo **Select Subscriptions** lista todas as subscrições do Azure que estão associadas às credenciais. Selecione a sua subscrição e, em seguida, clique em **Selecionar**.

    ![A caixa de diálogo Selecionar Subscrições](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Do **Azure Explorer,** expanda **a Apache Spark em Synapse** para ver os espaços de trabalho que estão nas suas subscrições.

    ![Vista principal do IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Para ver as piscinas Spark, você pode expandir ainda mais um espaço de trabalho.

    ![Contas de armazenamento do Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Remote Run uma aplicação Spark Scala em uma piscina de faíscas

Depois de criar uma aplicação Scala, pode executá-la remotamente.

1. Abra a janela de configurações de **executar/depuração** clicando no ícone.

    ![A Aplicação de Ignição Submeter ao comando HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. Na janela de diálogo **'Configurações Executar/Depuração',** clique e, **+** em seguida, selecione Apache Spark em **Synapse**.

    ![A Aplicação de Ignição Submeter ao comando HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Na janela **Depuração de Configurações,** forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Piscinas de faíscas|Selecione as piscinas Spark nas quais pretende executar a sua aplicação.|
    |Selecione um Artefacto para submeter|Deixe a definição predefinida.|
    |Nome principal da classe|O valor predefinido é a classe principal do ficheiro selecionado. Pode mudar a classe selecionando a **...** elipse e escolhendo outra classe.|
    |Configurações de trabalho|Pode alterar a chave e os valores predefinidos. Para mais informações, consulte [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos da linha de comandos|Pode introduzir argumentos separados pelo espaço para a classe principal, se necessário.|
    |Frascos referenciados e ficheiros referenciados|Pode introduzir os caminhos para os frascos e ficheiros referenciados, se houver. Também pode navegar em ficheiros no sistema de ficheiros virtual Azure, que atualmente suporta apenas o cluster ADLS Gen 2. Para mais informações: [Configuração de Faísca apache](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) e [como carregar recursos para cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Armazenamento de upload de trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **Use o Azure Blob para fazer o upload** da lista de lançamentos.|
    |Conta de Armazenamento|Insira a sua conta de armazenamento.|
    |Chave de armazenamento|Introduza a sua chave de armazenamento.|
    |Recipiente de armazenamento|Selecione o seu recipiente de armazenamento da lista de depósitos assim que a Conta de **Armazenamento** e **a Chave** de Armazenamento estiverem inseridas.|

    ![A caixa de diálogo de submissão de faíscas](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Clique no ícone **SparkJobRun** para submeter o seu projeto à piscina Spark selecionada. O **separador Remote Spark Job no Cluster** mostra o progresso da execução do trabalho na parte inferior. Pode parar a aplicação clicando no botão vermelho.

    ![Janela de submissão de faísca sonuosa](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![A caixa de diálogo de submissão de faíscas](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Aplicações locais run/Debug Apache Spark

Pode seguir as instruções abaixo para configurar a sua corrida local e depuração local para o seu trabalho de Apache Spark.

### <a name="scenario-1-do-local-run"></a>Cenário 1: Faça a corrida local

1. Abra o diálogo **de configurações Run/Debug,** selecione o sinal de mais (**+**). Em seguida, selecione a Faísca Apache na opção **Synapse.** Introduza informações para **Nome**, **nome principal** da classe para guardar.

    ![Intellij Executar configurações locais de depuração](./media/intellij-tool-synapse/local-run-synapse.png)

    - Variáveis ambientais e Localização WinUtils.exe são apenas para utilizadores de janelas.
    - Variáveis ambientais: A variável ambiente do sistema pode ser detetada automaticamente se a tiver definido antes e não precisar de adicionar manualmente.
    - [WinUtils.exe Localização](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): Pode especificar a localização WinUtils clicando no ícone da pasta à direita.

2. Em seguida, clique no botão de reprodução local.

    ![Intellij Executar configurações locais de depuração](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Uma vez concluída a execução local, se o script incluir a saída, pode verificar o ficheiro de saída a partir da**__predefinição__** de **dados** > .

    ![Resultado da execução local do Projeto Intellij](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Cenário 2: Depuração local

1. Abra o script **LogQuery,** detete pontos de rutura.
2. Clique no ícone **de depuração local** para fazer depuração local.

    ![Resultado da execução local do Projeto Intellij](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Acesso e gestão do espaço de trabalho synapse

Pode realizar diferentes operações no Azure Explorer dentro do Azure Toolkit para o IntelliJ. A partir da barra de menus, navegue para **ver** > **ferramentas Windows** > **Azure Explorer**.

### <a name="launch-workspace"></a>Lançar espaço de trabalho

1. Do Azure Explorer, navegue até **à Faísca Apache em Synapse,** em seguida, expanda-o.

    ![Vista principal do IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Clique num espaço de trabalho e, em seguida, selecione Espaço de **trabalho de lançamento,** o site será aberto.

    ![Detalhes da aplicação de vista de emprego de faísca](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Detalhes da aplicação de vista de emprego de faísca](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Consola de faíscas

Pode executar a Spark Local Console (Scala) ou executar a Spark Livy Interactive Session Console (Scala).

### <a name="spark-local-console-scala"></a>Consola local spark (Scala)

Certifique-se de que satisfez o WINUTILS. Pré-requisito exe.

1. A partir da barra de menus, navegue para **executar** > configurações de**edição...**.
2. A partir da janela **de configurações Run/Debug,** na vidraça esquerda, navegue até **Apache Spark em Synapse** > **[Spark on Synapse] myApp**.
3. A partir da janela principal, selecione o separador **Local.**
4. Forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Variáveis de ambiente|Certifique-se de que o valor para HADOOP_HOME está correto.|
    |Localização WINUTILS.exe|Certifique-se de que o caminho está correto.|

    ![Configuração do conjunto de consolalocal](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Do Project, navegue até ao **myApp** > **src** > **main** > **scala** > **myApp**.
6. Da barra de menus, navegue até **tools** > **Spark consola** > **local Spark (Scala)**.
7. Em seguida, podem ser apresentados dois diálogos para lhe perguntar se pretende corrigir automaticamente dependências. Em caso afirmativo, selecione **Auto Fix**.

    ![IntelliJ IDEA Auto Fix diálogo1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Auto Fix diálogo2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. A consola deve parecer semelhante à imagem abaixo. No tipo `sc.appName`de janela da consola , e, em seguida, prima ctrl+Enter. O resultado será mostrado. Pode parar a consola local clicando num botão vermelho.

    ![Resultado da consola local IntelliJ IDEA](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Consola de sessão interativa Spark Livy (Scala)

É apoiado apenas no IntelliJ 2018.2 e 2018.3.

1. A partir da barra de menus, navegue para **executar** > configurações de**edição...**.

2. A partir da janela **de configurações Run/Debug,** na vidraça esquerda, navegue até **Apache Spark em sinapse** > **[Faísca na sinapse] myApp**.

3. A partir da janela principal, selecione o separador **Executar remotamente no separador Cluster.**

4. Forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Piscinas de faíscas|Selecione as piscinas Spark nas quais pretende executar a sua aplicação.|
    ||

    ![Configuração interativa do conjunto de consolas](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Do Project, navegue até ao **myApp** > **src** > **main** > **scala** > **myApp**.

6. Da barra de menus, navegue até **tools** > **Spark consola** > **Run Spark Livy Interactive Session Console (Scala)**.
7. A consola deve parecer semelhante à imagem abaixo. No tipo `sc.appName`de janela da consola , e, em seguida, prima ctrl+Enter. O resultado será mostrado. Pode parar a consola local clicando num botão vermelho.

    ![Resultado da consola interativa IntelliJ IDEA](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para a consola Spark

É conveniente prever o resultado do script enviando algum código para a consola local ou para a Consola De Sessão Interativa Livy (Scala). Pode destacar algum código no ficheiro Scala e, em seguida, clique no clique direito **Enviar Selection To Spark consola**. O código selecionado será enviado para a consola e será feito. O resultado será apresentado após o código na consola. A consola verificará os erros se existir.

   ![Enviar Seleção para a consola Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](../overview-what-is.md)
- [Crie uma nova piscina Apache Spark para um espaço de trabalho Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
