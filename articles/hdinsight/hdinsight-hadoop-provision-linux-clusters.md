---
title: Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais
description: Configurar os clusters Hadoop, Kafka, Spark, HBase, R Server ou Storm para HDInsight a partir de um browser, o clássico AZURI, Azure PowerShell, REST ou SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 04/30/2020
ms.openlocfilehash: 4eaa9c4e3d200eedd57c468639c1af3830911d1d
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889264"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como configurar e configurar Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services ou Apache Storm em HDInsight. Além disso, aprenda a personalizar clusters e adicionar segurança juntando-os a um domínio.

Um cluster Hadoop é composto por várias máquinas virtuais (nós) que são usadas para o processamento distribuído de tarefas. O Azure HDInsight lida com detalhes de implementação da instalação e configuração de nós individuais, pelo que só tem de fornecer informações gerais de configuração.

> [!IMPORTANT]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Aprenda a [apagar um cluster.](hdinsight-delete-cluster.md)

Se estiver a utilizar vários clusters juntos, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark, também vai querer usar o Conector de Armazém hive. Para mais informações, consulte [Plan a rede virtual para Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) e [integre a Apache Spark e a Apache Hive com o Conector de Armazém hive](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Métodos de configuração de cluster

A tabela que se segue mostra os diferentes métodos que pode utilizar para configurar um cluster HDInsight.

| Aglomerados criados com | Browser | Linha de comandos | API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Este artigo acompanha-o através da configuração no [portal Azure,](https://portal.azure.com)onde pode criar um cluster HDInsight.

## <a name="basics"></a>Noções básicas

![hdinsight criar opções personalizadas rapidamente](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Detalhes do projeto

[O Azure Resource Manager](../azure-resource-manager/management/overview.md) ajuda-o a trabalhar com os recursos da sua aplicação como um grupo, referido como um grupo de [recursos](../azure-resource-manager/management/overview.md#resource-groups)Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos para a sua aplicação numa única operação coordenada.

### <a name="cluster-details"></a>Detalhes do cluster

#### <a name="cluster-name"></a>Nome do cluster

Os nomes de cluster HDInsight têm as seguintes restrições:

* Caracteres permitidos: a-z, 0-9, A-Z
* Comprimento máximo: 59
* Nomes reservados: apps
* O âmbito de nomeação do cluster é para todos os Azure, em todas as subscrições. Então o nome do cluster deve ser único em todo o mundo.
* Os primeiros seis caracteres devem ser únicos dentro de uma rede virtual

#### <a name="region"></a>Região

Não é necessário especificar explicitamente a localização do cluster: O cluster encontra-se no mesmo local que o armazenamento predefinido. Para obter uma lista de regiões apoiadas, selecione a lista de abandono da **Região** nos [preços HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Tipo de cluster

O Azure HDInsight fornece atualmente os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer determinadas funcionalidades.

> [!IMPORTANT]  
> Os clusters HDInsight estão disponíveis em vários tipos, cada um para uma única carga de trabalho ou tecnologia. Não existe um método suportado para criar um cluster que combine vários tipos, tais como Storm e HBase em um cluster. Se a sua solução necessitar de tecnologias que estão espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Consulta de lote e análise de dados armazenados |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados sem esquema, NoSQL |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Caching na memória para consultas interativas e mais rápidas da Colmeia |
| [Kafka](kafka/apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para construir pipelines e aplicações de dados de streaming em tempo real |
| [Serviços ML](r-server/r-server-overview.md) |Várias estatísticas de big data, modelação preditiva e capacidades de aprendizagem automática |
| [Spark](spark/apache-spark-overview.md) |Processamento de memória, consultas interativas, processamento de fluxo de micro-lote |
| [Storm](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |

#### <a name="version"></a>Versão

Escolha a versão do HDInsight para este cluster. Para mais informações, consulte [as versões HDInsight suportadas](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Credenciais de cluster

Com os clusters HDInsight, pode configurar duas contas de utilizador durante a criação do cluster:

* Nome de utilizador de login do cluster: O nome de utilizador predefinido é *administrador*. Utiliza a configuração básica no portal Azure. Às vezes chama-se "Utilizador do Cluster", ou "utilizador http".
* Nome de utilizador secure Shell (SSH): Utilizado para ligar ao cluster através do SSH. Para mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O nome de utilizador HTTP tem as seguintes restrições:

* Caracteres especiais `_` permitidos: e`@`
* Personagens não permitidas:\/#;,"",,','!*?$(][]<>{}&-=+~~espaço
* Comprimento máximo: 20

O nome de utilizador SSH tem as seguintes restrições:

* Caracteres especiais`_` permitidos: e`@`
* Personagens não permitidas:\/#;,"",,','!*?$(][]<>{}&-=+~~espaço
* Comprimento máximo: 64
* Nomes reservados: hadoop, utilizadores, oozie, colmeia, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, fios, hcat, ams, hbase, storm, administrador, administrador, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, consola, david, guest, john, proprietário, raiz, servidor, sql, suporte, support_388945a0, sys, test2, test3, user, user, spark4,

## <a name="storage"></a>Armazenamento

![Definições de armazenamento de cluster: pontos finais compatíveis com HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Embora uma instalação no local de Hadoop utilize o Sistema de Ficheiros Distribuídos Hadoop (HDFS) para armazenamento no cluster, na nuvem utiliza pontos finais de armazenamento ligados ao cluster. Utilizar o armazenamento em nuvem significa que pode eliminar com segurança os clusters HDInsight utilizados para a computação enquanto ainda conserva os seus dados.

Os clusters HDInsight podem utilizar as seguintes opções de armazenamento:

* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1
* Finalidade geral do armazenamento azure v2
* Finalidade geral do armazenamento azure v1
* Blob do Bloco de Armazenamento Azure **(apenas suportado como armazenamento secundário)**

Para obter mais informações sobre as opções de armazenamento com o HDInsight, consulte Compare opções de [armazenamento para utilização com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> A utilização de uma conta de armazenamento adicional num local diferente do cluster HDInsight não é suportada.

Durante a configuração, para o ponto final de armazenamento predefinido, especifique um recipiente de bolha de uma conta de Armazenamento Azure ou armazenamento de data lake. O armazenamento predefinido contém registos de aplicação e sistema. Opcionalmente, pode especificar contas adicionais de Armazenamento Azure ligadas e contas de Armazenamento de Data Lake a que o cluster pode aceder. O cluster HDInsight e as contas de armazenamento dependentes devem estar na mesma localização Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Permitir a transferência de armazenamento segura após a criação de um cluster pode resultar em erros utilizando a sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com transferência segura já ativada.

### <a name="metastore-settings"></a>Definições de metaloja

Pode criar lojas opcionais da Hive ou Apache Oozie. No entanto, nem todos os tipos de cluster suportam metalojas, e o Azure SQL Data Warehouse não é compatível com metalojas.

Para mais informações, consulte [Utilize lojas de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Quando criar uma metaloja personalizada, não utilize traços, hífens ou espaços no nome da base de dados. Isto pode fazer com que o processo de criação do cluster falhe.

#### <a name="sql-database-for-hive"></a>Base de dados SQL para Hive

Se quiser reter as suas tabelas de Colmeia sem eliminar um cluster HDInsight, utilize uma metaloja personalizada. Em seguida, pode anexar a metaloja a outro cluster HDInsight.

Uma metastore HDInsight que é criada para uma versão de cluster HDInsight não pode ser partilhada em diferentes versões de cluster HDInsight. Para obter uma lista de versões HDInsight, consulte [versões HDInsight suportadas](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>Base de dados SQL para Oozie

Para aumentar o desempenho ao utilizar o Oozie, utilize uma metaloja personalizada. Uma metaloja também pode fornecer acesso aos dados de trabalho da Oozie depois de eliminar o seu cluster.

#### <a name="sql-database-for-ambari"></a>Base de dados SQL para Ambari

Ambari é usado para monitorizar clusters HDInsight, fazer alterações de configuração e armazenar informações de gestão de clusters, bem como histórico de trabalho. A funcionalidade Ambari DB personalizada permite-lhe implementar um novo cluster e configurar ambari numa base de dados externa que gere. Para mais informações, consulte [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Não é possível reutilizar uma metaloja Oozie personalizada. Para utilizar uma metaloja Oozie personalizada, deve fornecer uma base de dados Azure SQL vazia ao criar o cluster HDInsight.

## <a name="security--networking"></a>Segurança + networking

![hdinsight criar opções escolha pacote de segurança da empresa](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Pacote de segurança empresarial

Para os tipos de cluster Hadoop, Spark, HBase, Kafka e Interactive Query, pode optar por ativar o Pacote de **Segurança Empresarial**. Este pacote oferece opção de ter uma configuração de cluster mais segura usando Apache Ranger e integrando-se com o Azure Ative Directory. Para mais informações, consulte [a visão geral da segurança da empresa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

O pacote de segurança enterprise permite-lhe integrar o HDInsight com o Ative Directory e o Apache Ranger. Vários utilizadores podem ser criados usando o pacote de segurança da Enterprise.

Para obter mais informações sobre a criação do cluster HDInsight, consulte [Create ambiente de caixa de areia HDInsight unido ao domínio](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Para mais informações, consulte [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Rede virtual

Se a sua solução necessitar de tecnologias que estão espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários. Esta configuração permite que os clusters, e qualquer código que implemente para eles, se comuniquem diretamente uns com os outros.

Para obter mais informações sobre a utilização de uma rede virtual Azure com o HDInsight, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para um exemplo de utilização de dois tipos de cluster dentro de uma rede virtual Azure, consulte [Use Apache Spark Structured Streaming com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Para obter mais informações sobre a utilização do HDInsight com uma rede virtual, incluindo requisitos específicos de configuração para a rede virtual, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Definição de encriptação do disco

Para obter mais informações, consulte a [encriptação](./disk-encryption.md)do disco de chave gerida pelo Cliente .

### <a name="kafka-rest-proxy"></a>Proxy REST do Kafka

Esta definição só está disponível para o tipo de cluster Kafka. Para mais informações, consulte [Utilizar um proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identidade

Para mais informações, consulte [identidades geridas no Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Configuração + preços

![HDInsight escolha o tamanho do seu nó](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

É cobrado para uso de nó durante o tempo que o cluster existe. A faturação começa quando um cluster é criado e para quando o cluster é eliminado. Os agrupamentos não podem ser desalocados ou colocados em espera.

### <a name="node-configuration"></a>Configuração do nó

Cada tipo de cluster tem o seu próprio número de nós, terminologia para nós e tamanho VM padrão. Na tabela seguinte, o número de nós para cada tipo de nó é em parênteses.

| Tipo | Nós | Diagrama |
| --- | --- | --- |
| Hadoop |Nó da cabeça (2), nó de trabalhador (1+) |![Nós de cluster de hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Servidor de cabeça (2), servidor da região (1+), nó master/ZooKeeper (3) |![Configuração do tipo de cluster HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nó Nimbus (2), servidor de supervisor (1+), nó ZooKeeper (3) |![Configuração do tipo de cluster de tempestade HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nó de cabeça (2), nó de trabalhador (1+), nó ZooKeeper (3) (grátis para o tamanho VM Do ZooKeeper A1) |![Configuração do tipo de cluster de faíscaHDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Para obter mais informações, consulte a [configuração do nó Predefinido e os tamanhos das máquinas virtuais para clusters](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) em "Quais são os componentes e versões Hadoop no HDInsight?"

O custo dos clusters HDInsight é determinado pelo número de nós e tamanhos das máquinas virtuais para os nós.

Diferentes tipos de cluster têm diferentes tipos de nós, números de nós e tamanhos de nós:
* Padrão do tipo de cluster hadoop:
    * Dois *nódosos de cabeça*  
    * Quatro *nódosos operários*
* Padrão do tipo de aglomerado de tempestade:
    * Dois *nódosos Nimbus*
    * Três *nóóis zookeeper*
    * Quatro *nomes de supervisor*

Se estiver apenas a experimentar o HDInsight, recomendamos que use um nó de Trabalhador. Para obter mais informações sobre os preços hDInsight, consulte [os preços hDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> O limite de tamanho do cluster varia entre as assinaturas Azure. Contacte o [suporte de faturação da Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para aumentar o limite.

Quando utiliza o portal Azure para configurar o cluster, o tamanho do nó está disponível através do separador **de preços Configuração +.** No portal, pode também ver o custo associado aos diferentes tamanhos do nó.

### <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Quando implementar clusters, escolha recursos computacionais com base na solução que pretende implementar. Os seguintes VMs são utilizados para clusters HDInsight:

* VMs série A e D1-4: [Tamanhos VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) de propósito geral
* Série D11-14 VM: Tamanhos de [VM linux otimizados pela memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Para descobrir qual o valor que deve utilizar para especificar um tamanho VM enquanto cria um cluster utilizando os diferentes SDKs ou enquanto utiliza o Azure PowerShell, consulte os [tamanhos vM para utilizar para clusters HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). A partir deste artigo ligado, utilize o valor na coluna **tamanho** das tabelas.

> [!IMPORTANT]  
> Se necessitar de mais de 32 nós de trabalhador num cluster, deve selecionar um tamanho de nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM.

Para mais informações, consulte [Tamanhos para máquinas virtuais](../virtual-machines/windows/sizes.md). Para obter informações sobre preços dos vários tamanhos, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Adicionar aplicação

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Pode utilizar aplicações fornecidas pela Microsoft, terceiros ou que se desenvolva. Para mais informações, consulte [Instalar aplicações Apache Hadoop de terceiros no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria das aplicações HDInsight estão instaladas num nó de borda vazia.  Um nó de borda vazia é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como no nó da cabeça. Pode utilizar o nó de borda para aceder ao cluster, testar as aplicações do seu cliente e hospedar as aplicações do seu cliente. Para mais informações, consulte [Utilize nós de borda vazia no HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Ações de script

Pode instalar componentes adicionais ou personalizar a configuração do cluster utilizando scripts durante a criação. Tais scripts são invocados via **Script Action**, que é uma opção de configuração que pode ser usada a partir do portal Azure, HDInsight Windows PowerShell cmdlets, ou o HDInsight .NET SDK. Para mais informações, consulte [Customize HDInsight cluster usando a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes nativos de Java, como Apache Mahout e Cascading, podem ser executados no cluster como ficheiros Java Archive (JAR). Estes ficheiros JAR podem ser distribuídos para o Armazenamento Azure e submetidos a clusters HDInsight com mecanismos de submissão de emprego hadoop. Para mais informações, consulte [submetor os trabalhos de Hadoop programáticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Se tiver problemas em implementar ficheiros JAR para clusters HDInsight ou chamar ficheiros JAR em clusters HDInsight, contacte o [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> A cascata não é suportada pelo HDInsight e não é elegível para suporte da Microsoft. Para listas de componentes suportados, consulte [as novidades nas versões de cluster fornecidas pela HDInsight](hdinsight-component-versioning.md).

Por vezes, pretende configurar os seguintes ficheiros de configuração durante o processo de criação:

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
