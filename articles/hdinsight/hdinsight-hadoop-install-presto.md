---
title: Instalar Presto em clusters do Linux de HDInsight do Azure
description: Saiba como instalar Presto e Airpal em clusters baseados em Linux Hadoop do HDInsight com ações de script.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 435c041bb5fb0a398f92914f943166108cc20080
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258348"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Instalar e utilizar o Presto em clusters do HDInsight baseado no Hadoop

Este artigo explica como instalar prontamente em clusters do HDInsight do Azure baseado no Hadoop ao utilizar as ações de script. Também irá aprender a instalar Airpal num cluster HDInsight Presto existente.

HDInsight também oferece o aplicativo Starburst Presto para clusters do Apache Hadoop. Para obter mais informações, consulte [instalar aplicações do Apache Hadoop de terceiros no Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Os passos neste artigo necessitam de um cluster de Hadoop do HDInsight 3.5, que utiliza o Linux. Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior. Para obter mais informações, consulte [versões do HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>O que é Presto?
[Presto](https://prestodb.io/overview.html) são um motor de consulta SQL distribuídos fast para grandes volumes de dados. Presto é adequado para consultas interativas de petabytes de dados. Para obter mais informações sobre os componentes de Presto e como elas funcionam em conjunto, consulte [conceitos Presto](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas. Support da Microsoft irá ajudar a isolar e resolver problemas relacionados com esses componentes.
> 
> Componentes personalizados, como Presto recebem suporte comercialmente razoável para o ajudar a resolver o problema. Esse suporte poderá resolver o problema. Ou poderá ser-lhe pedido para interagir com os canais disponíveis para as tecnologias de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Há muitos sites de Comunidade que podem ser utilizados. Os exemplos são [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com). 
>
> Projetos Apache também tem sites de projeto no [Web site do Apache](https://apache.org). Um exemplo é [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Instalar prontamente utilizando as ações de script

Esta secção explica como utilizar o script de exemplo, quando cria um novo cluster com o portal do Azure: 

1. Começar a aprovisionar um cluster através dos passos [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Certifique-se de criar o cluster utilizando o **personalizado** fluxo de criação do cluster. O cluster tem de cumprir os seguintes requisitos:

   * Tem de ser um cluster do Hadoop com o HDInsight versão 3.6.

   * Terá de utilizar armazenamento do Azure que o arquivo de dados. Utilizar o Presto no cluster que utilize o armazenamento do Azure Data Lake como a opção de armazenamento não é uma opção ainda.

     ![HDInsight, personalizado (tamanho, definições, aplicações)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na **definições avançadas** área, selecione **ações de Script**. Forneça as seguintes informações. Também pode escolher o **instalar Presto** opção para o tipo de script:
   
   * **NOME**. Introduza um nome amigável para a ação de script.
   * **URI do script de bash**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HEAD**. Selecione esta opção.
   * **FUNÇÃO DE TRABALHO**. Selecione esta opção.
   * **ZOOKEEPER**. Deixe esta caixa de verificação em branco.
   * **PARAMETERS**. Deixe este campo em branco.


3. Na parte inferior a **ações de Script** área, escolha a **selecione** botão para guardar a configuração. Por fim, escolha o **selecionar** na parte inferior do **definições avançadas** área para guardar as informações de configuração.

4. Continuar para aprovisionar o cluster, conforme descrito em [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > O Azure PowerShell, CLI clássica do Azure, o SDK de .NET do HDInsight ou modelos Azure Resource Manager também podem ser utilizados para aplicar ações de script. Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [clusters do HDInsight baseado em Linux personalizar utilizando as ações de script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Utilizar o Presto com o HDInsight

Para trabalhar com Presto num cluster do HDInsight, siga os passos seguintes:

1. Ligar ao cluster do HDInsight com SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Inicie o shell Presto ao executar o seguinte comando:
   
    `presto --schema default`

3. Executar uma consulta numa tabela de exemplo **hivesampletable**, que está disponível em todos os clusters do HDInsight por predefinição:
   
    `select count (*) from hivesampletable;`
   
    Por predefinição, [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) conectores para Presto já estão configuradas. O conector do ramo de registo está configurado para utilizar a instalação de ramo de registo predefinida. Portanto, todas as tabelas do Hive são automaticamente visíveis no Presto.

    Para obter mais informações, consulte [documentação Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Utilizar Airpal com Presto

[Airpal](https://github.com/airbnb/airpal#airpal) é uma interface de consulta baseada na web de código-fonte aberto para Presto. Para obter mais informações sobre Airpal, consulte [Airpal documentação](https://github.com/airbnb/airpal#airpal).

Siga os passos seguintes para instalar Airpal no nó de extremidade:

1. Ao utilizar o SSH, ligue-se ao nó principal do cluster HDInsight que Presto instalada:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Quando estiver ligado, execute o seguinte comando:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Pode ver o resultado semelhante ao seguinte JSON:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. A partir da saída, anote o valor para o **valor** propriedade. Este valor é necessário durante a instalação Airpal no nó periférico do cluster. A partir da saída anterior, o valor que precisa é **10.0.0.12:9090**.

4. Uso [este modelo](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) para criar um nó de extremidade de cluster do HDInsight. Forneça os valores mostrados na captura de ecrã seguinte.

    ![Implementação personalizada](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Selecione **Comprar**.

6. Depois das alterações são aplicadas à configuração do cluster, ao Airpal interface da web, efetuando os seguintes passos no [portal do Azure](https://portal.azure.com):

    1. No menu à esquerda, selecione **todos os serviços**.

    1. Sob **ANALYTICS**, selecione **clusters do HDInsight**.

    1. Selecione o cluster na lista, que abre a vista predefinida.

    1. A vista predefinida, em **configurações**, selecione **aplicativos**.

        ![HDInsight, aplicações](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Partir do **aplicações instaladas** página, localize a entrada de tabela para **airpal**. Selecione **Portal**.

        ![Aplicações instaladas](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Quando lhe for pedido, introduza as credenciais de administrador que especificou quando criou o cluster do HDInsight baseado no Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizar uma instalação Presto num cluster do HDInsight

Para personalizar a instalação, siga os passos seguintes:

1. Ao utilizar o SSH, ligue-se ao nó principal do cluster HDInsight que Presto instalada:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Faça as alterações de configuração no ficheiro `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Para obter mais informações sobre configuração Presto, consulte [opções de configuração Presto para clusters baseados no YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Parar e eliminar a instância em execução atual de Presto:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Inicie uma nova instância do Presto com a personalização:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Aguarde que a nova instância esteja pronta. Tome nota do endereço de coordenador Presto:

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Gerar dados de benchmark para clusters do HDInsight que executam o Presto

TPC-DS é a norma da indústria para medir o desempenho de muitos sistemas de suporte a decisões, incluindo sistemas de grandes volumes de dados. Pode usar Presto para gerar dados e avaliar o compara com os seus dados de benchmark do HDInsight. Para obter mais informações, consulte [tpcds hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Passos Seguintes
* [Instalar e utilizar Hue em clusters do HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). Hue é uma interface de Usuário que torna mais fácil criar, executar e guardar Apache Pig e tarefas do Hive do web.

* [Instalar o Apache Giraph nos clusters do Hadoop do HDInsight e utilizar Giraph para processar gráficos em grande escala](hdinsight-hadoop-giraph-install-linux.md). Utilize a personalização de cluster para instalar o Giraph nos clusters do HDInsight baseado no Hadoop. Com o Giraph, pode efetuar o processamento de gráficos com o Hadoop. Também pode ser utilizado com o Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
