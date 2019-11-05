---
title: Configuração de cluster para Apache e R Server no Azure HDInsight
description: Configure clusters Hadoop, Kafka, Spark, HBase, R Server ou Storm para o HDInsight em um navegador, a CLI clássica do Azure, Azure PowerShell, REST ou SDK.
keywords: configuração de cluster Hadoop, configuração de cluster Kafka, configuração de cluster Spark, o que é cluster no Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 0b977cb9fe2df1627ad9a2e07b00ffb0e749ed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498257"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como configurar e configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, consulta interativa, Apache HBase, serviços de ML ou Apache Storm. Além disso, saiba como personalizar clusters e adicionar segurança unindo-os a um domínio.

Um cluster Hadoop consiste em várias máquinas virtuais (nós) que são usadas para o processamento distribuído de tarefas. O Azure HDInsight lida com os detalhes de implementação de instalação e configuração de nós individuais, de modo que você só precisa fornecer informações gerais de configuração.

> [!IMPORTANT]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Saiba como [excluir um cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Métodos de configuração de cluster

A tabela a seguir mostra os diferentes métodos que você pode usar para configurar um cluster HDInsight.

| Clusters criados com | Navegador da Web | Linha de comandos | API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Modelos de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="basic-cluster-setup"></a>Configuração básica do cluster

Este artigo explica a instalação no [portal do Azure](https://portal.azure.com), onde você pode criar um cluster HDInsight usando a exibição padrão ou o *clássico*.

![criação rápida personalizada de opções de criação do hdinsight](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

Siga as instruções na tela. Os detalhes são fornecidos abaixo para:

* [Nome do grupo de recursos](#resource-group-name)
* [Tipos de cluster e configuração](#cluster-types)
* [Nome do cluster](#cluster-name)
* [Logon do cluster e nome de usuário SSH](#cluster-login-and-ssh-username)
* [Localização](#location)

## <a name="resource-group-name"></a>Nome do grupo de recursos

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ajuda a trabalhar com os recursos em seu aplicativo como um grupo, conhecido como um grupo de recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.

## <a name="cluster-types"></a>Tipos de cluster e configuração

Atualmente, o Azure HDInsight fornece os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer certas funcionalidades.

> [!IMPORTANT]  
> Os clusters HDInsight estão disponíveis em vários tipos, cada um para uma única carga de trabalho ou tecnologia. Não há nenhum método com suporte para criar um cluster que combine vários tipos, como o Storm e o HBase em um cluster. Se sua solução exigir tecnologias que sejam distribuídas por vários tipos de cluster HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) poderá conectar os tipos de cluster necessários.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Consulta em lote e análise de dados armazenados |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados NoSQL sem esquema |
| [Consulta interativa](./interactive-query/apache-interactive-query-get-started.md) |Cache na memória para consultas de Hive interativas e mais rápidas |
| [Kafka](kafka/apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para criar pipelines e aplicativos de dados de streaming em tempo real |
| [Serviços de ML](r-server/r-server-overview.md) |Várias estatísticas de Big Data, modelagem preditiva e recursos de aprendizado de máquina |
| [Spark](spark/apache-spark-overview.md) |Processamento na memória, consultas interativas, processamento de fluxo de micro batch |
| [Tempestade](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |

### <a name="hdinsight-version"></a>Versão do HDInsight

Escolha a versão do HDInsight para este cluster. Para obter mais informações, consulte [versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Nome do cluster

Os nomes de cluster HDInsight têm as seguintes restrições:

* Caracteres permitidos: a-z, 0-9, A-Z
* Comprimento máximo: 59
* Nomes reservados: aplicativos
* O escopo de nomenclatura de cluster é para todos os Azure, em todas as assinaturas. Portanto, o nome do cluster deve ser exclusivo em todo o mundo.
* Os seis primeiros caracteres devem ser exclusivos em uma VNET

## <a name="cluster-login-and-ssh-username"></a>Logon do cluster e nome de usuário SSH

Com os clusters HDInsight, você pode configurar duas contas de usuário durante a criação do cluster:

* Usuário HTTP: o nome de usuário padrão é *admin*. Ele usa a configuração básica no portal do Azure. Às vezes, é chamado de "usuário do cluster".
* Usuário SSH: usado para se conectar ao cluster por meio de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O nome de usuário HTTP tem as seguintes restrições:

* Caracteres especiais permitidos: _ e @
* Caracteres não permitidos: #;. "',\/: '! *? $ (){}[] < > | &--= +% ~ ^ espaço
* Comprimento máximo: 20

O nome de usuário do SSH tem as seguintes restrições:

* Caracteres especiais permitidos: _ e @
* Caracteres não permitidos: #;. "',\/: '! *? $ (){}[] < > | &--= +% ~ ^ espaço
* Comprimento máximo: 64
* Nomes reservados: Hadoop, usuários, oozie, Hive, mapred, ambari-QA, Zookeeper, tez, HDFS, sqoop, yarn, hcat, AMS, HBase, Storm, administrador, administrador, usuário, Usuário1, teste, Usuário2, Test1, usuário3, admin1, 1, 123, a, actuser, ADM, admin2, ASPNET, backup, console, David, convidado, John, proprietário, raiz, servidor, SQL, suporte, Support_388945a0, sys, test2, test3, User4, User5, Spark

O pacote de segurança empresarial permite que você integre o HDInsight com o Active Directory e o Apache Ranger. Vários usuários podem ser criados usando o pacote de segurança empresarial.

## <a name="location"></a>Local (regiões) para clusters e armazenamento

Você não precisa especificar o local do cluster explicitamente: o cluster está no mesmo local que o armazenamento padrão. Para obter uma lista de regiões com suporte, clique na lista suspensa **região** no [preço do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Pontos de extremidade de armazenamento para clusters

Embora uma instalação local do Hadoop use o Sistema de Arquivos Distribuído do Hadoop (HDFS) para armazenamento no cluster, na nuvem, você usa pontos de extremidade de armazenamento conectados ao cluster. O uso do armazenamento em nuvem significa que você pode excluir com segurança os clusters HDInsight usados para computação e, ao mesmo tempo, manter seus dados.

Os clusters HDInsight podem usar as seguintes opções de armazenamento:

* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1
* Armazenamento do Azure Uso Geral v2
* Armazenamento do Azure Uso Geral v1
* Blob de blocos de armazenamento do Azure (**com suporte apenas como armazenamento secundário**)

Para obter mais informações sobre opções de armazenamento com o HDInsight, consulte [comparar as opções de armazenamento para uso com clusters do Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

Durante a configuração, para o ponto de extremidade de armazenamento padrão, você especifica um contêiner de blob de uma conta de armazenamento do Azure ou Data Lake Storage. O armazenamento padrão contém logs do aplicativo e do sistema. Opcionalmente, você pode especificar contas de armazenamento do Azure vinculadas adicionais e Data Lake Storage contas que o cluster pode acessar. O cluster HDInsight e as contas de armazenamento dependentes devem estar no mesmo local do Azure.

![Configurações de armazenamento de cluster: pontos de extremidade de armazenamento compatíveis com HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage-blank.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Metastores opcionais

Você pode criar metastores opcionais do hive ou do Apache Oozie. No entanto, nem todos os tipos de cluster dão suporte a metastores e o Azure SQL Data Warehouse não é compatível com metastores.

Para obter mais informações, consulte [usar repositórios de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Quando você cria um metastore personalizado, não use traços, hifens ou espaços no nome do banco de dados. Isso pode fazer com que o processo de criação de cluster falhe.

### <a name="use-hiveoozie-metastore"></a>metastore do Hive

Se você quiser manter as tabelas do hive depois de excluir um cluster HDInsight, use um metastore personalizado. Em seguida, você pode anexar o metastore a outro cluster HDInsight.

HDInsight An metastore criado para uma versão de cluster HDInsight não pode ser compartilhado entre diferentes versões de cluster HDInsight. Para obter uma lista de versões do HDInsight, consulte [versões do Hdinsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metastore do Oozie

Para aumentar o desempenho ao usar o Oozie, use um metastore personalizado. Um metastore também pode fornecer acesso a dados de trabalho do Oozie depois de excluir o cluster.

> [!IMPORTANT]  
> Não é possível reutilizar um metastore personalizado do Oozie. Para usar um metastore personalizado do Oozie, você deve fornecer um banco de dados SQL do Azure vazio ao criar o cluster HDInsight.

## <a name="enterprise-security-package"></a>Pacote de segurança empresarial

Para o Hadoop, Spark, HBase, Kafka e tipos de cluster de consulta interativa, você pode optar por habilitar o **Enterprise Security Package**. Este pacote fornece a opção de ter uma configuração de cluster mais segura usando o Apache Ranger e a integração com o Azure Active Directory. Para obter mais informações, consulte [visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

![opções de criação do hdinsight escolher pacote de segurança empresarial](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking-esp.png)

Para obter mais informações sobre como criar um cluster HDInsight ingressado no domínio, consulte [criar ambiente de área restrita do hdinsight ingressado no domínio](./domain-joined/apache-domain-joined-configure.md).

## <a name="extend-clusters-with-a-virtual-network"></a>Estender clusters com uma rede virtual

Se sua solução exigir tecnologias que sejam distribuídas por vários tipos de cluster HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) poderá conectar os tipos de cluster necessários. Essa configuração permite que os clusters e qualquer código implantado neles se comuniquem diretamente entre si.

Para obter mais informações sobre como usar uma rede virtual do Azure com o HDInsight, consulte [planejar uma rede virtual para o hdinsight](hdinsight-plan-virtual-network-deployment.md).

Para obter um exemplo de como usar dois tipos de cluster em uma rede virtual do Azure, consulte [usar Apache Spark streaming estruturado com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Para obter mais informações sobre como usar o HDInsight com uma rede virtual, incluindo requisitos de configuração específicos para a rede virtual, consulte [planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="configure-cluster-size"></a>Configurar o tamanho do cluster

Você será cobrado pelo uso do nó desde que o cluster exista. A cobrança começa quando um cluster é criado e para quando o cluster é excluído. Os clusters não podem ser desalocados ou colocados em espera.

### <a name="number-of-nodes-for-each-cluster-type"></a>Número de nós para cada tipo de cluster

Cada tipo de cluster tem seu próprio número de nós, terminologia para nós e tamanho de VM padrão. Na tabela a seguir, o número de nós para cada tipo de nó está entre parênteses.

| Tipo | Nós | Diagrama |
| --- | --- | --- |
| Hadoop |Nó de cabeçalho (2), nó de trabalho (1 +) |![Nós de cluster Hadoop do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Servidor principal (2), servidor de região (1 +), nó mestre/ZooKeeper (3) |![Configuração de tipo de cluster HBase do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nó Nimbus (2), servidor do supervisor (1 +), nó ZooKeeper (3) |![Configuração de tipo de cluster do HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nó de cabeçalho (2), nó de trabalho (1 +), nó ZooKeeper (3) (gratuito para o tamanho de VM a1 ZooKeeper) |![Configuração de tipo de cluster HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Para obter mais informações, consulte [configuração de nó padrão e tamanhos de máquina virtual para clusters](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) em "quais são os componentes e versões do Hadoop no HDInsight?"

O custo dos clusters HDInsight é determinado pelo número de nós e pelos tamanhos das máquinas virtuais para os nós.

Tipos de cluster diferentes têm diferentes tipos de nó, números de nós e tamanhos de nó:
* Tipo de cluster Hadoop padrão:
    * Dois *nós de cabeçalho*  
    * Quatro *nós de trabalho*
* Padrão do tipo de cluster Storm:
    * Dois *nós Nimbus*
    * Três *nós ZooKeeper*
    * Quatro *nós de supervisor*

Se você estiver apenas experimentando o HDInsight, recomendamos usar um nó de trabalho. Para obter mais informações sobre os preços do HDInsight, consulte [preços do hdinsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> O limite de tamanho do cluster varia entre as assinaturas do Azure. Contate o [suporte de cobrança do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para aumentar o limite.

Quando você usa o portal do Azure para configurar o cluster, o tamanho do nó está disponível por meio da guia **configuração + preço** . No portal, você também pode ver o custo associado aos diferentes tamanhos de nó.

![HDInsight escolha o tamanho do seu nó](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration-pricing-hadoop.png)

### <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Ao implantar clusters, escolha recursos de computação com base na solução que você planeja implantar. As seguintes VMs são usadas para clusters HDInsight:

* VMs das séries a e D1-4: [tamanhos de VM Linux de uso geral](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* VM do D11-14 Series: [tamanhos de VM Linux com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Para descobrir qual valor você deve usar para especificar um tamanho de VM ao criar um cluster usando os diferentes SDKs ou ao usar Azure PowerShell, consulte [tamanhos de VM a serem usados para clusters HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Neste artigo vinculado, use o valor na coluna **tamanho** das tabelas.

> [!IMPORTANT]  
> Se você precisar de mais de 32 nós de trabalho em um cluster, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

Para obter mais informações, consulte [tamanhos das máquinas virtuais](../virtual-machines/windows/sizes.md). Para obter informações sobre os preços de vários tamanhos, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="classic-cluster-setup"></a>Configuração do cluster clássico

A configuração de cluster clássico se baseia nas configurações de criação padrão e adiciona as seguintes opções:

* [Aplicativos do HDInsight](#install-hdinsight-applications-on-clusters)
* [Ações de script](#advanced-settings-script-actions)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalar aplicações do HDInsight em clusters

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Você pode usar aplicativos fornecidos pela Microsoft, por terceiros ou por você mesmo desenvolver. Para obter mais informações, consulte [instalar aplicativos de Apache Hadoop de terceiros no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria dos aplicativos do HDInsight está instalada em um nó de borda vazio.  Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como no nó de cabeçalho. Você pode usar o nó de borda para acessar o cluster, testar seus aplicativos cliente e hospedar seus aplicativos cliente. Para obter mais informações, consulte [usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Configurações avançadas: ações de script

Você pode instalar componentes adicionais ou personalizar a configuração do cluster usando scripts durante a criação. Esses scripts são chamados por meio de **ação de script**, que é uma opção de configuração que pode ser usada no portal do Azure, nos cmdlets do Windows PowerShell do hdinsight ou no SDK do .net do hdinsight. Para obter mais informações, consulte [Personalizar o cluster HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes Java nativos, como Apache Mahout e em cascata, podem ser executados no cluster como arquivos de arquivo Java (JAR). Esses arquivos JAR podem ser distribuídos para o armazenamento do Azure e enviados para clusters HDInsight com mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [enviar trabalhos de Apache Hadoop programaticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Se você tiver problemas ao implantar arquivos JAR em clusters HDInsight ou chamar arquivos JAR em clusters HDInsight, entre em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).
>
> A cascata não é suportada pelo HDInsight e não é elegível para Suporte da Microsoft. Para obter listas de componentes com suporte, consulte [novidades nas versões de cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).

Às vezes, você deseja configurar os seguintes arquivos de configuração durante o processo de criação:

* clusterIdentity. xml
* Core-site. xml
* Gateway. xml
* HBase-env. xml
* HBase-site. xml
* HDFS-site. xml
* Hive-env. xml
* Hive-site. xml
* mapred-site
* oozie-site. xml
* oozie-env. xml
* Storm-site. xml
* tez-site. xml
* webhcat-site. xml
* yarn-site. xml

Para obter mais informações, consulte [Personalizar clusters HDInsight usando a inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Passos seguintes

* [Solucionar problemas de falhas de criação de cluster com o Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [O que são o HDInsight, o ecossistema de Apache Hadoop e os clusters Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Introdução ao uso de Apache Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Trabalhar em Apache Hadoop no HDInsight de um computador Windows](hdinsight-hadoop-windows-tools.md)
