---
title: "Instalar aplicações do Hadoop de terceiros no Azure HDInsight | Microsoft Docs"
description: "Saiba como instalar aplicações do Hadoop de terceiros no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/16/2017
ms.author: jgao
ms.openlocfilehash: b3b9d55f2d1e11156c21bc4ec5652e6d7c421db2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicações do Hadoop de terceiros no Azure HDInsight

Neste artigo, irá aprender a instalar uma aplicação do Hadoop de terceiros já publicada no Azure HDInsight. Para obter instruções sobre como instalar a sua própria aplicação, veja [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

Atualmente, existem quatro aplicações publicadas:

* **DATAIKU DDS no HDInsight**: o Dataiku DSS (estúdio de ciência de dados) é um software que permite aos profissionais de dados (cientistas de dados, analistas de negócios, programadores...) criarem protótipos, criarem e implementarem serviços altamente específicos que transformam dados não processados em predições de negócio que tenham impacto.
* **Datameer**: o [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) oferece aos analistas uma forma interativa de detetar, analisar e visualizar os resultados de Macrodados. Extraia origens de dados adicionais facilmente para detetar novas relações e obter depressa as respostas de que precisa.
* O **Recoletor de dados de Streamsets para o HDnsight** fornece um ambiente de desenvolvimento integrado com todas as funcionalidades (IDE) que lhe permite estruturar, testar, implementa e gerir qualquer para qualquer pipelines de ingestão que misturam fluxo e lote de dados e incluem uma variedade de transformações no fluxo — tudo isto sem ter de escrever código personalizado. 
* **Cask CDAP para o HDInsight** fornece a plataforma de integração unificada primeiro para macrodados cuts o tempo para a produção para aplicações de dados e lagos de dados por 80%. Esta aplicação apenas suporta clusters Standard HBase 3.4.
* **H2O Artificial Intelligence para o HDInsight (Beta)** H2O Sparkling máximo suporta os algoritmos seguintes distribuídos: GLM, Naïve Bayes, distribuídas aleatório floresta, gradação máquina os aumentos, as redes Neurais profundas, profundidade de aprendizagem, K significa, PCA, modelos de classificação de baixo generalizado, deteção de anomalias e Autoencoders.
* **Plataforma de análise Kyligence** Kyligence Analytics Platform (KAP) é um armazém de dados preparada para empresa com tecnologia do Apache Kylin e do Apache Hadoop; garante a latência de consulta do segundo secundárias no conjunto de dados de grande escala e simplifica a análise de dados para os utilizadores empresariais e analistas. 
* **SnapLogic Hadooplex** o SnapLogic Hadooplex, em execução no HDInsight permite aos clientes aceder às informações empresariais mais rápidas, fornecendo a ingestão de dados de self-service e preparação a partir de praticamente qualquer origem para a plataforma de nuvem do Microsoft Azure.
* **Servidor de tarefas do Spark para KNIME Spark Executor** servidor de tarefas do Spark para KNIME Spark Executor é utilizada para ligar a plataforma de análise KNIME a clusters do HDInsight.

As instruções fornecidas neste artigo utilizam o portal do Azure. Também pode exportar o modelo do Azure Resource Manager a partir do portal ou obter uma cópia do modelo do Resouce Manager junto de fornecedores e utilizar o Azure PowerShell e a CLI do Azure para implementar o modelo.  Veja [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicações em clusters existentes
O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight num cluster HDInsight existente.

**Para instalar uma aplicação do HDInsight**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Mais Serviços** e, em seguida, em **Clusters do HDInsight**.
3. Clique num cluster HDInsight.  Se não tiver um, tem de criá-lo.  Veja [Create clusters (Criar clusters)](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Clique em **Aplicações** na categoria **Configurações**. Pode ver uma lista das aplicações instaladas. Se não consegue encontrar Aplicações, significa que não existe nenhuma aplicação para esta versão do cluster do HDInsight.
   
    ![menu do portal das aplicações do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Clique em **Adicionar** no menu do painel. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Deverá ver uma lista de aplicações do HDInsight existentes.
   
    ![aplicações do HDInsight disponíveis](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Clique numa das aplicações, aceite os termos legais e clique em **Selecionar**.

Pode verificar o estado da instalação nas notificações do portal (clique no ícone de sino na parte superior do portal). Depois de instalada, a aplicação aparece no painel Aplicações Instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicações durante a criação do cluster
Tem a opção de instalar aplicações do HDInsight quando cria um cluster. Durante o processo, as aplicações do HDInsight são instaladas depois de o cluster ser criado e estar no estado Em Execução. O procedimento seguinte mostra-lhe como instalar aplicações do HDInsight quando cria um cluster.

**Para instalar uma aplicação do HDInsight**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **NOVA**, clique em **Dados + Análises** e clique em **HDInsight**.
3. Introduza o **Nome do Cluster**: este nome tem de ser globalmente exclusivo.
4. Clique em **subscrição** para selecionar a subscrição do Azure que é utilizada para o cluster.
5. Clique em **Selecionar tipo de cluster** e, em seguida, selecione:
   
   * **Tipo de Cluster**: se não sabe o que escolher, selecione **Hadoop**. É o tipo de cluster mais popular.
   * **Sistema Operativo**: selecione **Linux**.
   * **Versão**: utilize a versão predefinida se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).
   * **Escalão do Cluster**: o Azure HDInsight disponibiliza as ofertas de nuvem de macrodados em duas categorias -- escalão Standard e escalão Premium. Para obter mais informações, veja [Escalões de Clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Clique em **Aplicações**, clique numa das aplicações publicadas e, em seguida, clique em **Selecionar**.
7. Clique em **Credenciais** e, em seguida, introduza uma palavra-passe para o utilizador administrador. Também tem de introduzir um **nome de utilizador SSH** e um **palavra-passe** ou **chave pública**, que é utilizado para autenticar o utilizador SSH. A abordagem recomendada é utilizar uma chave pública. Clique em **Selecionar**, na parte inferior, para guardar a configuração das credenciais.
8. Clique em **origem de dados**, selecione uma das contas de armazenamento existente ou crie uma nova conta de armazenamento para ser utilizado como a conta de armazenamento predefinido para o cluster.
9. Clique em **Grupo de Recursos** para ver um grupo de recursos existente ou clique em **Novo** para criar um novo.
10. No painel **Novo Cluster HDInsight**, confirme que **Afixar ao Startboard** está selecionado e, em seguida, clique em **Criar**. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Apresentar as aplicações do HDInsight instaladas e propriedades
O portal mostra uma lista das aplicações do HDInsight instaladas relativas a um cluster e as propriedades de cada uma dessas aplicações.

**Para apresentar as aplicações do HDInsight e as respetivas propriedades**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique num cluster HDInsight.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. O painel Aplicações Instaladas apresenta uma lista de todas as aplicações instaladas. 
   
    ![aplicações instaladas do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Clique numa das aplicações instaladas para mostrar a propriedade. O painel de propriedades mostra:
   
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

## <a name="next-steps"></a>Passos seguintes
* [Instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada no HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates (Criar clusters Hadoop baseados em Linux no HDInsight com modelos do Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.

