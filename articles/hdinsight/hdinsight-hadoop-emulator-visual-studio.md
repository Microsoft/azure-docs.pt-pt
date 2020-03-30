---
title: Ferramentas data lake para estúdio visual & Hortonworks - Azure HDInsight
description: Aprenda a usar as ferramentas Azure Data Lake para O Estúdio Visual com a caixa de areia Hortonworks a funcionar num VM local. Com estas ferramentas, você pode criar e executar trabalhos de Hive e Porco na caixa de areia, e ver a saída de emprego e história.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500159"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utilize as ferramentas Azure Data Lake para Estúdio Visual com a Caixa de Areia Hortonworks

O Lago de Dados Azure inclui ferramentas para trabalhar com aglomerados genéricos de Hadoop Apache. Este documento fornece os passos necessários para usar as ferramentas data lake com a Caixa de Areia Hortonworks funcionando numa máquina virtual local.

A utilização da Caixa de Areia Hortonworks permite-lhe trabalhar com hadoop localmente no seu ambiente de desenvolvimento. Depois de ter desenvolvido uma solução e querer implantá-la em escala, pode então mover-se para um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A Hortonworks Sandbox, a funcionar numa máquina virtual no seu ambiente de desenvolvimento. Este documento foi escrito e testado com a caixa de areia a funcionar no Oracle VirtualBox. Para obter informações sobre a configuração da caixa de areia, consulte o Get iniciado com a caixa de [areia Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) documento.

* Visual Studio.

