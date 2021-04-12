---
title: Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais
description: Configurar os clusters Hadoop, Kafka, Spark, HBase, R Server ou Storm para HDInsight a partir de um navegador, o clássico Azure CLI, Azure PowerShell, REST ou SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 04da79509ac40ba870014995d8318a49c98e1ce5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863416"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Aprenda a configurar e configurar Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services ou Apache Storm em HDInsight. Além disso, aprenda a personalizar clusters e adicione segurança juntando-os a um domínio.

Um cluster Hadoop é composto por várias máquinas virtuais (nós) que são usadas para o processamento distribuído de tarefas. O Azure HDInsight lida com detalhes de implementação da instalação e configuração de nós individuais, pelo que só tem de fornecer informações gerais de configuração.

> [!IMPORTANT]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Aprenda a [apagar um cluster.](hdinsight-delete-cluster.md)

Se estiver a utilizar vários clusters em conjunto, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark também vai querer usar o Conector do Armazém da Colmeia. Para obter mais informações, consulte [Plan a virtual network for Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache [Hive with the Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Métodos de configuração do cluster

A tabela a seguir mostra os diferentes métodos que pode utilizar para configurar um cluster HDInsight.

| Clusters criados com | Browser | Linha de comandos | API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Este artigo acompanha-o através da configuração no [portal Azure,](https://portal.azure.com)onde pode criar um cluster HDInsight.

## <a name="basics"></a>Noções básicas

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="hdinsight criar opções personalizadas rápido":::

### <a name="project-details"></a>Detalhes do projeto

[O Azure Resource Manager](../azure-resource-manager/management/overview.md) ajuda-o a trabalhar com os recursos da sua aplicação como um grupo, referido como um [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups)Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos da sua aplicação numa única operação coordenada.

### <a name="cluster-details"></a>Detalhes do cluster

#### <a name="cluster-name"></a>Nome do cluster

Os nomes do cluster HDInsight têm as seguintes restrições:

* Personagens permitidos: a-z, 0-9, A-Z
* Comprimento máximo: 59
* Nomes reservados: apps
* O cluster naming scope é para todo o Azure, em todas as subscrições. Assim, o nome do cluster deve ser único em todo o mundo.
* Os primeiros seis caracteres devem ser únicos dentro de uma rede virtual

#### <a name="region"></a>Region

Não é necessário especificar explicitamente a localização do cluster: O cluster encontra-se no mesmo local que o armazenamento predefinido. Para obter uma lista de regiões apoiadas, selecione a lista de drop-down da **Região** nos [preços hdInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Tipo de cluster

A Azure HDInsight fornece atualmente os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer certas funcionalidades.

> [!IMPORTANT]  
> Os clusters HDInsight estão disponíveis em vários tipos, cada um para uma única carga de trabalho ou tecnologia. Não existe um método suportado para criar um cluster que combine vários tipos, tais como Storm e HBase em um cluster. Se a sua solução necessitar de tecnologias que estejam espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](../virtual-network/index.yml) pode ligar os tipos de clusters necessários.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Consulta de lote e análise de dados armazenados |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados noSQL sem esquemas |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Caching na memória para consultas interativas e mais rápidas da Colmeia |
| [Kafka](kafka/apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para construir oleodutos e aplicações de dados de streaming em tempo real |
| [Serviços ML](r-server/r-server-overview.md) |Várias estatísticas de big data, modelação preditiva e capacidades de aprendizagem automática |
| [Spark](spark/apache-spark-overview.md) |Processamento em memória, consultas interativas, processamento de fluxo de micro-lotes |
| [Storm](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |

#### <a name="version"></a>Versão

Escolha a versão de HDInsight para este cluster. Para obter mais informações, consulte [as versões HDInsight suportadas.](hdinsight-component-versioning.md#supported-hdinsight-versions)

### <a name="cluster-credentials"></a>Credenciais de cluster

Com clusters HDInsight, pode configurar duas contas de utilizador durante a criação do cluster:

* Nome de utilizador de login do cluster: O nome de utilizador predefinido é *administrador*. Utiliza a configuração básica no portal Azure. Às vezes chama-se "Utilizador do Cluster", ou "utilizador HTTP".
* Nome de utilizador Secure Shell (SSH): Usado para ligar ao cluster através de SSH. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O nome de utilizador HTTP tem as seguintes restrições:

* Personagens especiais permitidos: `_` e `@`
* Caracteres não permitidos: #;."', \/ {} '!*?$)[]<>|&--=+%~^espaço
* Comprimento máximo: 20

O nome de utilizador SSH tem as seguintes restrições:

* Personagens especiais permitidos: `_` e `@`
* Caracteres não permitidos: #;."', \/ {} '!*?$)[]<>|&--=+%~^espaço
* Comprimento máximo: 64
* Nomes reservados: hadoop, utilizadores, oozie, colmeia, maped, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrador, administrador, admin, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, consola, david, convidado, john, proprietário, raiz, servidor, sql, suporte, support_388945a0, sys, test2, test3, user4, user, user5, spark,

## <a name="storage"></a>Armazenamento

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Definições de armazenamento de cluster: pontos finais compatíveis com HDFS":::

Embora uma instalação no local do Hadoop utilize o Hadoop Distributed File System (HDFS) para armazenamento no cluster, na nuvem você usa pontos finais de armazenamento ligados ao cluster. A utilização de armazenamento em nuvem significa que pode eliminar com segurança os clusters HDInsight utilizados para a computação, mantendo os seus dados.

Os clusters HDInsight podem utilizar as seguintes opções de armazenamento:

* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1
* Azure Storage General Purpose v2
* Azure Armazenamento Geral Finalidade v1
* Bolha do bloco de armazenamento Azure **(apenas suportada como armazenamento secundário)**

Para obter mais informações sobre opções de armazenamento com HDInsight, consulte [opções de armazenamento compare para utilização com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> A utilização de uma conta de armazenamento adicional num local diferente do cluster HDInsight não é suportada.

Durante a configuração, para o ponto final de armazenamento predefinido, especifica-se um recipiente blob de uma conta de Armazenamento Azure ou armazenamento do Data Lake. O armazenamento predefinido contém registos de aplicações e sistemas. Opcionalmente, pode especificar contas adicionais de armazenamento Azure e contas de Armazenamento de Data Lake a que o cluster pode aceder. O cluster HDInsight e as contas de armazenamento dependentes devem estar no mesmo local Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Permitir uma transferência segura de armazenamento após a criação de um cluster pode resultar em erros usando a sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com transferência segura já ativada.

> [!Note]  
> O Azure HDInsight não transfere, move ou copia automaticamente os seus dados armazenados no Azure Storage de uma região para outra.

### <a name="metastore-settings"></a>Configurações de metastões

Pode criar metastões opcionais de Hive ou Apache Oozie. No entanto, nem todos os tipos de cluster suportam metastões, e o Azure Synapse Analytics não é compatível com as metastores.

Para obter mais informações, consulte [utilizar lojas de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Quando criar uma metástamo personalizada, não utilize traços, hífens ou espaços no nome da base de dados. Isto pode fazer com que o processo de criação do cluster falhe.

#### <a name="sql-database-for-hive"></a>Base de dados SQL para Colmeia

Se pretender reter as suas tabelas De Colmeia depois de eliminar um cluster HDInsight, utilize uma metástaia personalizada. Em seguida, pode anexar a meta-loja a outro cluster HDInsight.

Uma metásta de HDInsight criada para uma versão hdInsight cluster não pode ser partilhada em diferentes versões de cluster HDInsight. Para obter uma lista de versões HDInsight, consulte [as versões HDInsight suportadas](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A metástore predefinida fornece uma Base de Dados Azure SQL com um **limite básico de DTU de nível 5 (não atualizável)**! Adequado para fins básicos de teste. Para cargas de trabalho grandes ou de produção, recomendamos migrar para uma meta-loja externa.

#### <a name="sql-database-for-oozie"></a>Base de dados SQL para Oozie

Para aumentar o desempenho ao utilizar o Oozie, utilize uma metásta costume. Uma meta-loja também pode fornecer acesso aos dados de trabalho da Oozie depois de eliminar o seu cluster.

#### <a name="sql-database-for-ambari"></a>Base de dados SQL para Ambari

O Ambari é usado para monitorizar clusters HDInsight, fazer alterações de configuração e armazenar informações de gestão de clusters, bem como histórico de trabalho. A funcionalidade DB personalizada permite-lhe implementar um novo cluster e configurar o Ambari numa base de dados externa que gere. Para mais informações, consulte [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Não é possível reutilizar uma metástase oozie personalizada. Para utilizar uma metástaria Oozie personalizada, você deve fornecer uma base de dados Azure SQL vazia ao criar o cluster HDInsight.

## <a name="security--networking"></a>Segurança + networking

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="hdinsight criar opções escolher pacote de segurança da empresa":::

### <a name="enterprise-security-package"></a>Pacote de segurança da empresa

Para tipos de cluster Hadoop, Spark, HBase, Kafka e Interactive Query, pode optar por ativar o **Pacote de Segurança Empresarial.** Este pacote oferece a opção de ter uma configuração de cluster mais segura usando o Apache Ranger e integrando-se com o Azure Ative Directory. Para mais informações, consulte [a visão geral da segurança da empresa em Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

O pacote de segurança da Enterprise permite-lhe integrar o HDInsight com o Ative Directory e o Apache Ranger. Vários utilizadores podem ser criados usando o pacote de segurança Enterprise.

Para obter mais informações sobre a criação de um cluster HDInsight ligado ao domínio, consulte [Criar ambiente de caixa de areia HDInsight ligado ao domínio](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Para mais informações, consulte [a Segurança da Camada de Transporte](./transport-layer-security.md)

### <a name="virtual-network"></a>Rede virtual

Se a sua solução necessitar de tecnologias que estejam espalhadas por vários tipos de cluster HDInsight, uma [rede virtual Azure](../virtual-network/index.yml) pode ligar os tipos de clusters necessários. Esta configuração permite que os clusters, e qualquer código que implemente neles, se comuniquem diretamente uns com os outros.

Para obter mais informações sobre a utilização de uma rede virtual Azure com HDInsight, consulte [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para um exemplo de utilização de dois tipos de cluster dentro de uma rede virtual Azure, consulte [Use Apache Spark Structured Streaming com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Para obter mais informações sobre a utilização do HDInsight com uma rede virtual, incluindo requisitos específicos de configuração para a rede virtual, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Definição de encriptação de disco

Para obter mais informações, consulte a [encriptação do disco de chaves gerida pelo Cliente](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Proxy REST do Kafka

Esta definição só está disponível para o tipo de cluster Kafka. Para obter mais informações, consulte [utilizando um representante do REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identidade

Para obter mais informações, consulte [identidades geridas no Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Configuração + preços

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight escolha o tamanho do seu nó":::

És cobrado para uso de nó enquanto o aglomerado existir. A faturação começa quando um cluster é criado e para quando o cluster é eliminado. Os agrupamentos não podem ser desatribuidos ou colocados em espera.

### <a name="node-configuration"></a>Configuração do nó

Cada tipo de cluster tem o seu próprio número de nós, terminologia para nós, e tamanho VM padrão. Na tabela seguinte, o número de nós para cada tipo de nó está nos parênteses.

| Tipo | Nós | Diagrama |
| --- | --- | --- |
| Hadoop |Nó de cabeça (2), nó do trabalhador (1+) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HdInsight Hadoop cluster nosdes" border="false"::: |
| HBase |Servidor de cabeça (2), servidor de região (1+), nó master/ZooKeeper (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="Configuração do tipo de cluster HDInsight HBase" border="false"::: |
| Storm |Nó Nimbus (2), servidor de supervisor (1+), nó ZooKeeper (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="Configuração do tipo de cluster de tempestade HDInsight" border="false"::: |
| Spark |Nó de cabeça (2), nó do trabalhador (1+), nó ZooKeeper (3) (grátis para o tamanho A1 ZooKeeper VM) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="Configuração do tipo de cluster de faíscas HDInsight" border="false"::: |

Para obter mais informações, consulte [a configuração do nó padrão e os tamanhos de máquinas virtuais para clusters](hdinsight-supported-node-configuration.md) em "Quais são os componentes e versões Hadoop em HDInsight?"

O custo dos clusters HDInsight é determinado pelo número de nós e pelos tamanhos das máquinas virtuais para os nós.

Diferentes tipos de aglomerados têm diferentes tipos de nó, números de nó e tamanhos de nó:
* Padrão do tipo de cluster Hadoop:
    * Dois *acenos de cabeça*
    * Quatro *nódoas operárias*
* Padrão do tipo de aglomerado de tempestade:
    * Dois *nó de Nimbus*
    * Três *nódoas do ZooKeeper*
    * Quatro *nosdes de supervisor*

Se está apenas a experimentar o HDInsight, recomendamos que use um nó operário. Para obter mais informações sobre os preços da HDInsight, consulte [os preços da HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> O limite de tamanho do cluster varia entre as subscrições do Azure. Contacte [o suporte de faturação da Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) para aumentar o limite.

Quando utiliza o portal Azure para configurar o cluster, o tamanho do nó está disponível através do **separador Configuração + preços.** No portal, também é possível ver o custo associado aos diferentes tamanhos dos nós.

### <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais

Quando implementar clusters, escolha recursos de computação com base na solução que pretende implementar. Os seguintes VM são utilizados para clusters HDInsight:

* VMs da série A e D1-4: [Tamanhos Linux VM de uso geral](../virtual-machines/sizes-general.md)
* Série D11-14 VM: [Tamanhos Linux VM otimizados para memória](../virtual-machines/sizes-memory.md)

Para saber qual o valor que deve utilizar para especificar um tamanho VM enquanto cria um cluster utilizando os diferentes SDKs ou durante a utilização do Azure PowerShell, consulte [os tamanhos VM para utilizar para clusters HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). A partir deste artigo ligado, utilize o valor na coluna **Tamanho** das tabelas.

> [!IMPORTANT]  
> Se precisar de mais de 32 nós de trabalhador num cluster, deve selecionar um tamanho de nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM.

Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../virtual-machines/sizes.md) Para obter informações sobre preços dos vários tamanhos, consulte [os preços da HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Adicionar aplicação

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Pode utilizar aplicações fornecidas pela Microsoft, terceiros ou que se desenvolva. Para obter mais informações, consulte [instalar aplicações Apache Hadoop de terceiros no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria das aplicações HDInsight são instaladas num nó de borda vazia.  Um nó de borda vazia é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como no nó da cabeça. Pode utilizar o nó de borda para aceder ao cluster, testar as aplicações do seu cliente e hospedar as aplicações do seu cliente. Para obter mais informações, consulte [Utilize nós de borda vazio em HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Ações de script

Pode instalar componentes adicionais ou personalizar a configuração do cluster utilizando scripts durante a criação. Tais scripts são invocados através do **Script Action**, que é uma opção de configuração que pode ser usada a partir do portal Azure, cmdlets HDInsight Windows PowerShell ou o HDInsight .NET SDK. Para obter mais informações, consulte [o cluster HdInsight personalize usando a Ação script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes nativos de Java, como Apache Mahout e Cascading, podem ser executados no cluster como ficheiros Java Archive (JAR). Estes ficheiros JAR podem ser distribuídos para Azure Storage e submetidos a clusters HDInsight com mecanismos de submissão de emprego Hadoop. Para obter mais informações, consulte [os trabalhos de Submissão da Apache Hadoop programáticamente.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

> [!NOTE]  
> Se tiver problemas em implantar ficheiros JAR para clusters HDInsight ou ligar para ficheiros JAR em clusters HDInsight, contacte [o Microsoft Support](https://azure.microsoft.com/support/options/).
>
> A cascata não é suportada pela HDInsight e não é elegível para o Microsoft Support. Para listas de componentes suportados, consulte [as novidades nas versões de cluster fornecidas pela HDInsight](hdinsight-component-versioning.md).

Por vezes, pretende-se configurar os seguintes ficheiros de configuração durante o processo de criação:

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

Para obter mais informações, consulte [os clusters HDInsight personalizados utilizando o Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Passos seguintes

* [Falhas na criação de clusters de resolução de problemas com Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [O que são HDInsight, o ecossistema Apache Hadoop e os aglomerados hadoop?](hadoop/apache-hadoop-introduction.md)
* [Começar a usar Apache Hadoop em HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Trabalhar em Apache Hadoop em HDInsight a partir de um PC windows](hdinsight-hadoop-windows-tools.md)