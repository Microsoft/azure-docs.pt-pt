---
title: Hue com Hadoop em clusters baseados em HDInsight Linux - Azure
description: Aprenda a instalar hue em clusters HDInsight e use túneis para encaminhar os pedidos para Hue. Use Hue para navegar no armazenamento e executar Hive ou Porco.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: fabc8b7b2a97b75959eb7d82723d6af6bc55bbe5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835483"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instale e utilize Hue em clusters Hadoop HDInsight

Aprenda a instalar hue em clusters HDInsight e use túneis para encaminhar os pedidos para Hue.

## <a name="what-is-hue"></a>O que é Hue?

Hue é um conjunto de aplicações Web usadas para interagir com um cluster Apache Hadoop. Você pode usar Hue para navegar no armazenamento associado a um cluster Hadoop (WASB, no caso dos clusters HDInsight), executar trabalhos de Colmeia e scripts de porco, e assim por diante. Os seguintes componentes estão disponíveis com instalações Hue num cluster Hadoop HDInsight.

* Editor de Colmeia de Cera de Abelha
* Porco Apache
* Gestor de metaloja
* Apache Oozie
* FileBrowser (que fala com o recipiente padrão WASB)
* Navegador de Emprego

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados e o Microsoft Support ajudará a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para envolver canais disponíveis para as tecnologias de código aberto onde se encontra uma profunda experiência para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Microsoft Q&Uma página de perguntas para HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com) . Também os projetos Apache têm sites de projetos [https://apache.org](https://apache.org) em, por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instale o Hue usando as ações do script

Utilize as informações na tabela abaixo para a sua Ação de Script. Consulte os [clusters HDInsight personalizados com as ações](hdinsight-hadoop-customize-cluster-linux.md) do script para obter instruções específicas sobre a utilização de Ações de Script.

> [!NOTE]  
> Para instalar hue em clusters HDInsight, o tamanho recomendado do nódoato é pelo menos A4 (8 núcleos, 14 GB de memória).

|Propriedade |Valor |
|---|---|
|Tipo de script:|- Personalizado|
|Name|Instalar Matiz|
|Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Tipo de nó):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Use hue com clusters HDInsight

Só pode ter uma conta de utilizador com hue em clusters regulares. Para acesso a vários utilizadores, ative o Pacote de [Segurança Empresarial](./domain-joined/hdinsight-security-overview.md) no cluster. O túnel sSH é a única maneira de aceder ao Hue no aglomerado quando estiver a funcionar. A escavação via SSH permite que o tráfego vá diretamente para o headnode do aglomerado onde hue está em execução. Depois de o cluster ter terminado o fornecimento, utilize os seguintes passos para utilizar o Hue num cluster HDInsight.

> [!NOTE]  
> Recomendamos a utilização do navegador Web Firefox para seguir as instruções abaixo.

