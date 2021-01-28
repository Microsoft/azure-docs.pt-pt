---
title: FAQ sobre Apache Kafka em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre Apache Kafka em Azure HDInsight, um serviço de nuvem Hadoop gerido.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939103"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Perguntas mais frequentes sobre o Apache Kafka no Azure HDInsight

Este artigo aborda algumas questões comuns sobre a utilização de Apache Kafka em Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Que versões Kafka são suportadas pela HDInsight?

Encontre mais informações sobre as versões de componentes apoiadas oficialmente pelo [HDInsight em Quais são os componentes e versões Apache Hadoop disponíveis com HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions) Recomendamos sempre a utilização da versão mais recente para garantir o melhor desempenho e experiência do utilizador.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Que recursos são fornecidos num cluster HDInsight Kafka e de que recursos sou cobrado?

Um cluster HDInsight Kafka inclui os seguintes recursos:

* Nó de cabeça
* Nódoa de zookeeper
* Nós de corretor (trabalhador) 
* Discos geridos Azure ligados aos nosdes corretores
* Nó de gateway

Todos estes recursos são cobrados com base no nosso [modelo de preços HDInsight,](https://azure.microsoft.com/pricing/details/hdinsight/)exceto para nós de gateway. Não é cobrado por nós de gateway.

Para obter uma descrição mais detalhada de vários tipos de nós, consulte [a arquitetura de rede virtual Azure HDInsight.](../hdinsight-virtual-network-architecture.md) Os preços baseiam-se na utilização por nó por minuto. Os preços variam dependendo do tamanho do nó, número de nós, tipo de disco gerido utilizado, e região.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>As APIs Apache Kafka funcionam com a HDInsight?

Sim, o HDInsight usa APIs nativos de Kafka. O código de inscrição do seu cliente não precisa de ser alterado. Consulte [tutorial: Use as APIs de Produtor e Consumidor Apache Kafka](./apache-kafka-producer-consumer-api.md) para ver como pode utilizar APIs de produtor/consumidor baseado em Java com o seu cluster.

## <a name="can-i-change-cluster-configurations"></a>Posso alterar as configurações do cluster?

Sim, através do portal Ambari. Cada componente do portal tem uma secção **de configs,** que pode ser usada para alterar configurações de componentes. Algumas alterações podem requerer o reinício do corretor.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Que tipo de autenticação suporta o HDInsight para o Apache Kafka?

Utilizando [o Pacote de Segurança Empresarial (ESP)](../domain-joined/apache-domain-joined-architecture.md), você pode obter segurança de nível tópico para seus clusters Kafka. Consulte [Tutorial: Configurar as políticas apache kafka em HDInsight com Pacote de Segurança Empresarial (Preview)](../domain-joined/apache-domain-joined-run-kafka.md), para obter mais informações.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Os meus dados estão encriptados? Posso usar as minhas chaves?

Todas as mensagens Kafka nos discos geridos são encriptadas com [encriptação do Serviço de Armazenamento Azure (SSE)](../../storage/common/storage-service-encryption.md). Os dados em trânsito (por exemplo, os dados transmitidos de clientes a corretores e o contrário) não são encriptados por padrão. É possível encriptar este tráfego [configurando o TLS](./apache-kafka-ssl-encryption-authentication.md)por conta própria . Além disso, o HDInsight permite-lhe gerir as suas próprias chaves para encriptar os dados em repouso. Consulte a [encriptação do disco de chaves gerida pelo Cliente,](../disk-encryption.md)para obter mais informações.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Como posso ligar os clientes ao meu cluster?

Para que os clientes kafka comuniquem com os corretores Kafka, devem ser capazes de chegar aos corretores através da rede. Para os clusters HDInsight, a Rede Virtual (VNet) é o limite de segurança. Assim, a forma mais fácil de ligar os clientes ao seu cluster HDInsight é criar clientes dentro do mesmo VNet que o cluster. Outros cenários incluem:

* Conectar clientes num VNet Azure diferente – Peer o cluster VNet e o cliente VNet e configurar o cluster para [publicidade IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Ao utilizar a publicidade IP, os clientes kafka devem utilizar endereços IP do Corretor para se conectarem com os corretores, em vez de Nomes de Domínio Totalmente Qualificados (FQDNs).

* Conectar clientes no local – Utilizar uma rede VPN e configurar servidores DNS personalizados, conforme descrito no [Plano, uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Criar um ponto final público para o seu serviço Kafka – Se os requisitos de segurança da sua empresa o permitirem, pode implantar um ponto final público para os seus corretores Kafka, ou um ponto final de rest de código aberto auto-gerido com um ponto final público.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Posso adicionar mais espaço em disco num cluster existente?

Para aumentar a quantidade de espaço disponível para mensagens Kafka, pode aumentar o número de nós. Atualmente, adicionar mais discos a um cluster existente não é suportado.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Um cluster Kafka pode funcionar com databricks? 

Sim, os clusters kafka podem trabalhar com databricks desde que estejam no mesmo VNet. Para utilizar um cluster Kafka com Databricks, crie um VNet com um cluster HDInsight Kafka nele, em seguida, especifique que o VNet quando criar o seu espaço de trabalho databricks e usar a injeção VNet. Para obter mais informações, consulte [implementar databricks do Azure na sua Rede Virtual Azure (Injeção VNet)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Você precisará fornecer os nomes de corretor de botas do cluster Kafka ao criar o espaço de trabalho Databricks. Para obter informações sobre a recuperação dos nomes do corretor Kafka, consulte [as informações do hospedeiro Apache Zookeeper e Broker](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Como posso ter a durabilidade máxima dos dados?

A durabilidade dos dados permite-lhe atingir o menor risco de perda de mensagens. Para alcançar a máxima durabilidade dos dados, recomendamos as seguintes definições:

* usar um fator de replicação mínima de 3 na maioria das regiões
* utilizar um fator de replicação mínima de 4 em regiões com apenas dois domínios de avaria
* desativar eleições líder impuro
* definir **min.insync.replicas** para 2 ou mais - isto altera o número de réplicas que devem estar completamente em sintonia com o líder antes que uma escrita possa prosseguir
* definir a propriedade **acks** para **todos** - esta propriedade requer todas as réplicas para reconhecer todas as mensagens

Configurar a Kafka para uma maior consistência de dados afeta a disponibilidade de corretores para produzir pedidos.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Posso replicar os meus dados em vários grupos?

Sim, os dados podem ser replicados em vários clusters usando Kafka MirrorMaker. Veja os detalhes sobre a configuração do MirrorMaker nos [tópicos Mirror Apache Kafka](apache-kafka-mirroring.md). Além disso, existem outras tecnologias e fornecedores de código aberto auto-geridos que podem ajudar a conseguir a replicação em vários clusters, como [o Brooklin.](https://github.com/linkedin/Brooklin/)

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Posso melhorar o meu agrupamento? Como devo melhorar o meu aglomerado?

Atualmente, não apoiamos atualizações de versão de cluster no local. Para atualizar o seu cluster para uma versão kafka superior, crie um novo cluster com a versão que deseja e migrar os seus clientes Kafka para usar o novo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Como monitorizo o meu aglomerado de Kafka?

Utilize o monitor Azure para analisar os seus [registos Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Próximos passos

* [Configurar encriptação e autenticação TLS para Apache Kafka em Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Use MirrorMaker to replicate Apache Kafka topics with Kafka on HDInsight](./apache-kafka-mirroring.md) (Utilizar o MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight)