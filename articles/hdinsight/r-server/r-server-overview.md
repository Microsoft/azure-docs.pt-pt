---
title: Introdução aos serviços ML no Azure HDInsight
description: Saiba como utilizar os Serviços ML no HDInsight para criar aplicações para análise de big data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122367"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que é ml serviços em Azure HDInsight

O Microsoft Machine Learning Server está disponível como uma opção de implementação quando cria clusters HDInsight em Azure. O tipo de cluster que fornece esta opção chama-se **ML Services**. Esta capacidade fornece aos cientistas de dados, estatísticos e programadores R acesso a métodos de análise escaláveis e distribuídos no HDInsight.

Os serviços ML no HDInsight fornecem as mais recentes capacidades para análisebaseada em R em conjuntos de dados de praticamente qualquer tamanho, carregados para armazenamento Azure Blob ou Data Lake. Uma vez que o cluster ML Services é construído em código r aberto, as aplicações baseadas em R que você constrói podem alavancar qualquer um dos pacotes R de código aberto de 8000+. As rotinas em ScaleR, o pacote de análise de dados da Microsoft também estão disponíveis.

O nó de borda de um cluster fornece um lugar conveniente para ligar ao cluster e executar os seus scripts R. Com um nó de borda, tem a opção de executar as funções distribuídas paralelas do ScaleR através dos núcleos do servidor do nó de borda. Também pode executá-los através dos nós do cluster utilizando os contextos de cálculo de Calculo Map Reduce ou Apache Spark.

