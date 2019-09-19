---
title: Matiz com Hadoop em clusters baseados em Linux do HDInsight – Azure
description: Saiba como instalar o matiz em clusters HDInsight e usar o túnel para rotear as solicitações para o matiz. Use o matiz para procurar o armazenamento e executar Hive ou Pig.
keywords: Hadoop de matiz
author: hrasheed-msft
ms.date: 12/11/2017
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.openlocfilehash: 8795d28cb36bacf4f57dba2048e30219047a9ac5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098640"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e usar o matiz em clusters Hadoop do HDInsight

Saiba como instalar o matiz em clusters HDInsight e usar o túnel para rotear as solicitações para o matiz.

## <a name="what-is-hue"></a>O que é o matiz?

O matiz é um conjunto de aplicativos Web usado para interagir com um cluster Apache Hadoop. Você pode usar o matiz para procurar o armazenamento associado a um cluster Hadoop (WASB, no caso de clusters HDInsight), executar trabalhos do hive e scripts Pig e assim por diante. Os componentes a seguir estão disponíveis com instalações de matiz em um cluster Hadoop do HDInsight.

* Editor do hive do BeesWax
* Apache Pig
* Gerenciador de metastore
* Apache Oozie
* FileBrowser (que se comunica com o contêiner padrão do WASB)
* Navegador de trabalhos

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight têm suporte total e Suporte da Microsoft ajudarão a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de que você envolva canais disponíveis para as tecnologias de software livre em que a profunda experiência para essa tecnologia é encontrada. Por exemplo, há muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache [https://apache.org](https://apache.org)têm sites de projeto em, por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalar o matiz usando ações de script

O script para instalar o matiz em um cluster HDInsight baseado em Linux está disponível https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh em. Você pode usar esse script para instalar o matiz em clusters com o WASB (BLOBs de armazenamento do Azure) ou Azure Data Lake Storage como armazenamento padrão.

Esta seção fornece instruções sobre como usar o script ao provisionar o cluster usando o portal do Azure.

> [!NOTE]  
> Azure PowerShell, a CLI clássica do Azure, o SDK do .NET do HDInsight ou modelos de Azure Resource Manager também podem ser usados para aplicar ações de script. Você também pode aplicar ações de script a clusters já em execução. Para obter mais informações, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento.

   > [!NOTE]  
   > Para instalar o matiz em clusters HDInsight, o tamanho recomendado do cabeçalho é de pelo menos A4 (8 núcleos, 14 GB de memória).

1. Na folha **configuração opcional** , selecione **ações de script**e forneça as informações conforme mostrado abaixo:

    ![Fornecer parâmetros de ação de script para matiz](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Fornecer parâmetros de ação de script para matiz")

   * **NOME**: Insira um nome amigável para a ação de script.
   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **CABEÇALHO**: Marque essa opção.
   * **TRABALHO**: Deixe este campo em branco.
   * **ZOOKEEPER**: Deixe este campo em branco.
   * **PARÂMETROS**: Deixe este campo em branco.

1. Na parte inferior das **ações de script**, use o botão **selecionar** para salvar a configuração. Por fim, use o botão **selecionar** na parte inferior da folha **configuração opcional** para salvar as informações de configuração opcionais.

1. Continue Provisionando o cluster conforme descrito em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Usar o matiz com clusters HDInsight

O túnel SSH é a única maneira de acessar o matiz no cluster quando ele está em execução. O túnel via SSH permite que o tráfego vá diretamente para o cabeçalho do cluster em que o matiz está sendo executado. Depois que o cluster tiver concluído o provisionamento, use as etapas a seguir para usar o matiz em um cluster HDInsight Linux.

> [!NOTE]  
> É recomendável usar o navegador da Web Firefox para seguir as instruções abaixo.

1. Use as informações em [usar o túnel SSH para acessar a interface do usuário da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do seu sistema cliente para o cluster HDInsight e, em seguida, configurar seu navegador da Web para usar o Túnel SSH como proxy.

2. Depois de criar um túnel SSH e configurar seu navegador para o tráfego de proxy através dele, você deve encontrar o nome do host do nó principal primário. Você pode fazer isso conectando-se ao cluster usando SSH na porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` em que **username** é o nome de usuário SSH e **ClusterName** é o nome do cluster.

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Uma vez conectado, use o seguinte comando para obter o nome de domínio totalmente qualificado do cabeçalho primário:

        hostname -f

    Isso retornará um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Esse é o nome do host do cabeçalho primário em que o site de matiz está localizado.

4. Use o navegador para abrir o portal de matiz em http\/:/hostname: 8888. Substitua HOSTNAME pelo nome obtido na etapa anterior.

   > [!NOTE]  
   > Ao fazer logon pela primeira vez, você será solicitado a criar uma conta para fazer logon no portal de matiz. As credenciais que você especificar aqui serão limitadas ao portal e não estarão relacionadas às credenciais de usuário de administrador ou SSH especificadas ao provisionar o cluster.

    ![Janela de logon do portal de matiz do HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Especificar credenciais para o portal de matiz")

### <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. No portal de matiz, clique em **editores de consulta**e, em seguida, clique em **Hive** para abrir o editor do hive.

    ![Portal de matiz do HDInsight usar o editor de Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Usar o hive")
2. Na guia **ajuda** , em **banco de dados**, você deve ver **hivesampletable**. Esta é uma tabela de exemplo que é fornecida com todos os clusters Hadoop no HDInsight. Insira uma consulta de exemplo no painel direito e veja a saída na guia **resultados** no painel abaixo, conforme mostrado na captura de tela.

    ![Consulta de hive do portal de matiz do HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Executar consulta do hive")

    Você também pode usar a guia **gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procurar o armazenamento de cluster

1. No portal de matiz, clique em **navegador de arquivos** no canto superior direito da barra de menus.
2. Por padrão, o navegador de arquivos é aberto no diretório **/User/MyUser** . Clique na barra à direita antes do diretório de usuário no caminho para ir para a raiz do contêiner de armazenamento do Azure associado ao cluster.

    ![Navegador de arquivos do portal de matiz do HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Usar navegador de arquivos")

3. Clique com o botão direito do mouse em um arquivo ou pasta para ver as operações disponíveis. Use o botão **carregar** no canto direito para carregar arquivos no diretório atual. Use o botão **novo** para criar novos arquivos ou diretórios.

> [!NOTE]  
> O navegador de arquivos de matiz só pode mostrar o conteúdo do contêiner padrão associado ao cluster HDInsight. Quaisquer contêineres/contas de armazenamento adicionais que você tenha associado ao cluster não poderão ser acessados usando o navegador de arquivos. No entanto, os contêineres adicionais associados ao cluster sempre estarão acessíveis para os trabalhos do hive. Por exemplo, se você inserir o comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` no editor do hive, também poderá ver o conteúdo de contêineres adicionais. Nesse comando, **newcontainer** não é o contêiner padrão associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script usado para instalar o matiz o instala somente no cabeçalho primário do cluster.

2. Durante a instalação, vários serviços do Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois que o script termina de instalar o matiz, pode levar algum tempo para que outros serviços do Hadoop sejam inicializados. Isso pode afetar o desempenho do matiz inicialmente. Depois que todos os serviços forem iniciados, o matiz será totalmente funcional.
3. O matiz não entende Apache Tez trabalhos, que é o padrão atual para o hive. Se você quiser usar o MapReduce como o mecanismo de execução do hive, atualize o script para usar o seguinte comando em seu script:

        set hive.execution.engine=mr;

4. Com os clusters do Linux, você pode ter um cenário em que os serviços estão em execução no cabeçalho primário, enquanto o Gerenciador de recursos pode estar em execução no secundário. Esse cenário pode resultar em erros (mostrados abaixo) ao usar o matiz para exibir detalhes de execução de trabalhos no cluster. No entanto, você pode exibir os detalhes do trabalho quando o trabalho for concluído.

   ![Mensagem de exemplo de erro do portal de matiz](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Erro do portal de matiz")

   Isso ocorre devido a um problema conhecido. Como alternativa, modifique Ambari para que o Gerenciador de recursos ativo também seja executado no cabeçalho primário.
5. O matiz compreende o WebHDFS enquanto os clusters HDInsight usam `wasb://`o armazenamento do Azure usando o. Portanto, o script personalizado usado com a ação de script instala o WebWasb, que é um serviço compatível com WebHDFS para conversar com o WASB. Portanto, embora o portal de matiz diga HDFS em locais (como quando você move o mouse sobre o **navegador de arquivos**), ele deve ser interpretado como WASB.

## <a name="next-steps"></a>Passos seguintes

* [Instale o Apache o giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização de cluster para instalar o O giraph em clusters Hadoop do HDInsight. O o giraph permite que você execute o processamento de grafo usando o Hadoop e ele pode ser usado com o Azure HDInsight.
* [Instale o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use a personalização de cluster para instalar o R em clusters Hadoop do HDInsight. R é um ambiente e linguagem de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece amplos recursos gráficos.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
