---
title: Hue com o Hadoop em clusters baseados em Linux de HDInsight - Azure
description: Saiba como instalar a Hue em clusters do HDInsight e utilizar o túnel para encaminhar os pedidos para Hue. Utilize Hue para procurar no armazenamento e executar Hive ou Pig.
keywords: hue hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 9a5a3e7354d3d89a53e94553ed504df7c8117922
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314999"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar Hue em clusters do HDInsight Hadoop

Saiba como instalar a Hue em clusters do HDInsight e utilizar o túnel para encaminhar os pedidos para Hue.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="what-is-hue"></a>O que é Hue?
Hue é um conjunto de aplicações Web utilizado para interagir com um cluster do Apache Hadoop. Pode utilizar Hue para procurar o armazenamento associado um cluster do Hadoop (WASB, no caso de clusters do HDInsight), executar tarefas do Hive e Pig scripts e assim por diante. Os seguintes componentes estão disponíveis com instalações de Hue num cluster do HDInsight Hadoop.

* Editor do Hive beeswax
* Apache Pig
* Gestor de Metastore
* Apache Oozie
* FileBrowser (que comunica com o contentor predefinido WASB)
* Browser de trabalho

> [!WARNING]  
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas e Support da Microsoft irá ajudar a isolar e resolver problemas relacionados com esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isso pode resultar em resolver o problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Por exemplo, há muitos sites de Comunidade que podem ser utilizados, como: [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Também projetos Apache tem sites de projeto no [ https://apache.org ](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalar a Hue com ações de Script

O script para instalar a Hue num cluster do HDInsight baseado em Linux está disponível em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Pode utilizar este script para instalar a Hue em clusters com Blobs de armazenamento do Azure (WASB) ou armazenamento do Azure Data Lake como armazenamento predefinido.

Esta secção fornece instruções sobre como utilizar o script quando são aprovisionados clusters no portal do Azure.

> [!NOTE]  
> O Azure PowerShell, CLI clássica do Azure, o SDK de .NET do HDInsight ou modelos Azure Resource Manager também podem ser utilizados para aplicar ações de script. Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Iniciar o aprovisionamento de um cluster, utilizando os passos em [Provision HDInsight clusters no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não concluído o aprovisionamento.

   > [!NOTE]  
   > Para instalar a Hue em clusters do HDInsight, o tamanho de nó principal recomendada é, pelo menos, A4 (8 núcleos, 14 GB de memória).
   >
   >
2. Sobre o **configuração opcional** painel, selecione **ações de Script**e forneça as informações, conforme mostrado abaixo:

    ![Fornecer parâmetros de ação de script para Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "fornecer parâmetros de ação de script para Hue")

   * **NOME**: Introduza um nome amigável para a ação de script.
   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Marque esta opção.
   * **FUNÇÃO DE TRABALHO**: Deixe em branco.
   * **ZOOKEEPER**: Deixe em branco.
   * **PARÂMETROS**: Deixe em branco.
3. Na parte inferior a **ações de Script**, utilize o **selecione** botão para guardar a configuração. Por último, utilize o **selecionar** na parte inferior do **configuração opcional** painel para guardar as informações de configuração opcional.
4. Continuar o aprovisionamento do cluster, conforme descrito em [Provision HDInsight clusters no Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Utilizar Hue com clusters do HDInsight

Túnel SSH é a única forma de aceder a Hue num cluster quando ele estiver em execução. O túnel através de SSH permite o tráfego ir diretamente ao nó principal do cluster onde Hue está em execução. Após terminar o cluster ao aprovisionamento, utilize os seguintes passos para utilizar Hue num cluster do Linux de HDInsight.

> [!NOTE]  
> Recomendamos que utilize o navegador da web Firefox para seguir as instruções abaixo.
>
>

1. Utilize as informações em [utilizar túnel SSH para aceder à IU web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUS da web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do seu sistema de cliente para o cluster do HDInsight e, em seguida, configure seu navegador da Web para utilizar o túnel SSH como um proxy.

2. Depois de ter criado um túnel SSH e configurado o seu browser para tráfego de proxy através do mesmo, tem de encontrar o nome de anfitrião do nó principal primário. Pode fazê-lo ao ligar ao cluster através de SSH na porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` em que **nome de utilizador** é o seu nome de utilizador SSH e **CLUSTERNAME** é o nome do seu cluster.

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Assim que estiver ligado, utilize o seguinte comando para obter o nome de domínio completamente qualificado do nó principal primário:

        hostname -f

    Isto irá devolver um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Este é o nome de anfitrião do nó principal primário onde está localizado no site da Hue.
4. Utilizar o browser para abrir o portal da Hue em http:\//HOSTNAME:8888. Substitua o nome de anfitrião com o nome que obteve no passo anterior.

   > [!NOTE]  
   > Quando iniciar sessão pela primeira vez, será solicitado para criar uma conta para iniciar sessão no portal da Hue. As credenciais que especificar aqui serão limitadas para o portal e não estão relacionadas com o administrador do ou credenciais de utilizador SSH especificada durante o aprovisionamento do cluster.
   >
   >

    ![Inicie sessão no portal da Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "especificar credenciais para o portal da Hue")

### <a name="run-a-hive-query"></a>Executar uma consulta do Hive
1. A partir do portal da Hue, clique em **editores de consulta**e, em seguida, clique em **Hive** para abrir o editor do Hive.

    ![Utilizar o Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "utilizar o Hive")
2. Na **ajudar** separador, em **base de dados**, deverá ver **hivesampletable**. Esta é uma tabela de exemplo é fornecida com todos os clusters do Hadoop no HDInsight. Introduza uma consulta de exemplo no painel da direita e ver a saída no **resultados** separador no painel de abaixo, conforme mostrado na captura de ecrã.

    ![Executar consulta do Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "consulta de execução do Hive")

    Também pode utilizar o **gráfico** guia para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procurar o armazenamento de cluster
1. A partir do portal da Hue, clique em **Browser de ficheiros** no canto superior direito da barra de menus.
2. Por predefinição, o navegador de ficheiro é aberto no **/utilizador/omeuutilizador** diretório. Clique em barra à direita antes do diretório do utilizador no caminho para ir para a raiz do contentor de armazenamento do Azure associada ao cluster.

    ![Utilizar o browser de ficheiro](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "utilizar o browser de ficheiro")
3. Faça duplo clique num ficheiro ou pasta para ver as operações disponíveis. Utilize o **carregar** botão no canto direito para carregar ficheiros para o diretório atual. Utilize o **New** botão para criar novos ficheiros ou diretórios.

> [!NOTE]  
> O browser de ficheiro Hue só pode mostrar o conteúdo do contêiner predefinida associada ao cluster do HDInsight. Quaisquer contas/contentores de armazenamento adicionais que poderá ter associado o cluster não estará acessíveis através do browser de ficheiro. No entanto, os contentores adicionais associados ao cluster será sempre acessíveis para as tarefas do Hive. Por exemplo, se introduzir o comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` no editor do Hive, pode ver o conteúdo do também a contentores adicional. Neste comando, **newcontainer** não é o contentor predefinido associado a um cluster.
>
>

## <a name="important-considerations"></a>Considerações importantes
1. O script usado para instalar a Hue instala-o apenas no nó principal primário do cluster.

2. Durante a instalação, vários serviços do Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para que a atualização da configuração. Depois do script termina a instalar a Hue, poderá demorar algum tempo para outros serviços do Hadoop iniciar a cópia de segurança. Poderá afetar o desempenho da Hue inicialmente. Depois de todos os serviços for iniciado, Hue estará totalmente funcional.
3. Hue não entende tarefas do Apache Tez, que é o padrão atual para o Hive. Se pretender utilizar o MapReduce como o motor de execução do Hive, o script a utilizar o seguinte comando no script de atualização:

        set hive.execution.engine=mr;

4. Com os clusters do Linux, pode ter um cenário em que seus serviços em execução no nó principal primário enquanto o Gerenciador de recursos poderiam estar em execução no secundário. Um cenário como esse pode resultar em erros (mostrados abaixo) ao utilizar Hue para ver os detalhes das tarefas em execução no cluster. No entanto, pode ver os detalhes da tarefa quando a tarefa for concluída.

   ![Erro portal da Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "erro portal da Hue")

   Isso é devido a um problema conhecido. Como solução, modifique o Ambari, para que o Gestor de recursos do Active Directory também é executado no nó principal primário.
5. Hue compreende WebHDFS enquanto os clusters do HDInsight utilizar através do armazenamento do Azure `wasb://`. Então, o script personalizado utilizado com a ação de script instala webwasb Tomcat. tar, que é um serviço de compatíveis com WebHDFS para conversar com WASB. Assim, apesar do portal da Hue diz HDFS em locais (como quando move o mouse o **Browser de ficheiros**), devem ser interpretadas como WASB.

## <a name="next-steps"></a>Passos Seguintes
* [Instalar o Apache Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilize a personalização de cluster para instalar o Giraph nos clusters do HDInsight Hadoop. Giraph permite-lhe efetuar o processamento de gráficos usando o Hadoop, e pode ser utilizado com o Azure HDInsight.
* [Instalar o R nos clusters do HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilize a personalização de cluster para instalar o R nos clusters do HDInsight Hadoop. R é uma linguagem de código-fonte aberto e um ambiente para o cálculo estatístico. Ele fornece centenas de funções de estatísticas internas e a sua própria linguagem de programação que combina os aspectos da programação funcional e orientada a objeto. Ele também fornece extensas capacidades de gráficas.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