Os modelos ou previsões resultantes da análise podem ser descarregados para uso no local. Também podem ser operacionalizados noutros locais do Azure, em particular através do [serviço web Azure](../../machine-learning/studio/deploy-a-machine-learning-web-service.md) [Machine Learning Studio (clássico).](https://studio.azureml.net)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Começar com os Serviços ML no HDInsight

Para criar um cluster ml services em Azure HDInsight, selecione o tipo de cluster **ml Services** ao criar um cluster HDInsight utilizando o portal Azure. O tipo de cluster ml Services inclui o Servidor ML nos nós de dados do cluster e num nó de borda, que serve como uma zona de aterragem para análises baseadas em Serviços ML. Consulte os [clusters Create Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para uma passagem sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Porquê escolher os Serviços ML no HDInsight?

Os serviços ML no HDInsight proporcionam os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação de IA da Microsoft e open-source

  Os Serviços ML incluem um conjunto altamente escalável e distribuído de algoritmos como [O RevoscaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) que podem trabalhar em tamanhos de dados maiores do que o tamanho da memória física, e funcionam numa grande variedade de plataformas de forma distribuída. Saiba mais sobre a coleção de [pacotes R personalizados](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) da Microsoft e [pacotes Python incluídos](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) no produto.
  
  Ml Services faz a ponte entre estas inovações e contribuições da Microsoft provenientes da comunidade de código aberto (R, Python e kits de ferramentas de IA) tudo em cima de uma única plataforma de nível empresarial. Qualquer pacote de aprendizagem automática de código aberto R ou Python pode funcionar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacionalização e administração simples, seguras e de alta escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço para a operacionalização. Isto resulta em custos e atrasos inflacionados, incluindo o tempo de tradução para modelos, iterações para mantê-los válidos e atuais, aprovação regulamentar, e gestão de permissões através da operacionalização.

  Ml Services oferece [operacionalização](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)de nível empresarial, na forma de que, após a conclusão de um modelo de machine learning, são necessários apenas alguns cliques para gerar APIs de serviços web. Estes [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) estão hospedados numa grelha de servidor na nuvem e podem ser integrados com aplicações de linha de negócio. A capacidade de implantar para uma grelha elástica permite-lhe escalar perfeitamente com as necessidades do seu negócio, tanto para pontuação em tempo real. Para obter instruções, consulte [Operacionalize os Serviços ML no HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> O tipo de cluster ml Services no HDInsight é suportado apenas no HDInsight 3.6. O HDInsight 3.6 está previsto reformar-se a 31 de dezembro de 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais funcionalidades dos serviços ML no HDInsight

As seguintes funcionalidades estão incluídas nos Serviços ML no HDInsight.

| Categoria de recurso | Descrição |
|------------------|-------------|
| Ativado por R | [Pacotes R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de fonte aberta de R, e infraestrutura de tempo de execução de script. |
| Python-habilitado | [Módulos Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de código aberto de Python, e infraestrutura de tempo de execução de scripts.
| [Modelos pré-treinados](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimento de texto, pronto para marcar dados que fornece. |
| [Implantar e consumir](r-server-operationalize.md) | Operacionalize o seu servidor e implemente soluções como um serviço web. |
| [Execução remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster ML Services na sua rede a partir da sua estação de trabalho de cliente. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços ML no HDInsight

O armazenamento predefinido para o sistema de ficheiros HDFS dos clusters HDInsight pode ser associado a uma conta de Armazenamento Azure ou a um Armazenamento de Lago de Dados Azure. Esta associação garante que quaisquer dados enviados para o armazenamento do cluster durante a análise são persistentes e os dados estão disponíveis mesmo após o aglomerado ser eliminado. Existem várias ferramentas para o manuseamento da transferência de dados para a opção de armazenamento que seleciona, incluindo a facilidade de carregamento baseada no portal da conta de armazenamento e o utilitário [AzCopy.](../../storage/common/storage-use-azcopy.md)

Tem a opção de permitir o acesso a lojas adicionais de lagos Blob e Data durante o processo de provisionamento de clusters, independentemente da principal opção de armazenamento em uso.  Consulte as opções de [Armazenamento Azure para serviços ML no artigo da HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para saber mais sobre a utilização de várias contas de armazenamento.

Também pode utilizar [o Azure Files](../../storage/files/storage-how-to-use-files-linux.md) como opção de armazenamento para utilização no nó de borda. O Azure Files permite-lhe montar uma partilha de ficheiros que foi criada no Armazenamento Azure para o sistema de ficheiros Linux. Para obter mais informações sobre estas opções de armazenamento de dados para serviços ML no cluster HDInsight, consulte as opções de [Armazenamento Azure para serviços ML no HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Nó de borda de acesso ML Serviços

Pode ligar-se ao Microsoft ML Server no nó de borda utilizando um browser. É instalado por padrão durante a criação do cluster.  Também pode ligar-se ao nó de borda do cluster a partir da linha de comando utilizando sSH/PuTTY para aceder à consola R.

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R

Os scripts R que cria e executa podem utilizar qualquer um dos pacotes R de código aberto de 8000+ para além das rotinas paralelas e distribuídas disponíveis na biblioteca ScaleR. Em geral, um guião que é executado com serviços ML no nó de borda funciona dentro do intérprete R nesse nó. As exceções são as etapas que precisam de chamar uma função ScaleR com um contexto de cálculo que é definido para Hadoop Map Reduce (RxHadoopMR) ou Spark (RxSpark). Neste caso, a função funciona de forma distribuída através desses dados (tarefa) nós do cluster que estão associados aos dados referenciados. Para obter mais informações sobre as diferentes opções de contexto computacional, consulte as opções de [contexto computacionais para os serviços ML no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalizar um modelo

Quando a sua modelação de dados estiver completa, pode operacionalizar o modelo para fazer previsões para novos dados, quer a partir do Azure quer no local. Este processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação em HDInsight

Para marcar no HDInsight, escreva uma função R que ligue para o seu modelo para fazer previsões para um novo ficheiro de dados que carregou na sua conta de armazenamento. Em seguida, guarde as previsões de volta para a conta de armazenamento. Você pode executar esta rotina on-demand no nó de borda do seu cluster ou usando um trabalho programado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação em Aprendizagem automática Azure (AML)

Para pontuar utilizando o Azure Machine Learning, utilize o pacote Azure Machine Learning R de código aberto conhecido como [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) para publicar o seu modelo como um serviço web Azure. Por conveniência, este pacote é pré-instalado no nó de borda. Em seguida, utilize as instalações do Azure Machine Learning para criar uma interface de utilizador para o serviço web e, em seguida, ligue para o serviço web conforme necessário para pontuar.

Se escolher esta opção, tem de converter quaisquer objetos do modelo ScaleR em objetos de modelo de código aberto equivalentes para utilização com o serviço web. Utilize funções de coação `as.randomForest()` ScaleR, como para modelos baseados em conjunto, para esta conversão.

### <a name="score-on-premises"></a>Pontuação no local

Para marcar no local após a criação do seu modelo, pode serializar o modelo em R, descarregá-lo, desserializá-lo e, em seguida, usá-lo para marcar novos dados. Pode obter novos dados utilizando a abordagem descrita anteriormente no [Score in HDInsight](#score-in-hdinsight) ou utilizando [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o aglomerado

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maioria dos pacotes R que utiliza são necessários no nó da borda, uma vez que a maioria dos passos dos seus scripts R correm lá. Para instalar pacotes R adicionais no nó `install.packages()` de borda, pode utilizar o método em R.

Se estiver apenas a utilizar rotinas da biblioteca ScaleR através do cluster, normalmente não precisa de instalar pacotes R adicionais nos nós de dados. No entanto, poderá necessitar de pacotes adicionais para apoiar a utilização da execução **rxExec** ou **RxDataStep** nos nós de dados.

Nesses casos, os pacotes adicionais podem ser instalados com uma ação de script após criar o cluster. Para mais informações, consulte [Gerir os Serviços ML no cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar mapa do Hadoop ApacheReduzir as definições de memória

Um cluster pode ser modificado para alterar a quantidade de memória que está disponível para os Serviços ML quando está a executar um trabalho MapReduce. Para modificar um cluster, utilize o Apache Ambari UI que está disponível através da lâmina do portal Azure para o seu cluster. Para obter instruções sobre como aceder ao UI Ambari para o seu cluster, consulte [Gerir os clusters HDInsight utilizando o Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória que está disponível para os Serviços ML utilizando interruptores Hadoop na chamada para **RxHadoopMR** da seguinte forma:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Escala o seu cluster

Um cluster de serviços ML existente no HDInsight pode ser dimensionado para cima ou para baixo através do portal. Ao escalonar, pode ganhar a capacidade adicional que poderá necessitar para tarefas de processamento maiores, ou pode reduzir um cluster quando está inativo. Para obter instruções sobre como escalar um cluster, consulte [Gerir os clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção para aplicar patches de OS e outras atualizações é realizada nos VMs linux subjacentes num cluster HDInsight durante o horário de folga. Normalmente, a manutenção é feita às 3:30 da manhã (com base na hora local para o VM) todas as segundas e quintas-feiras. As atualizações são realizadas de tal forma que não impactam mais de um quarto do cluster de cada vez.

Uma vez que os nós da cabeça são redundantes e nem todos os nós de dados são impactados, quaisquer empregos que estejam a funcionar durante este tempo podem abrandar. No entanto, ainda devem correr para a conclusão. Qualquer software personalizado ou dados locais que tenha é preservado nestes eventos de manutenção, a menos que ocorra uma falha catastrófica que exija uma reconstrução do cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções IDE para serviços ML no HDInsight

O nó de borda Linux de um cluster HDInsight é a zona de aterragem para análise baseada em R. Versões recentes do HDInsight fornecem uma instalação padrão do RStudio Server no nó de borda como um IDE baseado no navegador. A utilização do RStudio Server como IDE para o desenvolvimento e execução de scripts R pode ser consideravelmente mais produtiva do que apenas usar a consola R.

Além disso, pode instalar um IDE de ambiente de trabalho e usá-lo para aceder ao cluster através da utilização de um contexto remoto de computação MapReduce ou Spark. As opções incluem [as Ferramentas R](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) da Microsoft para estúdio visual (RTVS), RStudio e [StatET](http://www.walware.de/goto/statet)baseado em Eclipse da Walware.

Além disso, pode aceder à consola R no nó da borda digitando **R** no pedido de comando Linux após a ligação via SSH ou PuTTY. Ao utilizar a interface da consola, é conveniente executar um editor de texto para o desenvolvimento do script R noutra janela, e cortar e colar secções do seu script na consola R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster ML Services HDInsight são estruturados de forma semelhante aos preços de outros tipos de cluster HDInsight. Baseiam-se no dimensionamento dos VMsubjacentes através do nome, dados e nós de borda, com a adição de uma elevação de horas de núcleo. Para mais informações, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como utilizar os Serviços ML em clusters HDInsight, consulte os seguintes tópicos:

* [Execute um script R num cluster de serviços ML em Azure HDInsight usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções de armazenamento para cluster de serviços ML no HDInsight](r-server-storage.md)
