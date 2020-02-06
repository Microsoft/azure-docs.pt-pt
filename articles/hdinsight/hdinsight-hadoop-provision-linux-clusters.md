---
title: Configuração de cluster para Apache e R Server no Azure HDInsight
description: Configure clusters Hadoop, Kafka, Spark, HBase, R Server ou Storm para o HDInsight em um navegador, a CLI clássica do Azure, Azure PowerShell, REST ou SDK.
keywords: configuração de cluster Hadoop, configuração de cluster Kafka, configuração de cluster Spark, o que é cluster no Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/03/2020
ms.openlocfilehash: 2c9c5b35110be8f9e51d2205f9fe63dfa4ef8e10
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031064"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como configurar e configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, consulta interativa, Apache HBase, serviços de ML ou Apache Storm. Além disso, saiba como personalizar clusters e adicionar segurança unindo-os a um domínio.

Um cluster Hadoop consiste em várias máquinas virtuais (nós) que são usadas para o processamento distribuído de tarefas. O Azure HDInsight lida com os detalhes de implementação de instalação e configuração de nós individuais, de modo que você só precisa fornecer informações gerais de configuração.

> [!IMPORTANT]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Aprenda a [apagar um cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Métodos de configuração de cluster

A tabela a seguir mostra os diferentes métodos que você pode usar para configurar um cluster HDInsight.

| Clusters criados com | Browser | Linha de comandos | API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modelos de Gestor de Recursos Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="basic-cluster-setup"></a>Configuração básica do cluster

Este artigo acompanha-o através da configuração no [portal Azure,](https://portal.azure.com)onde pode criar um cluster HDInsight utilizando a vista padrão ou *Classic*.

![criação rápida personalizada de opções de criação do hdinsight](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

Siga as instruções na tela. Os detalhes são fornecidos abaixo para:

* [Nome do grupo de recursos](#resource-group-name)
* [Tipos de cluster e configuração](#cluster-types)
* [Nome do cluster](#cluster-name)
* [Login de cluster e nome de utilizador SSH](#cluster-login-and-ssh-username)
* [Localização](#location)

## <a name="resource-group-name"></a>Nome do grupo de recursos

[O Azure Resource Manager](../azure-resource-manager/management/overview.md) ajuda-o a trabalhar com os recursos da sua aplicação como um grupo, referido como um grupo de recursos Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.

## <a name="cluster-types"></a>Tipos de cluster e configuração

Atualmente, o Azure HDInsight fornece os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer certas funcionalidades.

> [!IMPORTANT]  
> Os clusters HDInsight estão disponíveis em vários tipos, cada um para uma única carga de trabalho ou tecnologia. Não há nenhum método com suporte para criar um cluster que combine vários tipos, como o Storm e o HBase em um cluster. Se a sua solução necessitar de tecnologias que estão espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Consulta em lote e análise de dados armazenados |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados NoSQL sem esquema |
| [Consulta Interativa](./interactive-query/apache-interactive-query-get-started.md) |Cache na memória para consultas de Hive interativas e mais rápidas |
| [Kafka](kafka/apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para criar pipelines e aplicativos de dados de streaming em tempo real |
| [Serviços ML](r-server/r-server-overview.md) |Várias estatísticas de Big Data, modelagem preditiva e recursos de aprendizado de máquina |
| [Spark](spark/apache-spark-overview.md) |Processamento na memória, consultas interativas, processamento de fluxo de micro batch |
| [Tempestade](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |

### <a name="hdinsight-version"></a>Versão do HDInsight

Escolha a versão do HDInsight para este cluster. Para mais informações, consulte [as versões HDInsight suportadas](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Nome do cluster

Os nomes de cluster HDInsight têm as seguintes restrições:

* Caracteres permitidos: a-z, 0-9, A-Z
* Comprimento máximo: 59
* Nomes reservados: aplicativos
* O escopo de nomenclatura de cluster é para todos os Azure, em todas as assinaturas. Portanto, o nome do cluster deve ser exclusivo em todo o mundo.
* Os seis primeiros caracteres devem ser exclusivos em uma VNET

## <a name="cluster-login-and-ssh-username"></a>Logon do cluster e nome de usuário SSH

Com os clusters HDInsight, você pode configurar duas contas de usuário durante a criação do cluster:

* Utilizador HTTP: O nome de utilizador predefinido é *administrador*. Utiliza a configuração básica no portal Azure. Às vezes, é chamado de "usuário do cluster".
* Usuário SSH: usado para se conectar ao cluster por meio de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O nome de usuário HTTP tem as seguintes restrições:

* Caracteres especiais permitidos: _ e @
* Personagens não permitidos: #;."",\/:'!*?$(){}[]<>&-=+~^espaço
* Comprimento máximo: 20

O nome de usuário do SSH tem as seguintes restrições:

* Caracteres especiais permitidos: _ e @
* Personagens não permitidos: #;."",\/:'!*?$(){}[]<>&-=+~^espaço
* Comprimento máximo: 64
* Nomes reservados: hadoop, utilizadores, oozie, colmeia, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarns, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 1, 123, a, actuser, adm, in2, aspnet, convidado, john, proprietário, raiz, servidor, sql, suporte, support_388945a0, sys, test2, test3, user4, user5, spark

O pacote de segurança empresarial permite que você integre o HDInsight com o Active Directory e o Apache Ranger. Vários usuários podem ser criados usando o pacote de segurança empresarial.

## <a name="location"></a>Localização (regiões) para aglomerados e armazenamento

Você não precisa especificar o local do cluster explicitamente: o cluster está no mesmo local que o armazenamento padrão. Para obter uma lista de regiões apoiadas, clique na lista de abandono da **Região** sobre [os preços HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Pontos de extremidade de armazenamento para clusters

Embora uma instalação local do Hadoop use o Sistema de Arquivos Distribuído do Hadoop (HDFS) para armazenamento no cluster, na nuvem, você usa pontos de extremidade de armazenamento conectados ao cluster. O uso do armazenamento em nuvem significa que você pode excluir com segurança os clusters HDInsight usados para computação e, ao mesmo tempo, manter seus dados.

Os clusters HDInsight podem usar as seguintes opções de armazenamento:

* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1
* Armazenamento do Azure Uso Geral v2
* Armazenamento do Azure Uso Geral v1
* Blob bloco de armazenamento azure **(apenas suportado como armazenamento secundário)**

Para obter mais informações sobre as opções de armazenamento com o HDInsight, consulte Compare opções de [armazenamento para utilização com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

Durante a configuração, para o ponto de extremidade de armazenamento padrão, você especifica um contêiner de blob de uma conta de armazenamento do Azure ou Data Lake Storage. O armazenamento padrão contém logs do aplicativo e do sistema. Opcionalmente, você pode especificar contas de armazenamento do Azure vinculadas adicionais e Data Lake Storage contas que o cluster pode acessar. O cluster HDInsight e as contas de armazenamento dependentes devem estar no mesmo local do Azure.

![Configurações de armazenamento de cluster: pontos de extremidade de armazenamento compatíveis com HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Metastores opcionais

Você pode criar metastores opcionais do hive ou do Apache Oozie. No entanto, nem todos os tipos de cluster dão suporte a metastores e o Azure SQL Data Warehouse não é compatível com metastores.

Para mais informações, consulte [Utilize lojas de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Quando você cria um metastore personalizado, não use traços, hifens ou espaços no nome do banco de dados. Isso pode fazer com que o processo de criação de cluster falhe.

### <a name="use-hiveoozie-metastore"></a>Metaloja de colmeias

Se você quiser manter as tabelas do hive depois de excluir um cluster HDInsight, use um metastore personalizado. Em seguida, você pode anexar o metastore a outro cluster HDInsight.

HDInsight An metastore criado para uma versão de cluster HDInsight não pode ser compartilhado entre diferentes versões de cluster HDInsight. Para obter uma lista de versões HDInsight, consulte [versões HDInsight suportadas](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metastore do Oozie

Para aumentar o desempenho ao usar o Oozie, use um metastore personalizado. Um metastore também pode fornecer acesso a dados de trabalho do Oozie depois de excluir o cluster.

> [!IMPORTANT]  
> Não é possível reutilizar um metastore personalizado do Oozie. Para usar um metastore personalizado do Oozie, você deve fornecer um banco de dados SQL do Azure vazio ao criar o cluster HDInsight.

## <a name="enterprise-security-package"></a>Pacote de segurança empresarial

Para os tipos de cluster Hadoop, Spark, HBase, Kafka e Interactive Query, pode optar por ativar o Pacote de **Segurança Empresarial**. Este pacote fornece a opção de ter uma configuração de cluster mais segura usando o Apache Ranger e a integração com o Azure Active Directory. Para mais informações, consulte [a visão geral da segurança da empresa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

![opções de criação do hdinsight escolher pacote de segurança empresarial](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking-esp.png)

Para obter mais informações sobre a criação do cluster HDInsight, consulte [Create ambiente de caixa de areia HDInsight unido ao domínio](./domain-joined/apache-domain-joined-configure.md).

## <a name="extend-clusters-with-a-virtual-network"></a>Estender clusters com uma rede virtual

Se a sua solução necessitar de tecnologias que estão espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários. Essa configuração permite que os clusters e qualquer código implantado neles se comuniquem diretamente entre si.

Para obter mais informações sobre a utilização de uma rede virtual Azure com o HDInsight, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para um exemplo de utilização de dois tipos de cluster dentro de uma rede virtual Azure, consulte [Use Apache Spark Structured Streaming com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Para obter mais informações sobre a utilização do HDInsight com uma rede virtual, incluindo requisitos específicos de configuração para a rede virtual, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

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

Para obter mais informações, consulte a [configuração do nó Predefinido e os tamanhos das máquinas virtuais para clusters](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) em "Quais são os componentes e versões Hadoop no HDInsight?"

O custo dos clusters HDInsight é determinado pelo número de nós e pelos tamanhos das máquinas virtuais para os nós.

Tipos de cluster diferentes têm diferentes tipos de nó, números de nós e tamanhos de nó:
* Tipo de cluster Hadoop padrão:
    * Dois *nódosos de cabeça*  
    * Quatro *nódosos operários*
* Padrão do tipo de cluster Storm:
    * Dois *nódosos Nimbus*
    * Três *nóóis zookeeper*
    * Quatro *nomes de supervisor*

Se você estiver apenas experimentando o HDInsight, recomendamos usar um nó de trabalho. Para obter mais informações sobre os preços hDInsight, consulte [os preços hDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> O limite de tamanho do cluster varia entre as assinaturas do Azure. Contacte o [suporte de faturação da Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para aumentar o limite.

Quando utiliza o portal Azure para configurar o cluster, o tamanho do nó está disponível através do separador **de preços Configuração +.** No portal, pode também ver o custo associado aos diferentes tamanhos do nó.

![HDInsight escolha o tamanho do seu nó](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

### <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Ao implantar clusters, escolha recursos de computação com base na solução que você planeja implantar. As seguintes VMs são usadas para clusters HDInsight:

* VMs série A e D1-4: [Tamanhos VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) de propósito geral
* Série D11-14 VM: Tamanhos de [VM linux otimizados pela memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Para descobrir qual o valor que deve utilizar para especificar um tamanho VM enquanto cria um cluster utilizando os diferentes SDKs ou enquanto utiliza o Azure PowerShell, consulte os [tamanhos vM para utilizar para clusters HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). A partir deste artigo ligado, utilize o valor na coluna **tamanho** das tabelas.

> [!IMPORTANT]  
> Se você precisar de mais de 32 nós de trabalho em um cluster, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

Para mais informações, consulte [Tamanhos para máquinas virtuais](../virtual-machines/windows/sizes.md). Para obter informações sobre preços dos vários tamanhos, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="install-hdinsight-applications-on-clusters"></a>Instale aplicações HDInsight em clusters

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Você pode usar aplicativos fornecidos pela Microsoft, por terceiros ou por você mesmo desenvolver. Para mais informações, consulte [Instalar aplicações Apache Hadoop de terceiros no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria dos aplicativos do HDInsight está instalada em um nó de borda vazio.  Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como no nó de cabeçalho. Você pode usar o nó de borda para acessar o cluster, testar seus aplicativos cliente e hospedar seus aplicativos cliente. Para mais informações, consulte [Utilize nós de borda vazia no HDInsight](hdinsight-apps-use-edge-node.md).

![Aplicações de configuração de cluster de portal Azure](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration-applications.png)

## <a name="script-actions"></a>Ações de script

Você pode instalar componentes adicionais ou personalizar a configuração do cluster usando scripts durante a criação. Tais scripts são invocados via **Script Action**, que é uma opção de configuração que pode ser usada a partir do portal Azure, HDInsight Windows PowerShell cmdlets, ou o HDInsight .NET SDK. Para mais informações, consulte [Customize HDInsight cluster usando a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md).

![Ações de script de configuração de cluster de portal Azure](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration-scriptaction.png)

Alguns componentes Java nativos, como Apache Mahout e em cascata, podem ser executados no cluster como arquivos de arquivo Java (JAR). Esses arquivos JAR podem ser distribuídos para o armazenamento do Azure e enviados para clusters HDInsight com mecanismos de envio de trabalho do Hadoop. Para mais informações, consulte [submetor os trabalhos de Hadoop programáticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Se tiver problemas em implementar ficheiros JAR para clusters HDInsight ou chamar ficheiros JAR em clusters HDInsight, contacte o [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> A cascata não é suportada pelo HDInsight e não é elegível para Suporte da Microsoft. Para listas de componentes suportados, consulte [as novidades nas versões de cluster fornecidas pela HDInsight](hdinsight-component-versioning.md).

Às vezes, você deseja configurar os seguintes arquivos de configuração durante o processo de criação:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Para mais informações, consulte [Customize HDInsight clusters usando Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Passos seguintes

* [Falhas na criação de cluster saturadas com azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [O que são hDInsight, o ecossistema Apache Hadoop e aglomerados Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Começar a usar Apache Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Trabalhe em Apache Hadoop no HDInsight a partir de um PC do Windows](hdinsight-hadoop-windows-tools.md)
