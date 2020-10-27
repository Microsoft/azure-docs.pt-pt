---
title: Introdução aos Serviços ML em Azure HDInsight
description: Saiba como usar os Serviços ML em HDInsight para criar aplicações para análise de big data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: b3a6fc2ff45d61d63118edc23f40f69f16db9131
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536152"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que são os Serviços ML em Azure HDInsight

O Microsoft Machine Learning Server está disponível como opção de implementação quando cria clusters HDInsight em Azure. O tipo de cluster que fornece esta opção chama-se **ML Services** . Esta capacidade proporciona acesso a pedido a métodos de análise adaptáveis e distribuídos em HDInsight.

ML Services on HDInsight fornece as mais recentes capacidades para análise baseada em R em conjuntos de dados de praticamente qualquer tamanho. Os conjuntos de dados podem ser carregados para o armazenamento de Azure Blob ou Data Lake. As suas aplicações baseadas em R podem utilizar os pacotes R de código aberto com mais de 8000. As rotinas no ScaleR, o pacote de análise de dados da Microsoft também estão disponíveis.

O nó de borda fornece um lugar conveniente para ligar ao cluster e executar os seus scripts R. O nó de borda permite executar as funções distribuídas paralelas do ScaleR através dos núcleos do servidor. Também pode executá-los através dos nós do cluster usando o Hadoop Map Reduce da ScaleR. Também pode utilizar contextos de computação Apache Spark.

