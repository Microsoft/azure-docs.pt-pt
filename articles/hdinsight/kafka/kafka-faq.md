---
title: FAQ sobre Apache Kafka em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre Apache Kafka no Azure HDInsight, um serviço de nuvem hadoop gerido.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206984"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Perguntas frequentes sobre Apache Kafka no Azure HDInsight

Este artigo aborda algumas questões comuns sobre a utilização de Apache Kafka no Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Que versões Kafka são suportadas pela HDInsight?

Encontre mais informações sobre as versões componentes suportadas oficialmente pelo HDInsight em [Quais são os componentes e versões Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions) Recomendamos sempre a utilização da versão mais recente para garantir o melhor desempenho possível e experiência do utilizador.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Que recursos são fornecidos num cluster HDInsight Kafka e quais os recursos cobrados?

Um cluster HDInsight Kafka inclui os seguintes recursos:

* Nós de cabeça
* Nódoa zookeeper
* Nós de corretor (trabalhador) 
* Discos geridos azure ligados aos nódosos de corretor
* Nós de gateway

Todos estes recursos são cobrados com base no nosso modelo de [preços HDInsight,](https://azure.microsoft.com/pricing/details/hdinsight/)exceto nos nós de gateway. Não é cobrado pelos nódosos de gateway.

Para obter uma descrição mais detalhada de vários tipos de nó, consulte a arquitetura de [rede virtual Azure HDInsight](../hdinsight-virtual-network-architecture.md). Os preços baseiam-se na utilização do nó por minuto. Os preços variam consoante o tamanho do nó, o número de nós, o tipo de disco gerido utilizado e a região.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>As APIs Apache Kafka trabalham com o HDInsight?

Sim, o HDInsight usa APIs nativos de Kafka. O código de candidatura do seu cliente não precisa de ser alterado. Consulte [Tutorial: Utilize o Produtor Apache Kafka e as APIs de consumo](./apache-kafka-producer-consumer-api.md) para ver como pode utilizar APIs de produtor/consumidor baseados em Java com o seu cluster.

## <a name="can-i-change-cluster-configurations"></a>Posso mudar as configurações do cluster?

Sim, através do portal Ambari. Cada componente no portal tem uma secção **de configs,** que pode ser usada para alterar as configurações dos componentes. Algumas alterações podem exigir reinício do corretor.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Que tipo de autenticação o HDInsight suporta para Apache Kafka?

Utilizando o [Enterprise Security Package (ESP),](../domain-joined/apache-domain-joined-architecture.md)pode obter segurança de nível tópico para os seus clusters Kafka. Consulte [tutorial: Configure as políticas de Apache Kafka no HDInsight com o Pacote de Segurança Empresarial (Pré-visualização)](../domain-joined/apache-domain-joined-run-kafka.md), para obter mais informações.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Os meus dados estão encriptados? Posso usar as minhas próprias chaves?

Todas as mensagens Kafka nos discos geridos são encriptadas com encriptação do serviço de [armazenamento Azure (SSE)](../../storage/common/storage-service-encryption.md). O data-in-transit (por exemplo, os dados que estão a ser transmitidos de clientes para corretores e o contrário) não são encriptados por padrão. É possível encriptar esse tráfego [instalando o SSL por conta própria.](./apache-kafka-ssl-encryption-authentication.md) Além disso, o HDInsight permite-lhe gerir as suas próprias chaves para encriptar os dados em repouso. Consulte a encriptação do disco de [chave gerida pelo Cliente,](../disk-encryption.md)para obter mais informações.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Como posso ligar clientes ao meu cluster?

Para que os clientes kafka se comuniquem com os corretores Kafka, devem ser capazes de contactar os corretores através da rede. Para os clusters HDInsight, a Rede Virtual (VNet) é o limite de segurança. Assim, a forma mais fácil de ligar os clientes ao seu cluster HDInsight é criar clientes dentro do mesmo VNet que o cluster. Outros cenários incluem:

* Conectando clientes num Azure VNet diferente – Peer o cluster VNet e o cliente VNet e configurar o cluster para [publicidade IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Ao utilizar publicidade IP, os clientes kafka devem usar endereços IP broker para se conectarem com os corretores, em vez de Nomes de Domínio totalmente qualificados (FQDNs).

* Ligar clientes no local – Utilizando uma rede VPN e configurando servidores DNS personalizados, conforme descrito no [Plano uma rede virtual para Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Criar um ponto final público para o seu serviço Kafka – Se os seus requisitos de segurança da empresa o permitirem, pode implementar um ponto final público para os seus corretores Kafka, ou um ponto final de rest de fonte aberta autogerido com um ponto final público.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Posso adicionar mais espaço em disco num aglomerado existente?

Para aumentar a quantidade de espaço disponível para mensagens Kafka, pode aumentar o número de nós. Atualmente, adicionar mais discos a um cluster existente não é suportado.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Um aglomerado de Kafka pode funcionar com databricks? 

Sim, os aglomerados kafka podem trabalhar com databricks desde que estejam no mesmo VNet. Para utilizar um cluster Kafka com Databricks, crie um VNet com um cluster HDInsight Kafka no mesmo e, em seguida, especifique que vNet quando criar o seu espaço de trabalho Databricks e usar a injeção VNet. Para mais informações, consulte A implantação de tijolos de [dados Azure na sua Rede Virtual Azure (Injeção VNet)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Você precisará fornecer os nomes de corretor de bootstrap do cluster Kafka ao criar o espaço de trabalho Databricks. Para obter informações sobre a recuperação dos nomes do corretor Kafka, consulte [o Guardião do Zoológico apache e a informação](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)do anfitrião do corretor.

## <a name="how-can-i-have-maximum-data-durability"></a>Como posso ter a máxima durabilidade de dados?

A durabilidade dos dados permite-lhe alcançar o menor risco de perda de mensagem. Para alcançar a máxima durabilidade dos dados, recomendamos as seguintes definições:

* utilizar um fator de replicação mínimo de 3 na maioria das regiões
* utilizar um fator de replicação mínimo de 4 em regiões com apenas dois domínios de falha
* eleições líderes impuros desativar
* definir **min.insync.replicas** para 2 ou mais - isto altera o número de réplicas que devem estar completamente sincronizadas com o líder antes que uma escrita possa prosseguir
* definir a propriedade **acks** para **todos** - esta propriedade requer todas as réplicas para reconhecer todas as mensagens

Configurar kafka para uma maior consistência de dados afeta a disponibilidade de corretores para produzir pedidos.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Posso replicar os meus dados em vários aglomerados?

Sim, os dados podem ser replicados em vários clusters usando Kafka MirrorMaker. Veja detalhes sobre a configuração do MirrorMaker pode ser encontrado nos [tópicos Mirror Apache Kafka](apache-kafka-mirroring.md). Além disso, existem outras tecnologias e fornecedores de código aberto autogeridos que podem ajudar a alcançar a replicação a vários clusters como [brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Posso atualizar o meu cluster? Como devo atualizar o meu cluster?

Atualmente não suportamos atualizações de versão cluster no local. Para atualizar o seu cluster para uma versão kafka mais alta, crie um novo cluster com a versão que deseja e migra os seus clientes Kafka para usar o novo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Como posso monitorizar o meu aglomerado de Kafka?

Utilize o monitor Azure para analisar os seus [registos Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar encriptação e autenticação da camada de tomadas seguras (SSL) para Apache Kafka em Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Use MirrorMaker to replicate Apache Kafka topics with Kafka on HDInsight](./apache-kafka-mirroring.md) (Utilizar o MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight)
