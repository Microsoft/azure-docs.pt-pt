---
title: Instalar aplicações de terceiros no Azure HDInsight
description: Saiba como instalar aplicações do Hadoop de terceiros no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: 810f9bb81d367cfe70e59d62d81a9e129cf80e6b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225988"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicações do Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar uma aplicação de terceiros [Apache Hadoop](https://hadoop.apache.org/) aplicação no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação do HDInsight é um aplicativo que os utilizadores podem instalar num cluster do HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

A lista seguinte mostra as aplicações publicadas:

|Aplicação |O tipo ou tipos de cluster | Descrição |
|---|---|---|
|Plataforma de informações de AtScale |Hadoop |AtScale transforma o seu cluster do HDInsight para um servidor OLAP de escalamento horizontal, que lhe permite a consulta milhares de milhões de linhas de dados de forma interativa usando as ferramentas de BI que já conhece, o proprietário e adora – a partir do Microsoft Excel, Power BI, Tableau Software para QlikView. |
|CDAP 4.2, 4.3 para HDInsight |HBase |CDAP é a primeira plataforma de integração unificada para macrodados que aceleram o tempo para o valor para o Hadoop e permite que as TI fornecer dados de gestão personalizada. Código-fonte aberto e extensível, CDAP remove barreiras à inovação. Requisitos: 4 região nós, min D3 v2. |
|Datameer |Hadoop |Plataforma self-service dimensionável da Datameer para preparar, explorar e que regem os dados para análise aceleram transformar dados multisource complexos em informações valiosas de pronto a utilizar, fornecimento rápidas e inteligentes informações numa escala empresarial. |
|Dataiku DSS no HDInsight |Hadoop, Spark |Dataiku DSS numa plataforma de ciência de dados empresarial que permite que os cientistas de dados e analistas de dados colaborar para conceber e executar os novos produtos de dados e serviços de forma mais eficiente, transformar dados não processados em predições com impacto. |
|O WANdisco Fusion HDI aplicação - 2.12.3, 2.12.1, 2.11.2 |Hadoop, Spark,HBase,Storm,Kafka |Mantendo os dados consistentes num ambiente distribuído é um desafio de operações de grandes quantidades de dados. O WANdisco Fusion, uma plataforma de software de nível empresarial, resolve esse problema, permitindo que a consistência de dados não estruturados em qualquer ambiente. |
|H2O SparklingWater para HDInsight |Spark |O Sparkling Water da H2O oferece suporte aos seguintes algoritmos distribuídos: GLM, Bayes Ingênua, distribuídas Random floresta, máquina aumentam a gradação, redes Neurais profundas, aprendizagem profunda, K-means, o PCA, modelos de classificação de baixa generalizada, deteção de anomalias, Autoencoders. |
|Striim para integração de dados em tempo real para HDInsight |Hadoop,HBase,Storm,Spark,Kafka |Striim (pronunciado "fluxo") é um ponto-a-ponto transmissão em fluxo de integração de dados + plataforma de informações, permitindo contínua ingestão, processamento e análise de fluxos de dados diferentes. |
|Jumbune para HDInsight |Hadoop, Spark |Num alto nível, Jumbune auxilia as empresas em 1. Acelerando o Tez, o motor de MapReduce e o Spark com base Hive, Java, Scala desempenho de carga de trabalho. 2. Monitorização, 3 proativa Cluster do Hadoop. A estabelecer a gestão de qualidade de dados no sistema de ficheiros distribuído. |
|Kyligence Enterprise |Hadoop,HBase,Spark |O Kyligence Enterprise com tecnologia Apache Kylin, permite que o BI em grandes volumes de dados. Como um motor OLAP do enterprise no Hadoop, o Kyligence Enterprise capacita analista de negócios para a arquitetura de BI no Hadoop com o armazém de dados padrão do setor e à metodologia de BI. |
|Servidor de tarefas do Spark para o Executor do Spark KNIME |Spark |Servidor de tarefas do Spark para o Executor do Spark de KNIME é utilizado para ligar a plataforma de análise de KNIME a clusters do HDInsight. |
|Starburst Presto no Azure HDInsight, Starburst Presto (v0.213-e) |Hadoop |Presto é um rápidas e dimensionável distribuído motor de consulta SQL. Criado para a separação de armazenamento e computação, Presto é perfeito para consultar dados no armazenamento do Azure Data Lake, bases de dados do armazenamento de Blobs do Azure, SQL e NoSQL e outras origens de dados. |
|Recoletor de dados de StreamSets para a nuvem do HDInsight |Hadoop,HBase,Spark,Kafka |O StreamSets Data Collector é um motor de simples e poderoso que transmite os dados em tempo real. Utilize Recoletor de dados para a rota e processar dados em seus fluxos de dados. Ele vem com uma licença de avaliação de 30 dias. |
|[Trifacta Wrangler Enterprise](https://www.trifacta.com/) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise para o HDInsight oferece suporte a preparação para qualquer escala de dados de dados de toda a empresa. O custo de executar o Trifacta no Azure é uma combinação de custos das subscrições Trifacta mais os custos de infraestrutura do Azure para as máquinas virtuais. |
|Plataforma de dados de UNIFI 3.1 |Hadoop,HBase,Storm,Spark |A plataforma de dados de Unifi é um conjunto integrado de forma totalmente integrada de ferramentas de dados de gestão personalizada criadas para oferecer ao usuário de negócios para enfrentar os desafios de dados essa receita incremental de unidade, reduzir os custos ou complexidade operacional. |
|Unraveldata APM |Spark |Desvendar aplicação de dados para o cluster do HDInsight Spark. |
|Catálogo de dados de waterline |Spark |Waterline cataloga, organiza e rege dados através de ia para dados de auto-etiquetas com estes termos de negócios. Catálogo de alfabetizada do waterline corporativos é um componente de sucesso críticos, para análises de autoatendimento, conformidade e governação e iniciativas de gestão de TI. |

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e utilizar o Azure PowerShell e CLI clássica do Azure para implementar o modelo.  Ver [Apache Hadoop criar clusters no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Instalar uma aplicação do HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **análise** > **clusters do HDInsight**.
3. Selecione um cluster do HDInsight a partir da lista.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Sob o **configurações** categoria, selecione **aplicativos**. Pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![menu do portal das aplicações do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecione **+ adicionar** no menu. Pode ver uma lista de aplicações disponíveis.  Se **+ adicionar** está esbatida, que significa que há não é nenhuma aplicação para esta versão do cluster do HDInsight.
   
    ![aplicações do HDInsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Selecione uma das aplicações disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Pode ver o estado de instalação a partir de notificações do portal (selecione o ícone de sino na parte superior do portal). Depois da aplicação está instalada, a aplicação é apresentada na lista de aplicações instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster
Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. Para instalar aplicações durante a criação do cluster no portal do Azure, utilize o **personalizada** opção em vez da predefinição **criação rápida** opção.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Aplicações do HDInsight e apresentar as propriedades**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **análise** > **clusters do HDInsight**.
3. Selecione um cluster do HDInsight a partir da lista.
4. Sob o **configurações** categoria, selecione **aplicativos**. Pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecione uma das aplicações instaladas para mostrar a propriedade. As listas de propriedades:

    |Propriedade | Descrição |
    |---|---|
    |Nome da aplicação |Nome da aplicação. |
    |Estado |Estado da aplicação. |
    |Página Web |O URL da aplicação web que implementou no nó de extremidade. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. |
    |Ponto final de SSH |Pode utilizar o SSH para ligar ao nó de extremidade. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Descrição | Descrição da aplicação. |

6. Para eliminar uma aplicação, clique com o botão direito do aplicativo e, em seguida, clique em **eliminar** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Ligar ao nó de extremidade
Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Resolução de problemas
Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passos Seguintes
* [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicações HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar aplicações HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Resource Manager para criar clusters do HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

