---
title: Instalar aplicações do Hadoop de terceiros no Azure HDInsight | Microsoft Docs
description: Saiba como instalar aplicações do Hadoop de terceiros no Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: bd74be9c78744619f60606b509ea16d000bd3998
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicações do Hadoop de terceiros no Azure HDInsight

Saiba como instalar uma aplicação do Hadoop de terceiros no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

A lista seguinte mostra as aplicações publicadas:

* **Plataforma de Intelligence AtScale** ativa do cluster do HDInsight para um servidor OLAP de escalamento horizontal. A aplicação permite-lhe consulta billions de linhas de dados interativamente utilizando as ferramentas de BI do Microsoft Excel, PowerBI, Tableau Software para QlikView.
* **Cask CDAP para o HDInsight** fornece a plataforma de integração unificada primeiro para macrodados cuts o tempo para a produção para aplicações de dados e lagos de dados por 80%. Esta aplicação apenas suporta clusters Standard HBase 3.4.
* **DATAIKU DDS no HDInsight** permite que os profissionais de dados para criar protótipos, criar e implementar os serviços de elevada disponibilidade específicos transformar dados não processados para predições do negócio impactful.
* **H2O Artificial Intelligence para o HDInsight (Beta)** H2O Sparkling máximo suporta os algoritmos seguintes distribuídos: GLM Naïve Bayes, distribuídas aleatório floresta, gradação máquina os aumentos, as redes Neurais profundas, avançada learning K-significa, PCA, Modelos de classificação baixos generalizados, deteção de anomalias e Autoencoders.
* **Plataforma de análise Kyligence** Kyligence Analytics Platform (KAP) é um armazém de dados preparada para empresa com tecnologia do Apache Kylin e do Apache Hadoop; proporciona segundo secundárias latência no conjunto de dados de grande escala de consulta e simplifica a análise de dados do os utilizadores empresariais e analistas. 
* **Preparação de dados de personalização de Paxata**
* **Servidor de tarefas do Spark para KNIME Spark Executor** servidor de tarefas do Spark para KNIME Spark Executor é utilizada para ligar a plataforma de análise KNIME a clusters do HDInsight.
* O **Recoletor de dados de Streamsets para o HDnsight** fornece um ambiente de desenvolvimento integrado com todas as funcionalidades (IDE) que lhe permite estruturar, testar, implementa e gerir qualquer para qualquer pipelines de ingestão que misturam fluxo e lote de dados e incluem uma variedade de transformações no fluxo — tudo isto sem ter de escrever código personalizado. 
* **[Trifacta](http://www.trifacta.com/)**  permite engenheiros de dados e os analistas explorar e preparar os dados de diversos da atualidade através da utilização do machine learning fornecer uma experiência de utilizador breakthrough, o fluxo de trabalho e a arquitetura de forma mais eficiente.
* **WANdisco Fusion HDI aplicação** permite a conectividade contínua consistente aos dados à medida que muda independentemente do local onde está localizado. Apresenta com acesso aos seus dados em qualquer altura e em qualquer lugar com sem períodos de indisponibilidade e sem interrupção.

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo do Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resouce Manager junto de fornecedores e utilizar o Azure PowerShell e a CLI do Azure para implementar o modelo.  Consulte [clusters do Hadoop criar no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Instalar aplicações HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.
3. Clique num cluster HDInsight.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Clique em **Aplicações** na categoria **Configurações**. Pode ver uma lista das aplicações instaladas. Se não consegue encontrar Aplicações, significa que não existe nenhuma aplicação para esta versão do cluster do HDInsight.
   
    ![menu do portal das aplicações do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Clique em **adicionar** no menu. Deverá ver uma lista de aplicações do HDInsight existentes.
   
    ![aplicações do HDInsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Clique das aplicações disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Pode verificar o estado da instalação nas notificações do portal (clique no ícone de sino na parte superior do portal). Depois da aplicação é instalada, a aplicação é apresentada na lista de aplicações instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster
Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. Para instalar aplicações durante a criação do cluster utilizando o portal do Azure, utilize a opção – personalizada – em vez da predefinição – rápido criar – opção.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Aplicações do HDInsight e apresentar as propriedades**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda. 
3. Clique num cluster HDInsight.
4. De **definições**, clique em **aplicações** sob o **configuração** categoria. As aplicações instaladas são apresentadas no lado direito. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Clique numa das aplicações instaladas para mostrar a propriedade. As listas de propriedades:
   
   * Nome da aplicação: o nome da aplicação.
   * Estado: o estado da aplicação. 
   * Página Web: O URL da aplicação web que implementou ao nó de extremidade. A credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster.
   * Ponto final de HTTP: a credencial é a mesma que as credenciais de utilizador HTTP que configurou para o cluster. 
   * Ponto final de SSH: pode utilizar o SSH para ligar ao nó de extremidade. As credenciais de SSH são as mesmas que as credenciais de utilizador de SSH que configurou para o cluster. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Para eliminar uma aplicação, clique com o botão direito a aplicação e, em seguida, clique em **eliminar** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Ligar ao nó de extremidade
Pode ligar ao nó de extremidade com HTTP e SSH. Pode encontrar as informações relativas ao ponto final no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais do ponto final de HTTP são as credenciais de utilizador HTTP que configurou para o cluster HDInsight; as credenciais do ponto final de SSH são as credenciais de SSH que configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Resolução de problemas
Veja [Troubleshoot the installation (Resolver problemas de instalação)](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passos Seguintes
* [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada no HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

