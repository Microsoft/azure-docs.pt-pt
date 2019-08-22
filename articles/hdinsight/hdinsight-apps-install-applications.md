---
title: Instalar aplicações de terceiros no Azure HDInsight
description: Saiba como instalar aplicações do Hadoop de terceiros no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9b96294362400891113812e308ff59bd0e438b90
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873356"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicações do Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar uma aplicação de terceiros [Apache Hadoop](https://hadoop.apache.org/) aplicação no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação do HDInsight é um aplicativo que os utilizadores podem instalar num cluster do HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

A lista seguinte mostra as aplicações publicadas:

|Aplicação |Tipo (s) de cluster | Descrição |
|---|---|---|
|[Plataforma AtScale Intelligence](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |O AtScale transforma seu cluster HDInsight em um servidor OLAP de expansão, permitindo que você consulte bilhões de linhas de dados interativamente usando as ferramentas de BI que você já conhece, tem e adora – do Microsoft Excel, Power BI, tableau software para QlikView. |
|[CDAP para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP é a primeira plataforma de integração unificada para Big Data que acelera o tempo de implantação para o Hadoop e permite que ele forneça dados de autoatendimento. Software livre e extensível, o CDAP remove as barreiras para a inovação. Requisitos: 4 nós de região, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |A plataforma escalonável de autoatendimento do Datameer para preparar, explorar e governar seus dados para análise acelera a transformação de dados de várias fontes complexos em informações valiosas prontas para os negócios, fornecendo insights mais rápidos e inteligentes em escala empresarial. |
|[Dataiku DSS no HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS em uma plataforma de ciência de dados corporativos que permite que cientistas de dados e analistas de dados colaborem para projetar e executar novos produtos de dados e serviços com mais eficiência, transformando dados brutos em previsões impactantes. |
|[Aplicativo WANdisco Fusion HDI](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Manter os dados consistentes em um ambiente distribuído é um grande desafio de operações de dados. O WANdisco Fusion, uma plataforma de software de classe empresarial, resolve esse problema habilitando a consistência de dados não estruturados em qualquer ambiente. |
|[H2O SparklingWater para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |A água do H2O sparkling dá suporte aos seguintes algoritmos distribuídos: GLM, Bayes ingênua, floresta aleatória distribuída, máquina de aumento de gradiente, redes neurais profundas, aprendizado profundo, K-means, PCA, modelos de classificação baixa generalizados, detecção de anomalias, autocodificadores. |
|[Striim para integração de dados em tempo real ao HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striim (pronuncia-se "Stream") é uma plataforma de integração de dados de streaming + inteligência de ponta a ponta, permitindo ingestão contínua, processamento e análise de fluxos de dados diferentes. |
|[Jumbune Enterprise – acelerando o BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Em um alto nível, o Jumbune auxilia empresas pela, 1. Acelerando o tez, o MapReduce & o hive baseado no mecanismo Spark, o Java, o desempenho da carga de trabalho escalar. 2. Monitoramento proativo do cluster Hadoop, 3. Estabelecendo o gerenciamento de qualidade de dados no sistema de arquivos distribuído. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Da plataforma Apache Kylin, o Kyligence Enterprise permite BI em Big Data. Como um mecanismo OLAP corporativo no Hadoop, o Kyligence Enterprise capacita o analista de negócios a arquitetar BI no Hadoop com metodologia de data warehouse e BI padrão do setor. |
|[Explosão de presto para o Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto é um mecanismo de consulta SQL distribuído rápido e escalonável. Criado para a separação de armazenamento e computação, Presto é perfeito para consultar dados no armazenamento do Azure Data Lake, bases de dados do armazenamento de Blobs do Azure, SQL e NoSQL e outras origens de dados. |
|[Coletor de dados do StreamSets para nuvem do HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |O coletor de dados StreamSets é um mecanismo leve e poderoso que transmite dados em tempo real. Use o coletor de dados para rotear e processar dados em seus fluxos de dados. Ele vem com uma licença de avaliação de 30 dias. |
|[Trifator Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Trifator o Wrangler Enterprise para HDInsight dá suporte a Wrangling de dados de toda a empresa para qualquer escala de dados. O custo da execução de trifator no Azure é uma combinação dos custos de assinatura de trifator, além dos custos de infraestrutura do Azure para as máquinas virtuais. |
|[Plataforma de dados de Unifi](https://unifisoftware.com/platform/) |Hadoop,HBase,Storm,Spark |A plataforma de dados unificada é um pacote totalmente integrado de ferramentas de dados de autoatendimento projetadas para capacitar o usuário de negócios a lidar com os desafios de dados que impulsionam a receita incremental, reduzem os custos ou a complexidade operacional. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Aplicativo de dados Unravel para o cluster HDInsight Spark. |
|[Eswater o catálogo de dados controlado por ia](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Preponha catálogos, organiza e rege dados usando o ia para marcar automaticamente os dados com os termos comerciais. Catálogo de alfabetizada do waterline corporativos é um componente de sucesso críticos, para análises de autoatendimento, conformidade e governação e iniciativas de gestão de TI. |

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e utilizar o Azure PowerShell e CLI clássica do Azure para implementar o modelo.  Ver [Apache Hadoop criar clusters no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Instalar uma aplicação do HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **clusters HDInsight**de**análise** > .
3. Selecione um cluster HDInsight na lista.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Na categoria **configurações** , selecione **aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![menu do portal das aplicações do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecione **+ Adicionar** no menu. Você pode ver uma lista de aplicativos disponíveis.  Se **+ Add** estiver esmaecido, isso significará que não há nenhum aplicativo para esta versão do cluster HDInsight.
   
    ![aplicações do HDInsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Selecione um dos aplicativos disponíveis e siga as instruções para aceitar os termos legais.

Você pode ver o status da instalação nas notificações do portal (selecione o ícone de sino na parte superior do Portal). Depois da aplicação está instalada, a aplicação é apresentada na lista de aplicações instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster
Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. Para instalar aplicativos durante a criação do cluster usando o portal do Azure, use a opção **personalizado** em vez da opção **criação rápida** padrão.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Aplicações do HDInsight e apresentar as propriedades**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **clusters HDInsight**de**análise** > .
3. Selecione um cluster HDInsight na lista.
4. Na categoria **configurações** , selecione **aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecione um dos aplicativos instalados para mostrar a propriedade. As listas de propriedades:

    |Propriedade | Descrição |
    |---|---|
    |Nome da aplicação |Nome do aplicativo. |
    |State |Status do aplicativo. |
    |Página Web |A URL do aplicativo Web que você implantou no nó de borda. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. |
    |Ponto final de SSH |Você pode usar o SSH para se conectar ao nó de borda. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Descrição | Descrição do aplicativo. |

6. Para eliminar uma aplicação, clique com o botão direito do aplicativo e, em seguida, clique em **eliminar** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Ligar ao nó de extremidade
Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Resolução de problemas
Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passos Seguintes
* [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [INGLÊS Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do HDInsight: Saiba como definir aplicativos do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Resource Manager para criar clusters do HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

