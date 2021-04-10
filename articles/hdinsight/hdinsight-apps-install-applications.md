---
title: Instale aplicações de terceiros no Azure HDInsight
description: Saiba como instalar aplicações Apache Hadoop de terceiros no Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 46a22b78ade3d137f25b14e5cbdaff3264208228
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868397"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instale aplicações Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar uma aplicação [Apache Hadoop](https://hadoop.apache.org/) de terceiros no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

A lista que se segue mostra as aplicações publicadas:

|Aplicação |Tipo de agrupamento ou tipo de agrupamento | Description |
|---|---|---|
|[Plataforma de Inteligência AtScale](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |O AtScale transforma o seu cluster HDInsight num servidor OLAP de escala, permitindo-lhe consultar milhares de milhões de linhas de dados interativamente utilizando as ferramentas BI que já conhece, possui e ama – do Microsoft Excel, Power BI, Tableau Software para qlikView. |
|[CDAP para HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |O CDAP é a primeira plataforma de integração unificada para big data que acelera o tempo para valorizar o Hadoop e permite que a TI forneça dados de autosserviço. Fonte aberta e extensível, o CDAP elimina barreiras à inovação. Requisitos: 4 Nós regionais, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |A plataforma escalável de self-service da Datameer para preparar, explorar e reger os seus dados para análise acelera a transformação de dados complexos de multisource em informações valiosas prontas para o negócio, fornecendo insights mais rápidos e inteligentes à escala da empresa. |
|[Dataiku DSS em HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop |Dataiku DSS numa plataforma de ciência de dados da empresa que permite que cientistas de dados e analistas de dados colaborem para projetar e executar novos produtos e serviços de dados de forma mais eficiente, transformando dados brutos em previsões impactantes. |
|[WANdisco Fusion HDI App](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark,HBase,Storm,Kafka |Manter os dados consistentes num ambiente distribuído é um enorme desafio de operações de dados. A WANdisco Fusion, uma plataforma de software de classe empresarial, resolve este problema permitindo uma consistência de dados não estruturada em qualquer ambiente. |
|[H2O SparklingWater para HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |A H2O Sparkling Water suporta os seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim para integração de dados Real-Time para HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop,HBase,Tempestade,Faísca, Kafka |Striim (pronuncia-se "stream") é uma plataforma de integração de dados de streaming + plataforma de inteligência, permitindo a ingestão contínua, o processamento e a análise de fluxos de dados diferentes. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop |A um nível elevado, Jumbune ajuda as empresas por 1. Acelerando tez, MapReduce & desempenho de trabalho baseado em motor Spark Hive, Java, Scala. 2. Monitorização proativa do cluster Hadoop, 3. Estabelecer gestão da Qualidade de Dados no sistema de ficheiros distribuídos. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Faísca |Powered by Apache Kylin, Kyligence Enterprise Enables BI on Big Data. Como motor OLAP da empresa em Hadoop, a Kyligence Enterprise capacita o analista de negócios para o arquiteto BI em Hadoop com o armazém de dados padrão da indústria e metodologia bi. |
|[Starburst Presto para Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto é um motor de consulta SQL distribuído rápido e escalável. Projetado para a separação do armazenamento e cálculo, Presto é perfeito para consulta de dados em Azure Data Lake Storage, Azure Blob Storage, SQL e NoSQL bases de dados, e outras fontes de dados. |
|[StreamSets Data Collector para HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector é um motor leve e poderoso que transmite dados em tempo real. Utilize o Data Collector para encaminhar e processar dados nos seus fluxos de dados. Vem com uma licença de 30 dias de julgamento. |
|[Empresa Trifacta Wrangler](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark,HBase |A Trifacta Wrangler Enterprise para a HDInsight suporta a luta de dados em toda a empresa para qualquer escala de dados. O custo de funcionamento da Trifacta em Azure é uma combinação de custos de subscrição da Trifacta mais os custos de infraestrutura Azure para as máquinas virtuais. |
|[Plataforma de Dados Unificado](https://www.crunchbase.com/organization/unifi-software) |Hadoop,HBase,Tempestade,Faísca |A Plataforma de Dados Unificado é um conjunto integrado perfeitamente de ferramentas de dados de autosserviço projetadas para capacitar o utilizador do negócio a enfrentar desafios de dados que impulsionam receitas incrementais, reduzem custos ou complexidade operacional. |
|[Desvendadata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Desvenda a aplicação De dados para o cluster HDInsight Spark. |
|[Catálogo de dados de AI-Driven de água](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Catálogos de linha de água, organiza e rege dados usando AI para marcar automaticamente dados com termos de negócio. O catálogo de literatos de negócios da Waterline é uma componente crítica e de sucesso para a análise de autosserviço, conformidade e governação, e iniciativas de gestão de TI. |

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo Azure Resource Manager a partir do portal ou obter uma cópia do modelo de Gestor de Recursos dos fornecedores, e usar o Azure PowerShell e o Azure Classic CLI para implementar o modelo.  Consulte [os clusters Apache Hadoop em HDInsight utilizando modelos de Gestor de Recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Instale uma aplicação HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir do menu esquerdo, navegue para **todos os serviços**  >  **Analytics**  >  **HDInsight clusters**.
3. Selecione um cluster HDInsight da lista.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Na categoria **Definições,** selecione **Aplicações**. Pode ver uma lista de aplicações instaladas na janela principal. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png" alt-text="menu do portal das aplicações do HDInsight":::
5. **Selecione +Adicione** no menu. Pode ver uma lista de aplicações disponíveis.  Se **+Add** estiver acinzentado, isso significa que não existem aplicações para esta versão do cluster HDInsight.
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png" alt-text="aplicações do HDInsight disponíveis":::
6. Selecione uma das candidaturas disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Pode ver o estado de instalação a partir das notificações do portal (selecione o ícone da campainha na parte superior do portal). Após a instalação da aplicação, a aplicação aparece na lista de Aplicações Instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster

Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. Para instalar aplicações durante a criação do cluster utilizando o portal Azure, a partir do **separador Configuração + preços,** selecione **+ Adicionar a aplicação**.

:::image type="content" source="./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png" alt-text="Aplicações de configuração do cluster do portal Azure":::

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Listar propriedades de aplicação e exibição HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir do menu esquerdo, navegue para **todos os serviços**  >  **Analytics**  >  **HDInsight clusters**.
3. Selecione um cluster HDInsight da lista.
4. Na categoria **Definições,** selecione **Aplicações**. Pode ver uma lista de aplicações instaladas na janela principal. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png" alt-text="Aplicações HDInsight instaladas apps":::
5. Selecione uma das aplicações instaladas para mostrar a propriedade. As listas de imóveis:

    |Propriedade | Descrição |
    |---|---|
    |Nome da aplicação |Nome da aplicação. |
    |Estado |Estado da candidatura. |
    |Página web |O URL da aplicação web que implementou para o nó de borda. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. |
    |Ponto final do SSH |Pode utilizar o SSH para ligar ao nó de borda. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Description | Descrição da aplicação. |

6. Para eliminar uma aplicação, clique com o botão direito na aplicação e, em seguida, clique em **Eliminar** a partir do menu de contexto.

## <a name="connect-to-the-edge-node"></a>Ligar ao nó de extremidade
Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Resolução de problemas
Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passos seguintes
* [Instale aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters Apache Hadoop baseados em Linux em HDInsight usando modelos de Gestor de Recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a chamar modelos de Gestor de Recursos para criar clusters HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.