1. Utilize as informações em [Use SSH Tunneling para aceder à Web UI Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outros UI's web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do seu sistema de cliente para o cluster HDInsight, e, em seguida, configurar o seu navegador Web para usar o túnel SSH como um proxy.

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez ligado, utilize o seguinte comando para obter o nome de domínio totalmente qualificado do cabeçalho principal:

    ```bash
    hostname -f
    ```

    Isto devolverá um nome semelhante ao seguinte:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Este é o nome de anfitrião do headnode principal onde está localizado o site Hue.

1. Use o navegador para abrir o portal Hue em `http://HOSTNAME:8888` . Substitua o NOME ANFITRIÃO pelo nome obtido no passo anterior.

   > [!NOTE]  
   > Quando iniciar sessão pela primeira vez, será solicitado a criar uma conta para iniciar sessão no portal Hue. As credenciais que especifica aqui serão limitadas ao portal e não estão relacionadas com as credenciais de utilizador de administração ou SSH que especificou durante a disponibilização do cluster.

    ![Janela de login do portal de tonalidade HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Especificar credenciais para o portal Hue")

### <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. A partir do portal Hue, selecione **Editores**de Consulta, e, em seguida, selecione **Hive** para abrir o editor da Colmeia.

    ![Portal de matiz HDInsight usa editor de colmeia](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Use colmeia")

2. No separador **Assistência,** em **Base de Dados,** deve ver **a colmeia.** Esta é uma tabela de amostras que é enviada com todos os clusters Hadoop no HDInsight. Introduza uma consulta de amostra no painel direito e veja a saída no separador **Resultados** no painel abaixo, como mostra a captura do ecrã.

    ![Consulta de hiv portal de matiz hDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Executar consulta da Colmeia")

    Também pode utilizar o separador **Chart** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Navegue no armazenamento do cluster

1. A partir do portal Hue, selecione **File Browser** no canto superior direito da barra de menus.
2. Por padrão, o navegador de ficheiros abre no diretório **/utilizador/utilizador/utilizador.** Selecione o corte dianteiro imediatamente antes do diretório do utilizador no caminho para ir para a raiz do recipiente de armazenamento Azure associado ao cluster.

    ![Navegador de ficheiros do portal de matizes HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Use o navegador de ficheiros")

3. Clique à direita num ficheiro ou pasta para ver as operações disponíveis. Utilize o botão **Upload** no canto direito para fazer o upload de ficheiros para o diretório atual. Utilize o botão **Novo** para criar novos ficheiros ou diretórios.

> [!NOTE]  
> O navegador de ficheiros Hue só pode mostrar o conteúdo do recipiente predefinido associado ao cluster HDInsight. Quaisquer contas/contentores de armazenamento adicionais que possa ter associado ao cluster não estarão acessíveis através do navegador de ficheiros. No entanto, os recipientes adicionais associados ao cluster estarão sempre acessíveis aos postos de trabalho da Colmeia. Por exemplo, se você entrar no comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` no editor da Hive, você pode ver o conteúdo de recipientes adicionais também. Neste comando, o **novo recipiente** não é o recipiente predefinido associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script usado para instalar Hue instala-o apenas no headnode primário do cluster.

1. Durante a instalação, vários serviços Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois do script terminar a instalação de Hue, pode levar algum tempo para que outros serviços hadoop comecem a funcionar. Isto pode afetar o desempenho de Hue inicialmente. Assim que todos os serviços começarem, o Hue estará totalmente funcional.

1. Hue não entende os empregos de Apache Tez, que é o padrão atual para a Colmeia. Se pretender utilizar o MapReduce como motor de execução da Colmeia, atualize o script para utilizar o seguinte comando no seu script:

         set hive.execution.engine=mr;

1. Com os clusters Linux, pode ter um cenário em que os seus serviços estão a funcionar no headnode principal enquanto o Gestor de Recursos pode estar a funcionar no secundário. Tal cenário pode resultar em erros (mostrados abaixo) ao utilizar hue para visualizar detalhes de empregos em EXECUÇÃo no cluster. No entanto, pode ver os detalhes do trabalho quando o trabalho estiver concluído.

   ![Mensagem de amostra de erro do portal hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Erro do portal hue")

   Isto deve-se a uma questão conhecida. Como uma suposições, modifique Ambari de modo que o Gestor de Recursos ativo também funcione no headnode primário.

1. Hue entende WebHDFS enquanto os clusters HDInsight usam o Armazenamento Azure usando `wasbs://` . Assim, o script personalizado usado com ação de script instala WebWasb, que é um serviço compatível com WebHDFS para falar com WASB. Assim, mesmo que o portal Hue diga HDFS em lugares (como quando move o rato sobre o Navegador de **Ficheiros),** deve ser interpretado como WASB.

## <a name="next-steps"></a>Passos seguintes

[Instale R nos clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilize a personalização do cluster para instalar R em clusters Hadoop HDInsight. R é uma linguagem e ambiente de código aberto para a computação estatística. Fornece centenas de funções estatísticas incorporadas e sua própria linguagem de programação que combina aspetos da programação funcional e orientada para objetos. Também fornece extensas capacidades gráficas.
