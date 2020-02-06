---
title: Instalar aplicações de terceiros no Azure HDInsight
description: Saiba como instalar aplicações Apache Hadoop de terceiros no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9643e1f77be400a89a8dfaecdc2f7e1205a1938b
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031322"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicações do Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar uma aplicação [Apache Hadoop](https://hadoop.apache.org/) de terceiros no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação do HDInsight é um aplicativo que os utilizadores podem instalar num cluster do HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

A lista seguinte mostra as aplicações publicadas:

|Aplicação |Tipo de cluster(s) | Descrição |
|---|---|---|
|[Plataforma de Inteligência AtScale](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |O AtScale transforma o seu cluster HDInsight num servidor OLAP em escala, permitindo-lhe consultar milhares de milhões de linhas de dados utilizando interativamente as ferramentas BI que já conhece, possui e ama – desde o Microsoft Excel, Power BI, Tableau Software até ao QlikView. |
|[CDAP para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |O CDAP é a primeira plataforma unificada de integração para big data que acelera o tempo para valorizar o Hadoop e permite que a TI forneça dados de self-service. Fonte aberta e extensível, o CDAP elimina barreiras à inovação. Requisitos: 4 nós da região, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |A plataforma autosserviço escalável da Datameer para preparar, explorar e reger os seus dados para análise acelera a transformação de dados complexos multisource em informações valiosas prontas para o negócio, fornecendo informações mais rápidas e inteligentes à escala da empresa. |
|[Dataiku DSS no HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS numa plataforma de ciência de dados da empresa que permite que cientistas de dados e analistas de dados colaborem para projetar e executar novos produtos e serviços de dados de forma mais eficiente, transformando dados brutos em previsões impactantes. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark,HBase,Storm,Kafka |Manter dados consistentes num ambiente distribuído é um desafio massivo de operações de dados. A WANdisco Fusion, uma plataforma de software de classe empresarial, resolve este problema permitindo a consistência de dados não estruturada em qualquer ambiente. |
|[H2O SparklingWater para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water suporta os seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Deteção de Anomalias, Autoencoders. |
|[Striim para integração de dados em tempo real para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Tempestade,Faísca,Kafka |Striim (pronuncia-se "stream") é uma plataforma de integração de dados de streaming de ponta a ponta + plataforma de inteligência, permitindo a ingestão contínua, processamento e análise de fluxos de dados díspares. |
|[Análise do BigData acelerador da Empresa Jumbune](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |A um nível elevado, Jumbune ajuda as empresas por, 1. Accelerating Tez, MapReduce & Spark engine based Hive, Java, Scala workload performance. 2. Monitorização proactiva do cluster do hadoop, 3. Estabelecer a gestão da qualidade dos dados no sistema de ficheiros distribuídos. |
|[Empresa Kyligence](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Powered by Apache Kylin, Kyligence Enterprise Enables BI on Big Data. Como motor oLAP da empresa em Hadoop, a Kyligence Enterprise capacita o analista de negócios ao arquiteto BI em Hadoop com o armazém de dados padrão da indústria e a metodologia BI. |
|[Starburst Presto para Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto é um motor de consulta SQL distribuído rápido e escalável. Criado para a separação de armazenamento e computação, Presto é perfeito para consultar dados no armazenamento do Azure Data Lake, bases de dados do armazenamento de Blobs do Azure, SQL e NoSQL e outras origens de dados. |
|[StreamSets Data Collector para HDInsight Cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector é um motor leve e poderoso que transmite dados em tempo real. Utilize o Data Collector para encaminhar e processar dados nos seus fluxos de dados. Vem com uma licença de 30 dias de julgamento. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise for HDInsight suporta a luta de dados em toda a empresa para qualquer escala de dados. O custo de funcionamento da Trifacta no Azure é uma combinação de custos de subscrição da Trifacta mais os custos de infraestrutura Azure para as máquinas virtuais. |
|[Plataforma de Dados Unifi](https://unifisoftware.com/platform/) |Hadoop,HBase,Storm,Spark |A Plataforma de Dados Unifi é um conjunto perfeitamente integrado de ferramentas de dados de self-service projetadas para capacitar o utilizador do negócio para enfrentar desafios de dados que impulsionam receitas incrementais, reduzir custos ou complexidade operacional. |
|[Desvendadados APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Aplicativo De dados deunravel para cluster HDInsight Spark. |
|[Catálogo de dados baseado em IA da linha de água](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Catálogos de linha de água, organizae e regula dados usando IA para marcar dados com termos de negócio. Catálogo de alfabetizada do waterline corporativos é um componente de sucesso críticos, para análises de autoatendimento, conformidade e governação e iniciativas de gestão de TI. |

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e utilizar o Azure PowerShell e CLI clássica do Azure para implementar o modelo.  Consulte [a Create Apache Hadoop clusters no HDInsight utilizando modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md)de Gestor de Recursos .

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Instale uma aplicação HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Do menu esquerdo, navegue para **todos os serviços** > **Analytics** > **clusters HDInsight**.
3. Selecione um cluster HDInsight da lista.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Na categoria **Definições,** selecione **Aplicações**. Pode ver uma lista de aplicações instaladas na janela principal. 
   
    ![menu do portal das aplicações do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecione **+Adicionar** a partir do menu. Pode ver uma lista de aplicações disponíveis.  Se **+Adicionar** é acinzentado, isso significa que não existem aplicações para esta versão do cluster HDInsight.
   
    ![aplicações do HDInsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Selecione uma das aplicações disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Pode ver o estado de instalação a partir das notificações do portal (selecione o ícone da campainha na parte superior do portal). Depois da aplicação está instalada, a aplicação é apresentada na lista de aplicações instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster

Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. Para instalar aplicações durante a criação do cluster utilizando o portal Azure, a partir do separador **De Configuração + preços,** selecione **+ Adicionar aplicação**.

![Aplicações de configuração de cluster de portal Azure](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Lista de propriedades de aplicação E exibição HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Do menu esquerdo, navegue para **todos os serviços** > **Analytics** > **clusters HDInsight**.
3. Selecione um cluster HDInsight da lista.
4. Na categoria **Definições,** selecione **Aplicações**. Pode ver uma lista de aplicações instaladas na janela principal. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecione uma das aplicações instaladas para mostrar a propriedade. As listas de propriedades:

    |Propriedade | Descrição |
    |---|---|
    |Nome da aplicação |Nome da candidatura. |
    |Estado |Estado da candidatura. |
    |Página Web |O URL da aplicação web que implementou para o nó de borda. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. |
    |Ponto final do SSH |Pode utilizar o SSH para se ligar ao nó da borda. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Descrição | Descrição da candidatura. |

6. Para eliminar uma aplicação, clique na aplicação e clique em **Apagar** a partir do menu de contexto.

## <a name="connect-to-the-edge-node"></a>Ligar ao nó de extremidade
Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Resolução de problemas
Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passos seguintes
* [Instale aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Crie clusters Apache Hadoop baseados em Linux no HDInsight utilizando modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md)de Gestor de Recursos: aprenda a chamar modelos de Gestor de Recursos para criar clusters HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

