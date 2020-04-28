---
title: Introdução aos serviços ML no Azure HDInsight
description: Saiba como utilizar os Serviços ML no HDInsight para criar aplicações para análise de big data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 1dd716a279f7a09e7d9152ee34ff5c7bdac201dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188247"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que é ml serviços em Azure HDInsight

O Microsoft Machine Learning Server está disponível como uma opção de implementação quando cria clusters HDInsight em Azure. O tipo de cluster que fornece esta opção chama-se **ML Services**. Esta capacidade proporciona acesso a pedido a métodos de análise adaptáveis e distribuídos no HDInsight.

Os serviços ML no HDInsight fornecem as mais recentes capacidades para análises baseadas em R em conjuntos de dados de praticamente qualquer tamanho. Os conjuntos de dados podem ser carregados para armazenamento de Azure Blob ou Data Lake. As suas aplicações baseadas em R podem utilizar os pacotes R de código aberto de 8000+ . As rotinas em ScaleR, o pacote de análise de dados da Microsoft também estão disponíveis.

O nó de borda proporciona um lugar conveniente para ligar ao cluster e executar os seus scripts R. O nó de borda permite executar as funções distribuídas paralelas ScaleR através dos núcleos do servidor. Também pode executá-los através dos nós do cluster usando o Mapa de Hadoop da ScaleR. Também pode utilizar contextos de computação Apache Spark.

