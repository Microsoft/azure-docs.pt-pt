---
title: Matiz com Hadoop em clusters baseados em HDInsight Linux - Azure
description: Aprenda a instalar o Hue em clusters HDInsight e use túneis para encaminhar os pedidos para Hue. Use a Tonalidade para navegar no armazenamento e executar Hive ou Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: e80df5d1c3d2b2195e76622964406cc65c933a63
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546199"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instale e utilize o Hue nos clusters hdInsight Hadoop

Aprenda a instalar o Hue em clusters HDInsight e use túneis para encaminhar os pedidos para Hue.

## <a name="what-is-hue"></a>O que é Hue?

Hue é um conjunto de aplicações Web usadas para interagir com um cluster Apache Hadoop. Você pode usar Hue para navegar no armazenamento associado a um cluster Hadoop (WASB, no caso de clusters HDInsight), executar trabalhos de Colmeia e scripts de porco, e assim por diante. Os seguintes componentes estão disponíveis com instalações Hue num cluster HDInsight Hadoop.

* Editor de Colmeia de Cera de Abelha
* Porco-apache
* Gestor de metastore
* Apache Oozie
* FileBrowser (que fala com o recipiente padrão WASB)
* Navegador de Emprego

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados e o Microsoft Support ajudará a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para envolver canais disponíveis para as tecnologias de código aberto onde se encontram conhecimentos profundos para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Microsoft Q&Uma página de perguntas para HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com) . Também os projetos Apache têm sites de projeto [https://apache.org](https://apache.org) em , por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instale o Hue usando ações de script

Utilize as informações na tabela abaixo para a sua Ação do Script. Consulte [os clusters HDInsight personalizem-se com ações de script](hdinsight-hadoop-customize-cluster-linux.md) para obter instruções específicas sobre a utilização de ações de script.

> [!NOTE]  
> Para instalar o Hue nos clusters HDInsight, o tamanho recomendado do cabeçalho é pelo menos A4 (8 núcleos, 14 GB de memória).

|Propriedade |Valor |
|---|---|
|Tipo de script:|- Personalizado|
|Nome|Instalar Matiz|
|URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Tipo de nó(s):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Use Matiz com clusters HDInsight

Só pode ter uma conta de utilizador com o Hue em agrupamentos regulares. Para acesso a vários utilizadores, ative o [Pacote de Segurança Empresarial](./domain-joined/hdinsight-security-overview.md) no cluster. O túnel SSH é a única maneira de aceder ao Hue no aglomerado uma vez que esteja a funcionar. O túnel via SSH permite que o tráfego vá diretamente para o cabeçanode do cluster onde Hue está correndo. Depois de terminar o fornecimento do cluster, utilize os seguintes passos para utilizar o Hue num cluster HDInsight.

> [!NOTE]  
> Recomendamos a utilização do navegador Firefox para seguir as instruções abaixo.

1. Utilize as informações em [Use SSH Tunneling para aceder a Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outros UI's web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do seu sistema cliente para o cluster HDInsight e, em seguida, configurar o seu navegador Web para usar o túnel SSH como um representante.

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez ligado, utilize o seguinte comando para obter o nome de domínio totalmente qualificado do cabeçano primário:

    ```bash
    hostname -f
    ```

    Isto devolverá um nome semelhante ao seguinte:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Este é o nome de anfitrião do headnode primário onde o site Hue está localizado.

1. Utilize o navegador para abrir o portal Hue em `http://HOSTNAME:8888` . Substitua o NOME ANFITRIÃO pelo nome obtido no passo anterior.

   > [!NOTE]  
   > Quando iniciar sessão pela primeira vez, será solicitado que crie uma conta para iniciar sessão no portal Hue. As credenciais que especifica aqui serão limitadas ao portal e não estão relacionadas com as credenciais de administração ou de utilizador SSH especificadas enquanto fornece o cluster.

    ![Janela de login do portal de tonalidade HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Especificar credenciais para o portal Hue")

### <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. A partir do portal Hue, selecione **Os Editores de Consulta** e, em seguida, selecione **Hive** para abrir o editor da Hive.

    ![HdInsight hue portal usar editor de colmeia](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Use a Colmeia")

2. No separador **Assist,** em **Base de Dados,** deverá ver **ser tão esplecível.** Esta é uma tabela de amostras que é enviada com todos os clusters Hadoop em HDInsight. Introduza uma consulta de amostra no painel direito e veja a saída no **separador Resultados** no painel abaixo, como mostra a captura do ecrã.

    ![Consulta de colmeia do portal hdInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Executar consulta de colmeia")

    Também pode utilizar o **separador Gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Navegue no armazenamento do cluster

1. A partir do portal Hue, selecione **File Browser** no canto superior direito da barra de menu.
2. Por predefinição, o navegador de ficheiros abre no diretório **/utilizador/myuser.** Selecione o corte dianteiro imediatamente antes do diretório do utilizador no caminho para ir até à raiz do recipiente de armazenamento Azure associado ao cluster.

    ![HdInsight hue portal browser de ficheiros](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Use o navegador de arquivos")

3. Clique com o botão direito num ficheiro ou numa pasta para ver as operações disponíveis. Utilize o botão **Upload** no canto direito para enviar ficheiros para o diretório atual. Utilize o botão **Novo** para criar novos ficheiros ou diretórios.

> [!NOTE]  
> O navegador de ficheiros Hue só pode mostrar o conteúdo do recipiente predefinido associado ao cluster HDInsight. Quaisquer contas de armazenamento/contentores adicionais que possa ter associado ao cluster não estarão acessíveis usando o navegador de ficheiros. No entanto, os contentores adicionais associados ao cluster estarão sempre acessíveis aos trabalhos da Colmeia. Por exemplo, se introduzir o comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` no editor da Hive, também pode ver o conteúdo de recipientes adicionais. Neste comando, **o novo sistema** não é o recipiente padrão associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script utilizado para instalar o Hue instala-o apenas no cabeçano primário do cluster.

1. Durante a instalação, vários serviços Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois de o script terminar a instalação de Hue, pode levar algum tempo para que outros serviços Hadoop arranquem. Isto pode afetar inicialmente o desempenho de Hue. Uma vez que todos os serviços sejam iniciados, Hue estará totalmente funcional.

1. Hue não entende os empregos de Apache Tez, que é o padrão atual para a Colmeia. Se pretender utilizar o MapReduce como motor de execução da Colmeia, atualize o script para utilizar o seguinte comando no seu script:

   `set hive.execution.engine=mr;`

1. Com os clusters Linux, pode ter um cenário em que os seus serviços estão a funcionar na cabeça primária enquanto o Gestor de Recursos pode estar a funcionar no secundário. Tal cenário pode resultar em erros (mostrados abaixo) ao utilizar Hue para ver detalhes de trabalhos RUNNING no cluster. No entanto, pode ver os detalhes do trabalho quando o trabalho estiver concluído.

   ![Mensagem de amostra de erro do portal de tonalidade](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Erro do portal hue")

   Isto deve-se a um problema conhecido. Como uma solução alternativa, modifique Ambari para que o Gestor de Recursos ativo também corra no headnode primário.

1. Hue compreende webHDFS enquanto os clusters HDInsight usam o Azure Storage usando `wasbs://` . Assim, o script personalizado usado com ação de script instala WebWasb, que é um serviço compatível com WebHDFS para falar com WASB. Assim, mesmo que o portal Hue diga HDFS em lugares (como quando move o rato sobre o **Browser de Ficheiros),** deve ser interpretado como WASB.

## <a name="next-steps"></a>Passos seguintes

[Instale R em clusters HDInsight](./r-server/r-server-overview.md). Utilize a personalização do cluster para instalar R nos clusters HDInsight Hadoop. R é uma linguagem e ambiente de código aberto para a computação estatística. Fornece centenas de funções estatísticas incorporadas e a sua própria linguagem de programação que combina aspetos da programação funcional e orientada para os objetos. Também fornece extensas capacidades gráficas.