---
title: Tutorial - Azure Toolkit para IntelliJ (aplicação Spark)
description: Tutorial - Utilize o Kit de Ferramentas Azure para IntelliJ para desenvolver aplicações Spark, que são escritas em Scala, e submetê-las a uma piscina Apache Spark sem servidor.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 39f39b6f53944510b5f3692bb8b3fdd472004454
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121231"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Tutorial: Criar uma aplicação Apache Spark com IntelliJ usando um espaço de trabalho synapse

Este tutorial mostra-lhe como usar o Azure Toolkit para o plug-in IntelliJ para desenvolver aplicações Apache Spark, que são escritas em [Scala,](https://www.scala-lang.org/)e depois submetê-las a um conjunto apache spark sem servidor diretamente do ambiente de desenvolvimento integrado intelliJ (IDE). Pode utilizar o plug-in de várias formas:

- Desenvolva e submeta uma aplicação Scala Spark numa piscina de faíscas.
- Aceda aos recursos das suas piscinas Spark.
- Desenvolver e executar uma aplicação Scala Spark localmente.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
>
> - Utilize o kit de ferramentas Azure para o plug-in IntelliJ
> - Desenvolver aplicações Apache Spark
> - Submeter candidatura a piscinas Spark

## <a name="prerequisites"></a>Pré-requisitos

- [Versão comunitária intellij IDEA](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Ficha de kit de ferramentas Azure 3.27.0-2019.2 – Instalação do [repositório IntelliJ Plugin](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (versão 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin – Instalar no [repositório IntelliJ Plugin](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).
- O seguinte pré-requisito é apenas para utilizadores do Windows:

  Enquanto executa a aplicação local Spark Scala num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe falta no Windows.
  Para resolver este erro, faça o download dos [WinUtils executáveis](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\WinUtils\bin**. Em seguida, adicione a variável ambiental **HADOOP_HOME**, e desave o valor da variável para **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Crie uma aplicação Spark Scala para uma piscina de faíscas

1. Inicie a Ideia IntelliJ e selecione **Criar Novo Projeto** para abrir a janela New **Project.**
2. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.
3. Selecione **Spark Project com Amostras (Scala)** da janela principal.
4. A partir da lista de drop-down da **ferramenta Build,** selecione um dos seguintes tipos:

   - **Maven** para o suporte de assistente de criação de projeto Scala.
   - **SBT** para gerir as dependências e construção para o projeto Scala.

    ![IntelliJ IDEA Nova caixa de diálogo do projeto](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecione **Seguinte**.
6. Na janela **New Project,** forneça as seguintes informações:

    | Propriedade | Descrição |
    | ----- | ----- |
    |Nome do projeto| Insira um nome. Este tutorial utiliza `myApp`.|
    |&nbsp;Localização do projeto| Insira o local desejado para salvar o seu projeto.|
    |Projeto SDK| Pode estar em branco no seu primeiro uso da IDEA. Selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. A Sinapse só suporta **o Spark 2.4.0**.|
    |||

    ![Selecionando o Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecione **Concluir**. Pode levar alguns minutos até que o projeto fique disponível.
8. O projeto Spark cria automaticamente um artefacto para si. Para ver o artefacto, faça o seguinte funcionamento:

   a. Da barra de menu, navegue para a Estrutura do Projeto **de**  >  **Arquivo...**.

   b. A partir da janela Estrutura do **Projeto,** selecione **Artefactos.**

   c. Selecione **Cancelar** depois de ver o artefacto.

    ![Informação de artefacto na caixa de diálogo](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Encontre **o LogQuery** da amostra principal de scala do **myApp**  >  **src.**  >    >   >   >   Este tutorial usa **LogQuery** para executar.

   ![Comandos para a criação de uma classe Scala do Projeto](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Conecte-se às suas piscinas Spark

Inscreva-se na subscrição da Azure para ligar às suas piscinas Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua assinatura Azure

1. A partir da barra de menu, navegue para **ver o Tool**  >  **Windows**  >  **Azure Explorer**.

   ![IntelliJ IDEA mostra Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. A partir do Azure Explorer, clique à direita no nó **Azure** e, em seguida, selecione **Iniciar Sôm.**

   ![IntelliJ IDEA explorador à direita Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Na caixa **de diálogo Azure In** dialog, escolha Iniciar **sessão** e, em seguida, selecione **Iniciar sessão .**

    ![IntelliJ IDEA Azure adubá-lo](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Na caixa de diálogo **de início de sessão do Dispositivo Azure,** selecione **Copy&Open**.

   ![Login do dispositivo IntelliJ IDEA Azure](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Na interface do navegador, cole o código e, em seguida, selecione **Next**.

   ![Microsoft introduz diálogo de código para HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Insira as suas credenciais Azure e, em seguida, feche o navegador.

   ![Microsoft introduz diálogo de e-mail para HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Depois de ter assinado, a caixa de diálogo **Select Subscriptions** lista todas as subscrições do Azure que estão associadas às credenciais. Selecione a sua subscrição e, em seguida, **selecione Select**.

    ![A caixa de diálogo Selecionar Subscrições](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. A partir do **Azure Explorer,** expanda **o Apache Spark na Sinapse** para ver os espaços de trabalho que estão nas suas subscrições.

    ![IntelliJ IDEA Azure Explorer vista principal](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Para ver as piscinas Spark, você pode expandir ainda mais um espaço de trabalho.

    ![Contas de armazenamento do Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Remote Run uma aplicação Spark Scala em uma piscina de faíscas

Depois de criar uma aplicação Scala, pode executá-la remotamente.

1. Abrir a janela **configurações de execução/depuragem** selecionando o ícone.

    ![A Aplicação de Faísca submeter ao comando HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. Na janela de diálogo **de configurações Run/Debug,** selecione **+** e selecione **Apache Spark na Sinapse**.

    ![A Aplicação de Faísca submeter ao comando HDInsight 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Na janela **Configurações Run/Debug,** forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Piscinas de faíscas|Selecione as piscinas Spark nas quais pretende executar a sua aplicação.|
    |Selecione um artefacto para submeter|Deixe a definição predefinida.|
    |Nome da classe principal|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a elipsee escolhendo outra classe.|
    |Configurações de trabalho|Pode alterar a chave e valores predefinidos. Para mais informações, consulte [a Apache Livy REST API.](http://livy.incubator.apache.org./docs/latest/rest-api.html)|
    |Argumentos de linha de comando|Pode introduzir argumentos separados por espaço para a classe principal, se necessário.|
    |Frascos referenciados e ficheiros referenciados|Pode introduzir os caminhos para os frascos e ficheiros referenciados, se houver. Também pode navegar em ficheiros no sistema de ficheiros virtuais Azure, que atualmente apenas suporta o cluster ADLS Gen2. Para mais informações: [Configuração de faíscas Apache] https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) e Como carregar recursos para [cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Armazenamento de upload de trabalho|Expandir para revelar opções adicionais.|
    |Tipo de armazenamento|**Selecione Use Azure Blob para carregar** ou Use a conta de armazenamento **predefinida** do cluster para fazer o upload da lista de drop-down.|
    |Conta de Armazenamento|Insira a sua conta de armazenamento.|
    |Chave de armazenamento|Insira a sua chave de armazenamento.|
    |Recipiente de armazenamento|Selecione o seu recipiente de armazenamento da lista de drop-down assim que **a conta de armazenamento** e a chave de **armazenamento** erram.|

    ![A caixa de diálogo de submissão de faíscas 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Selecione o ícone **SparkJobRun** para submeter o seu projeto à piscina de Faíscas selecionada. O **trabalho de faísca remota no separador Cluster** mostra o progresso da execução do trabalho na parte inferior. Pode parar a aplicação selecionando o botão vermelho.

    ![Janela de submissão de faíscas apaches](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![A caixa de diálogo de submissão de faíscas 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Aplicações Local Run/Debug Apache Spark

Pode seguir as instruções abaixo para configurar a sua corrida local e depurar local para o seu trabalho de Apache Spark.

### <a name="scenario-1-do-local-run"></a>Cenário 1: Fazer corrida local

1. Abra o diálogo **de Configurações Run/Debug,** selecione o sinal mais **+** (). Em seguida, selecione a faísca Apache na opção **Sinapse.** Introduza informações para **nome,** **nome da classe principal** para guardar.

    ![Intellij Run depurar configurações locais executar 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - As variáveis ambientais e a localização WinUtils.exe são apenas para utilizadores de janelas.
    - Variáveis ambientais: A variável ambiente do sistema pode ser detetada automaticamente se a tiver definido antes e não precisar de adicionar manualmente.
    - [WinUtils.exe Localização](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): Pode especificar a localização WinUtils selecionando o ícone da pasta à direita.

2. Em seguida, selecione o botão de reprodução local.

    ![Intellij Run depurar configurações locais run 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Uma vez concluída a execução local, se o script incluir a saída, pode verificar o ficheiro de saída a partir do  >  **__predefinido dados__**.

    ![Intellij Project resultado de execução local 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Cenário 2: Fazer depuragem local

1. Abra o script **LogQuery,** desconte pontos de rutura.
2. Selecione o ícone **local de depuragem** para fazer depuragem local.

    ![Intellij Project resultado de execução local 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Acesso e gestão do Espaço de Trabalho Da Sinapse

Pode executar diferentes operações no Azure Explorer dentro do Azure Toolkit para IntelliJ. A partir da barra de menu, navegue para **ver o Tool**  >  **Windows**  >  **Azure Explorer**.

### <a name="launch-workspace"></a>Espaço de trabalho de lançamento

1. De Azure Explorer, navegue até **Apache Spark na Sinapse** e, em seguida, expanda-o.

    ![IntelliJ IDEA Azure Explorer vista principal](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Clique com o botão direito num espaço de trabalho e, em seguida, selecione **Launch workspace**, website será aberto.

    ![Detalhes da aplicação de visualização de emprego spark 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Detalhes da aplicação de visualização de emprego spark 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Consola de faíscas

Pode executar a Consola Local Spark (Scala) ou executar a Consola De Sessão Interativa Spark Livy (Scala).

### <a name="spark-local-console-scala"></a>Consola local de faísca (Scala)

Certifique-se de ter satisfeito o pré-requisito WINUTILS.EXE.

1. A partir da barra de menu, navegue para  >  **configurações de edição de execução...**.
2. A partir da janela **configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark na Synapse**  >  **[Faísca na Sinapse] myApp**.
3. A partir da janela principal, selecione o **separador Localmente Executar.**
4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Variáveis de ambiente|Certifique-se de que o valor para HADOOP_HOME está correto.|
    |WINUTILS.exe localização|Certifique-se de que o caminho está correto.|

    ![Configuração local do conjunto de consolas](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Do Project, navegue para **o myApp**  >  **src**  >  **main**  >  **scala**  >  **myApp**.
6. A partir da barra de menu, navegue para a consola **Tools**  >  **Spark**  >  **Run Spark Local Console(Scala)**.
7. Em seguida, podem ser apresentados dois diálogos para lhe perguntar se pretende corrigir automaticamente as dependências. Em caso afirmativo, selecione **Auto Fix**.

    ![IntelliJ IDEA Spark Auto Fix diálogo1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix diálogo2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. A consola deve ser semelhante à imagem abaixo. No tipo de janela da consola `sc.appName` , e, em seguida, prima ctrl+Enter. O resultado será mostrado. Pode parar a consola local selecionando o botão vermelho.

    ![Resultado da consola local IntelliJ IDEA](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Consola de sessão interativa Spark Livy (Scala)

Só é suportado no IntelliJ 2018.2 e 2018.3.

1. A partir da barra de menu, navegue para  >  **configurações de edição de execução...**.

2. A partir da janela **configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark na sinapse**  >  **[Faísca na sinapse] myApp**.

3. A partir da janela principal, selecione o **separador "Executar remotamente" no separador Cluster.**

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Nome da classe principal| Selecione o nome da classe principal.| 
    |Piscinas de faíscas|Selecione as piscinas Spark nas quais pretende executar a sua aplicação.|
    ||

    ![Configuração do conjunto de consolas interativas](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Do Project, navegue para **o myApp**  >  **src**  >  **main**  >  **scala**  >  **myApp**.

6. A partir da barra de menu, navegue para **ferramentas**  >  **spark consola** Run Spark  >  **Livy Interactive Session Console(Scala)**.
7. A consola deve ser semelhante à imagem abaixo. No tipo de janela da consola `sc.appName` , e, em seguida, prima ctrl+Enter. O resultado será mostrado. Pode parar a consola local selecionando o botão vermelho.

    ![Resultado da consola interativa Intellij IDEA](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para a consola Spark

Pode querer ver o resultado do script enviando algum código para a consola local ou consola de sessão interativa Livy (Scala). Para tal, pode destacar algum código no ficheiro Scala e, em seguida, clique com o botão direito **Enviar Seleção para a consola Spark**. O código selecionado será enviado para a consola e será feito. O resultado será apresentado após o código na consola. A consola verificará os erros existentes.

   ![Enviar Seleção para consola Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](../overview-what-is.md)
- [Crie uma nova piscina Apache Spark para um espaço de trabalho Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)