Os modelos ou previsões resultantes da análise podem ser descarregados para uso no local. Também podem `operationalized` estar em outro lugar em Azure. Em particular, através do [Azure Machine Learning Studio (clássico)](https://studio.azureml.net)e [do serviço web.](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Começar com os Serviços ML no HDInsight

Para criar um cluster ml services no HDInsight, selecione o tipo de cluster **ml Services.** O tipo de cluster ml Services inclui o Servidor ML nos nós de dados e o nó de borda. O nó de borda serve como uma zona de aterragem para análises baseadas em serviços ML. Consulte os [clusters Create Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para uma passagem sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Porquê escolher os Serviços ML no HDInsight?

Os serviços ML no HDInsight proporcionam os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação de IA da Microsoft e open-source

  Os Serviços ML incluem um conjunto altamente adaptável e distribuído de algoritmos como [O RevoscaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Estes algoritmos podem trabalhar em tamanhos de dados maiores do que o tamanho da memória física. Eles também funcionam em uma grande variedade de plataformas de forma distribuída. Saiba mais sobre a coleção de [pacotes R personalizados](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) da Microsoft e [pacotes Python incluídos](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) no produto.
  
  Ml Services faz a ponte entre estas inovações e contribuições da Microsoft provenientes da comunidade de código aberto (R, Python e kits de ferramentas de IA). Tudo em cima de uma única plataforma de nível empresarial. Qualquer pacote de aprendizagem automática de código aberto R ou Python pode funcionar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacionalização e administração simples, seguras e de alta escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço para a operacionalização. Esta ação resulta em custos e atrasos inflacionados, incluindo o tempo de tradução para: modelos, iterações para mantê-los válidos e atuais, aprovação regulamentar e permissões de gestão.

  Ml Services oferece [operacionalização](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)de nível empresarial. Depois de um modelo de aprendizagem automática ser concluído, basta alguns cliques para gerar APIs de serviços web. Estes [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) estão hospedados numa grelha de servidor na nuvem e podem ser integrados com aplicações de linha de negócio. A capacidade de implantar para uma grelha elástica permite-lhe escalar perfeitamente com as necessidades do seu negócio, tanto para pontuação em tempo real. Para obter instruções, consulte [Operacionalize os Serviços ML no HDInsight](r-server-operationalize.md).

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
| Ativado por R | [Pacotes R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de código aberto de R, e infraestrutura de tempo de execução de script. |
| Python-habilitado | [Módulos Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de código aberto de Python, e infraestrutura de tempo de execução de scripts.
| [Modelos pré-treinados](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimento de texto, pronto para marcar dados que fornece. |
| [Implantar e consumir](r-server-operationalize.md) | `Operationalize`o seu servidor e implementar soluções como um serviço web. |
| [Execução remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster ML Services na sua rede a partir da sua estação de trabalho de cliente. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços ML no HDInsight

O armazenamento predefinido para o sistema de ficheiros HDFS pode ser uma conta de Armazenamento Azure ou armazenamento de lagos de dados azure. Os dados enviados para o armazenamento do cluster durante a análise são persistentes. Os dados estão disponíveis mesmo após a eliminação do cluster. Várias ferramentas podem lidar com a transferência de dados para armazenamento. As ferramentas incluem a instalação de upload baseada no portal da conta de armazenamento e o utilitário AzCopy.

Você pode permitir o acesso a lojas de lagos Blob e Data adicionais durante a criação de cluster. Não se limita pela opção de armazenamento principal em uso.  Consulte as opções de [Armazenamento Azure para serviços ML no artigo da HDInsight](./r-server-storage.md) para saber mais sobre a utilização de várias contas de armazenamento.

Também pode utilizar o Azure Files como opção de armazenamento para utilização no nó de borda. O Azure Files permite as ações de ficheirocriadas no Armazenamento Azure para o sistema de ficheiros Linux. Para mais informações, consulte as opções de [Armazenamento Azure para serviços ML no HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Nó de borda de acesso ML Serviços

Pode ligar-se ao Microsoft ML Server no nó de borda utilizando um browser, ou SSH/PuTTY. A consola R é instalada por padrão durante a criação do cluster.  

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R

Os seus scripts R podem utilizar qualquer um dos pacotes R de código aberto de 8000+. Também pode utilizar as rotinas paralelizadas e distribuídas da biblioteca ScaleR. Os scripts correm no nó de borda dentro do intérprete R naquele nó. Exceto por passos que chamam funções scaleR com um contexto de computação de redução de mapa (RxHadoopMR) ou Spark (RxSpark). As funções funcionam de forma distribuída pelos nódosos de dados que estão associados aos dados. Para obter mais informações sobre opções de contexto, consulte as opções de [contexto computacionais para os serviços ML no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize`um modelo

Quando a sua modelação `operationalize` de dados estiver completa, o modelo para fazer previsões para novos dados, quer do Azure quer no local. Este processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação em HDInsight

Para marcar no HDInsight, escreva uma função R. A função chama o seu modelo para fazer previsões para um novo ficheiro de dados que carregou na sua conta de armazenamento. Em seguida, guarde as previsões de volta para a conta de armazenamento. Você pode executar esta rotina on-demand no nó de borda do seu cluster ou usando um trabalho programado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação em Aprendizagem automática Azure (AML)

Para pontuar utilizando o Azure Machine Learning, utilize o pacote Azure Machine Learning R de código aberto conhecido como [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) para publicar o seu modelo como um serviço web Azure. Por conveniência, este pacote é pré-instalado no nó de borda. Em seguida, utilize as instalações do Azure Machine Learning para criar uma interface de utilizador para o serviço web e, em seguida, ligue para o serviço web conforme necessário para pontuar. Em seguida, converta objetos do modelo ScaleR em objetos de modelo de código aberto equivalentes para utilização com o serviço web. Utilize funções de coação `as.randomForest()` ScaleR, como para modelos baseados em conjunto, para esta conversão.

### <a name="score-on-premises"></a>Pontuação no local

Para marcar no local após criar o seu modelo: serialize o modelo em R, descarregue-o, desserialize-o e, em seguida, use-o para marcar novos dados. Pode obter novos dados utilizando a abordagem descrita anteriormente no Score in HDInsight ou utilizando [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o aglomerado

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maioria dos pacotes R que utiliza são necessários no nó da borda, uma vez que a maioria dos passos dos seus scripts R correm lá. Para instalar pacotes R adicionais no nó `install.packages()` de borda, pode utilizar o método em R.

Se está apenas a usar as rotinas da biblioteca ScaleR, normalmente não precisa de pacotes R adicionais. Pode necessitar de pacotes adicionais para a execução **rxExec** ou **RxDataStep** nos nódosos de dados.

Os pacotes adicionais podem ser instalados com uma ação de script depois de criar o cluster. Para mais informações, consulte [Gerir os Serviços ML no cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar mapa do Hadoop ApacheReduzir as definições de memória

A memória disponível para os Serviços ML pode ser modificada quando está a executar um trabalho mapReduce. Para modificar um cluster, utilize a UI Apache Ambari para o seu cluster. Para instruções ambari UI, consulte [Gerir os clusters HDInsight utilizando o Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

A memória disponível para os serviços ML pode ser alterada utilizando interruptores Hadoop na chamada para **RxHadoopMR:**

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Escala o seu cluster

Um cluster de serviços ML existente no HDInsight pode ser dimensionado para cima ou para baixo através do portal. Ao escalonar, ganha-se capacidade adicional para tarefas de processamento maiores. Pode-se escalar um aglomerado quando está inativo. Para obter instruções sobre como escalar um cluster, consulte [Gerir os clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção do OS é feita nos VMs linux subjacentes num cluster HDInsight durante o horário de folga. Normalmente, a manutenção é feita às 3:30 da manhã (hora local da VM) todas as segundas e quintas-feiras. As atualizações não têm impacto em mais de um quarto do cluster de cada vez.

Os trabalhos de funcionamento podem abrandar durante a manutenção. No entanto, ainda devem correr para a conclusão. Qualquer software personalizado ou dados locais que você tenha preservado em todos estes eventos de manutenção, a menos que ocorra uma falha catastrófica que requer uma reconstrução do cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções IDE para serviços ML no HDInsight

O nó de borda Linux de um cluster HDInsight é a zona de aterragem para análise baseada em R. Versões recentes do HDInsight fornecem um IDE baseado no navegador do RStudio Server no nó de borda. O RStudio Server é mais produtivo do que a consola R para desenvolvimento e execução.

Um IDE de ambiente de trabalho pode aceder ao cluster através de um contexto remoto mapReduce ou Spark compute. As opções incluem: [As Ferramentas R](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) da Microsoft para estúdio visual (RTVS), RStudio e StatET baseado em Eclipse da Walware.

Aceda à consola R no nó da borda digitando **R** no pedido de comando. Ao utilizar a interface da consola, é conveniente desenvolver o script R num editor de texto. Em seguida, corte e cola secções do seu script na consola R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster ML Services HDInsight são estruturados de forma semelhante a outros tipos de cluster HDInsight. Baseiam-se no tamanho dos VMs subjacentes através do nome, dados e nós de borda. Elevações de horas de núcleo também. Para mais informações, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como utilizar os Serviços ML em clusters HDInsight, consulte os seguintes artigos:

* [Execute um script R num cluster de serviços ML em Azure HDInsight usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções de armazenamento para cluster de serviços ML no HDInsight](r-server-storage.md)