* O [Azure SDK para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou posterior.

* As [ferramentas Azure Data Lake para Estúdio Visual.](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>Configure palavras-passe para a caixa de areia

Certifique-se de que a Caixa de Areia Hortonworks está a funcionar. Em seguida, siga os passos no Get iniciado no documento [Hortonworks Sandbox.](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) Estes passos configuram a palavra-passe para a conta SSH `root` e a conta Apache Ambari. `admin` Estas palavras-passe são usadas quando se conecta à caixa de areia do Estúdio Visual.

## <a name="connect-the-tools-to-the-sandbox"></a>Ligue as ferramentas à caixa de areia

1. Abra o Estúdio Visual, selecione **View**, e, em seguida, selecione **Server Explorer**.

2. A partir do **Server Explorer,** clique à direita na entrada **HDInsight** e, em seguida, selecione **Connect to HDInsight Emulator**.

    ![Server Explorer, com Connect to HDInsight Emulator em destaque](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Da caixa de diálogo **Emulator Connect to HDInsight,** introduza a palavra-passe que configurapara Ambari.

    ![Screenshot da caixa de diálogo, com caixa de texto de palavra-passe ambari em destaque](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **Seguinte** para continuar.

4. Utilize o campo **Password** para introduzir a `root` palavra-passe configurada para a conta. Deixe os outros campos pelo valor padrão.

    ![Screenshot da caixa de diálogo, com caixa de texto de palavra-passe raiz realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Selecione **Seguinte** para continuar.

5. Aguarde a validação dos serviços para terminar. Em alguns casos, a validação pode falhar e levar-o a atualizar a configuração. Se a validação falhar, selecione **'Actualizar'** e aguarde a configuração e verificação para que o serviço termine.

    ![Screenshot da caixa de diálogo, com botão de atualização realçado](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > O processo de atualização utiliza a Ambari para modificar a configuração Hortonworks Sandbox para o que é esperado pelas ferramentas data lake para o Estúdio Visual.

6. Depois de terminada a validação, selecione **Terminar** para completar a configuração.
    ![Screenshot da caixa de diálogo, com botão acabamento realçado](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Dependendo da velocidade do seu ambiente de desenvolvimento e da quantidade de memória atribuída à máquina virtual, pode levar vários minutos para configurar e validar os serviços.

Depois de seguir estes passos, tem agora uma entrada de **cluster local HDInsight** no Server Explorer, sob a secção **HDInsight.**

## <a name="write-an-apache-hive-query"></a>Escreva uma consulta de Apache Hive

A Hive fornece uma linguagem de consulta semelhante a SQL (HiveQL) para trabalhar com dados estruturados. Use os seguintes passos para aprender a executar consultas a pedido contra o cluster local.

1. No **Server Explorer,** clique à direita na entrada do cluster local que adicionou anteriormente e, em seguida, selecione **Escrever uma Consulta**de Colmeia .

    ![Screenshot do Server Explorer, com Write a Hive Query em destaque](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    É apresentada uma nova janela de consulta. Aqui pode escrever e submeter rapidamente uma consulta ao aglomerado local.

2. Na nova janela de consulta, insira o seguinte comando:

        select count(*) from sample_08;

    Para executar a consulta, selecione **Submeter** na parte superior da janela. Deixe os outros valores **(nome** de lote e servidor) nos valores predefinidos.

    ![Screenshot da janela de consulta, com o botão Enviar em destaque](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Também pode utilizar o menu drop-down ao lado de **Submeter** para selecionar **Advanced**. Opções avançadas permitem-lhe fornecer opções adicionais quando submete o trabalho.

    ![Screenshot da urticária de submissão script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Depois de submeter a consulta, o estado do trabalho aparece. O estado do trabalho mostra informações sobre o trabalho à medida que é processado por Hadoop. **O Estado** de Trabalho fornece o estatuto do trabalho. O estado é atualizado periodicamente, ou pode utilizar o ícone de atualização para refrescar o estado manualmente.

    ![Screenshot da caixa de diálogo Job View, com O Estado de Emprego em destaque](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Após as alterações do **Estado de Trabalho** para **Terminado,** é apresentado um gráfico acíclico direcionado (DAG). Este diagrama descreve o caminho de execução que foi determinado por Tez ao processar a consulta da Colmeia. Tez é o motor de execução padrão para a Colmeia no aglomerado local.

    > [!NOTE]  
    > Apache Tez também é o padrão quando está a usar clusters HDInsight baseados em Linux. Não é o padrão no HDInsight baseado no Windows. Para usá-lo lá, você `set hive.execution.engine = tez;` deve adicionar a linha ao início da sua consulta da Colmeia.

    Utilize o link saída de **trabalho** para visualizar a saída. Neste caso, é 823, o número de filas na mesa sample_08. Pode ver informações de diagnóstico sobre o trabalho utilizando os links de Registo de **Trabalho** e **Descarregamento de YARN.**

4. Também pode executar trabalhos de Colmeia interativamente, alterando o campo **De lote** para **Interativo.** Em seguida, selecione **Executar**.

    ![Screenshot dos botões Interativo e Executar em destaque](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Uma consulta interativa transmite o registo de saída gerado durante o processamento para a janela **de saída hiveServer2.**

    > [!NOTE]  
    > A informação é a mesma que está disponível a partir do link **De trabalho** após o fim de um trabalho.

    ![Screenshot da saída HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Criar um projeto da Colmeia

Você também pode criar um projeto que contém vários scripts hive. Utilize um projeto quando tiver scripts relacionados ou queira armazenar scripts num sistema de controlo de versão.

1. No Estúdio Visual, selecione **File**, **New**, e depois **Project**.

2. A partir da lista de **projetos, expandir Modelos,** expandir o Lago de **Dados Azure,** e depois selecionar **HIVE (HDInsight)**. A partir da lista de modelos, selecione Amostra de **Colmeia**. Introduza um nome e localização e, em seguida, selecione **OK**.

    ![Nova janela do projeto, com Lago de Dados Azure, Amostra de Colmeia, e OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

O projeto **Hive Sample** contém dois scripts, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. Pode submeter estes scripts utilizando o mesmo botão **Enviar** na parte superior da janela.

## <a name="create-an-apache-pig-project"></a>Criar um projeto Apache Pig

Enquanto a Hive fornece uma linguagem semelhante ao SQL para trabalhar com dados estruturados, o Porco trabalha executando transformações em dados. O porco fornece uma língua (Pig Latin) que lhe permite desenvolver um oleoduto de transformações. Para utilizar o Porco com o aglomerado local, siga estes passos:

1. Abra o Estúdio Visual e selecione **File**, **Novo,** e depois **Project**. A partir da lista de **projetos, expandir Modelos,** expandir o Lago de **Dados Azure,** e depois selecionar **Porco (HDInsight)**. A partir da lista de modelos, selecione **Aplicação**de porco . Introduza um nome, localização e, em seguida, selecione **OK**.

    ![Screenshot da janela do Novo Projeto, com Lago de Dados Azure, Porco, Aplicação de Porco, e OK em destaque](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Introduza o seguinte texto como o conteúdo do ficheiro **script.pig** que foi criado com este projeto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Enquanto o Porco usa uma linguagem diferente da Colmeia, a forma como gere os trabalhos é consistente entre ambas as línguas, através do botão **Enviar.** A seleção da gota ao lado **do Submit** apresenta uma caixa de diálogo de submissão avançada para porco.

    ![Screenshot de submeter script log box porco](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. O estado de trabalho e a saída também são apresentados, o mesmo que uma consulta da Hive.

    ![Screenshot de um trabalho de porco concluído](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Ver empregos

As ferramentas data Lake também permitem visualizar facilmente informações sobre empregos que foram executados em Hadoop. Use os seguintes passos para ver os trabalhos que têm sido executados no aglomerado local.

1. A partir do **Server Explorer,** clique no cluster local e, em seguida, selecione **'Ver Empregos'**. É apresentada uma lista de trabalhos que foram submetidos ao cluster.

    ![Screenshot do Server Explorer, com Ver Jobs em destaque](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Da lista de empregos, selecione um para ver os detalhes do trabalho.

    ![Screenshot do Job Browser, com um dos trabalhos em destaque](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    As informações apresentadas são semelhantes às que se vê depois de executar uma consulta de Hive ou Pig, incluindo links para visualizar a saída e registar informações.

3. Também pode modificar e reenviar o trabalho a partir daqui.

## <a name="view-hive-databases"></a>Ver bases de dados da Hive

1. No **Server Explorer,** expanda a entrada **de cluster local HDInsight** e, em seguida, expanda as Bases de **Dados da Hive**. As bases de dados **Predefinidas** e **xademo** no cluster local são apresentadas. Expandir uma base de dados mostra as tabelas dentro da base de dados.

    ![Screenshot do Server Explorer, com bases de dados expandidas](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Expandir uma mesa exibe as colunas para essa mesa. Para visualizar rapidamente os dados, clique à direita numa tabela e selecione **Ver Top 100 Rows**.

    ![Server Explorer, com tabela expandida e Ver Top 100 Rows selecionados](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Bases de dados e propriedades de mesa

Pode ver as propriedades de uma base de dados ou de uma mesa. A seleção **de Propriedades** apresenta detalhes para o item selecionado na janela de propriedades. Por exemplo, consulte as informações mostradas na seguinte imagem:

![Screenshot da janela Propriedades](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela, clique à direita numa base de dados e, em seguida, **selecione Criar Tabela**.

![Screenshot do Server Explorer, com tabela de criação em destaque](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Em seguida, pode criar a tabela usando um formulário. Na parte inferior da seguinte imagem, você pode ver o HiveQL cru que é usado para criar a tabela.

![Screenshot do formulário usado para criar uma tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Passos seguintes

* [Aprender as cordas da Caixa de Areia Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial Apache Hadoop - Começar com HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