Os modelos ou previsões que resultam da análise podem ser descarregados para uso no local. Também podem estar `operationalized` em outro lugar em Azure. Em particular, através do [Azure Machine Learning Studio (clássico)](https://studio.azureml.net)e [do serviço web.](../../machine-learning/classic/deploy-a-machine-learning-web-service.md)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Começar com os Serviços ML em HDInsight

Para criar um cluster ML Services em HDInsight, selecione o tipo de cluster **ML Services.** O tipo de cluster ML Services inclui o ML Server nos nós de dados e no nó de borda. O nó de borda serve como uma zona de aterragem para análise baseada em serviços ML. Consulte [os clusters Create Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para uma passagem sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Porquê escolher os Serviços ML em HDInsight?

Os serviços ML em HDInsight proporcionam os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação em IA da Microsoft e open-source

  O ML Services inclui um conjunto de algoritmos altamente adaptáveis e distribuídos, tais como [RevoscaleR,](/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)e [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Estes algoritmos podem funcionar em tamanhos de dados maiores do que o tamanho da memória física. Também funcionam em uma grande variedade de plataformas de forma distribuída. Saiba mais sobre a recolha dos [pacotes R personalizados](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) da Microsoft e [pacotes Python incluídos](/machine-learning-server/python-reference/introducing-python-package-reference) com o produto.
  
  A ML Services faz a ponte com estas inovações e contribuições da Microsoft provenientes da comunidade de código aberto (R, Python e kits de ferramentas de IA). Tudo em cima de uma única plataforma de nível empresarial. Qualquer pacote de aprendizagem automática de código aberto R ou Python pode funcionar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacionalização e administração simples, segura e de alta escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço para a operacionalização. Esta ação resulta em custos e atrasos inflacionados, incluindo o tempo de tradução para: modelos, iterações para mantê-los válidos e atuais, aprovação regulamentar e permissões de gestão.

  A ML Services oferece operacionalização de nível [empresarial.](/machine-learning-server/what-is-operationalization) Depois de um modelo de machine learning estar concluído, são precisos apenas alguns cliques para gerar APIs de serviços web. Estes [serviços web](/machine-learning-server/operationalize/concept-what-are-web-services) estão hospedados numa grelha de servidor na nuvem e podem ser integrados com aplicações de linha de negócio. A capacidade de implantação para uma grelha elástica permite-lhe escalar perfeitamente com as necessidades do seu negócio, tanto para pontuação em lote como em tempo real. Para obter instruções, consulte [Operacionalizar os Serviços ML em HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> O tipo de cluster ML Services em HDInsight é suportado apenas em HDInsight 3.6. O HDInsight 3.6 está previsto reformar-se a 31 de dezembro de 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais funcionalidades dos Serviços ML em HDInsight

As seguintes funcionalidades estão incluídas nos Serviços ML em HDInsight.

| Categoria de recurso | Descrição |
|------------------|-------------|
| Ativado por R | [Pacotes R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de código aberto de R, e infraestruturas de tempo de execução para execução de scripts. |
| Ativado por python | [Módulos Python](/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de código aberto de Python, e infraestruturas de tempo de execução para execução de scripts.
| [Modelos pré-treinados](/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimento de texto, pronto para marcar dados que fornece. |
| [Implantar e consumir](r-server-operationalize.md) | `Operationalize` o seu servidor e implementar soluções como um serviço web. |
| [Execução remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster ML Services na sua rede a partir da sua estação de trabalho do cliente. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços ML em HDInsight

O armazenamento predefinido para o sistema de ficheiros HDFS pode ser uma conta de Armazenamento Azure ou armazenamento de data lake Azure. Os dados enviados para armazenamento de cluster durante a análise são tornados persistentes. Os dados estão disponíveis mesmo depois de o cluster ser eliminado. Várias ferramentas podem lidar com a transferência de dados para o armazenamento. As ferramentas incluem a instalação de upload baseada no portal da conta de armazenamento e o utilitário AzCopy.

Você pode permitir o acesso a lojas adicionais blob e data lake durante a criação de cluster. Não se limita a opção de armazenamento primário em uso.  Consulte [as opções de Armazenamento Azure para Serviços ML em artigo HDInsight](./r-server-storage.md) para saber mais sobre a utilização de várias contas de armazenamento.

Também pode utilizar os Ficheiros Azure como opção de armazenamento para utilização no nó de borda. O Azure Files permite ações de ficheiros criadas no Azure Storage para o sistema de ficheiros Linux. Para obter mais informações, consulte [as opções de Armazenamento Azure para Serviços ML em HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Acesso ML Services nó de borda

Pode ligar-se ao Microsoft ML Server no nó de borda utilizando um browser ou SSH/PuTTY. A consola R é instalada por padrão durante a criação do cluster.  

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R

Os seus scripts R podem utilizar qualquer um dos mais de 8000 pacotes R de código aberto. Também pode utilizar as rotinas paralelas e distribuídas da biblioteca ScaleR. Os scripts são executados no nó de borda dentro do intérprete R nesse nó. Exceto para etapas que chamam scaler funções com um contexto de cálculo de Redução de Mapa (RxHadoopMR) ou Spark (RxSpark). As funções funcionam de forma distribuída através dos nós de dados que estão associados aos dados. Para obter mais informações sobre opções de contexto, consulte [opções de contexto computacional para serviços ML em HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` um modelo

Quando a modelação dos seus dados estiver concluída, `operationalize` o modelo para fazer previsões para novos dados, quer a partir do Azure, quer no local. Este processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação em HDInsight

Para marcar em HDInsight, escreva uma função R. A função chama o seu modelo para fazer previsões para um novo ficheiro de dados que carregou na sua conta de armazenamento. Em seguida, guarde as previsões de volta para a conta de armazenamento. Pode executar esta rotina a pedido no nó de borda do seu cluster ou utilizando um trabalho programado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação em Azure Machine Learning (AML)

Para marcar usando o Azure Machine Learning, utilize o pacote Azure Machine Learning R de código aberto conhecido como [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) para publicar o seu modelo como um serviço web Azure. Por conveniência, este pacote está pré-instalado no nó de borda. Em seguida, utilize as instalações em Azure Machine Learning para criar uma interface de utilizador para o serviço web e, em seguida, ligue para o serviço web conforme necessário para a pontuação. Em seguida, converta os objetos do modelo ScaleR em objetos de modelo de código aberto equivalentes para utilização com o serviço web. Utilize funções de coação ScaleR, tais como `as.randomForest()` modelos baseados em conjunto, para esta conversão.

### <a name="score-on-premises"></a>Pontuação no local

Para marcar no local depois de criar o seu modelo: serialize o modelo em R, descarregue-o, desescute-o e, em seguida, use-o para obter novos dados. Pode obter novos dados utilizando a abordagem descrita anteriormente no Score in HDInsight ou utilizando [serviços web](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maioria dos pacotes R que utiliza são necessários no nó de borda, uma vez que a maioria dos passos dos seus scripts R são executados lá. Para instalar pacotes R adicionais no nó de borda, pode utilizar o `install.packages()` método em R.

Se estiver apenas a usar rotinas de biblioteca ScaleR, normalmente não precisa de pacotes R adicionais. Poderá necessitar de pacotes adicionais para a execução **do RxExec** ou **do RxDataStep** nos nós de dados.

Os pacotes adicionais podem ser instalados com uma ação de script depois de criar o cluster. Para obter mais informações, consulte [Gerir os Serviços ML no cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar configurações de memória De Mapa de Apache Hadoop

A memória disponível para os Serviços ML pode ser modificada quando está a executar um trabalho mapReduce. Para modificar um cluster, use a UI Apache Ambari para o seu cluster. Para obter instruções sobre a UI Ambari, consulte [os clusters HDInsight utilizando o Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

A memória disponível para os Serviços ML pode ser alterada utilizando interruptores Hadoop na chamada para **RxHadoopMR** :

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Dimensione o seu cluster

Um cluster de Serviços ML existente em HDInsight pode ser dimensionado para cima ou para baixo através do portal. Ao aumentar, ganha-se capacidade adicional para tarefas de processamento maiores. Pode reduzir um aglomerado quando está inativo. Para obter instruções sobre como escalar um cluster, consulte [os clusters HdInsight .](../hdinsight-administer-use-portal-linux.md)

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção do OS é feita nos VMs Linux subjacentes num cluster HDInsight durante o período de folga. Normalmente, a manutenção é feita às 3:30 da manhã (hora local da VM) todas as segundas e quintas-feiras. As atualizações não afetam mais de um quarto do cluster de cada vez.

Trabalhar pode abrandar durante a manutenção. No entanto, ainda devem estar concluídos. Qualquer software personalizado ou dados locais que tenha preservado através destes eventos de manutenção, a menos que ocorra uma falha catastrófica que exija uma reconstrução de cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções IDE para Serviços ML em HDInsight

O nó de borda Linux de um cluster HDInsight é a zona de aterragem para análise baseada em R. Versões recentes do HDInsight fornecem um IDE baseado no navegador do RStudio Server no nó de borda. O RStudio Server é mais produtivo do que a consola R para desenvolvimento e execução.

Um IDE de ambiente de trabalho pode aceder ao cluster através de um contexto remoto de cálculo MapReduce ou Spark. As opções incluem: [As Ferramentas R para Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio e StatET baseado em Eclipse da Walware.

Aceda à consola R no nó de borda digitando **R** na origem do comando. Ao utilizar a interface da consola, é conveniente desenvolver script R num editor de texto. Em seguida, corte e cole secções do seu script na consola R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster ML Services HDInsight são estruturados de forma semelhante a outros tipos de cluster HDInsight. Baseiam-se no tamanho dos VMs subjacentes através do nome, dados e nós de borda. Elevações de horas de núcleo também. Para obter mais informações, consulte [os preços da HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como utilizar os Serviços ML em clusters HDInsight, consulte os seguintes artigos:

* [Execute um script R num cluster de Serviços ML em Azure HDInsight usando o Servidor RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções de armazenamento para cluster ML Services em HDInsight](r-server-storage.md)