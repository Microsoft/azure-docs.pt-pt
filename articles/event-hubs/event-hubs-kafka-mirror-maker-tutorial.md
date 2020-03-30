---
title: Use Apache Kafka MirrorMaker - Azure Event Hubs [ Hubs de eventos De Ana) Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka MirrorMaker para espelhar um cluster Kafka em AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6dc902b6a26c175713381b4fce88934dca3f409e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283587"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Use O MirrorMaker Kafka com Hubs de Eventos para Apache Kafka

Este tutorial mostra como espelhar um corretor Kafka num centro de eventos ativado por Kafka usando kafka MirrorMaker.

   ![Kafka MirrorMaker com Hubs de Eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Criar um aglomerado de Kafka
> * Configure Kafka MirrorMaker
> * Executar Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Uma das principais considerações para as aplicações modernas em escala de nuvem é a capacidade de atualizar, melhorar e alterar infraestruturas sem interromper o serviço. Este tutorial mostra como um hub de eventos e kafka MirrorMaker podem integrar um oleoduto kafka existente em Azure, "espelhando" o fluxo de entrada kafka no serviço Event Hubs. 

Um ponto final do Azure Event Hubs Kafka permite-lhe ligar-se aos Hubs de Eventos Azure utilizando o protocolo Kafka (isto é, clientes Kafka). Ao fazer alterações mínimas numa aplicação Kafka, pode ligar-se aos Hubs de Eventos Azure e usufruir dos benefícios do ecossistema Azure. Kafka habilitado Event Hubs atualmente suporta as versões Kafka 1.0 e mais tarde.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Kit de Desenvolvimento Java (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Descarregue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Para obter instruções sobre como conseguir um ponto final de Kafka dos Hubs de Eventos, veja [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Criar um hub de eventos com Kafka ativado). Certifique-se de copiar a cadeia de ligação De Eventos Hubs para posterior utilização.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem uma cadeia de ligação Kafka Habilitada para o Event Hubs, clone `mirror-maker` os Hubs de Eventos Azure para o repositório Kafka e navegue para a subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Criar um aglomerado de Kafka

Utilize o [guia kafka quickstart](https://kafka.apache.org/quickstart) para configurar um cluster com as definições desejadas (ou utilize um aglomerado kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configure Kafka MirrorMaker

Kafka MirrorMaker permite o "espelhamento" de um riacho. Dada a origem e destino dos clusters Kafka, o MirrorMaker garante que quaisquer mensagens enviadas para o cluster de origem são recebidas tanto pelos clusters de origem como pelos grupos de destino. Este exemplo mostra como espelhar um aglomerado de origem Kafka com um centro de eventos de destino. Este cenário pode ser usado para enviar dados de um oleoduto kafka existente para Centros de Eventos sem interromper o fluxo de dados. 

Para obter informações mais detalhadas sobre o Kafka MirrorMaker, consulte o [guia Kafka Mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dê-lhe um cluster Kafka como seu consumidor/fonte e um centro de eventos como seu produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o `source-kafka.config`ficheiro de configuração do consumidor, que indica ao MirrorMaker as propriedades do cluster de origem Kafka.

##### <a name="source-kafkaconfig"></a>fonte-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora atualize o `mirror-eventhub.config`ficheiro de configuração do produtor , que diz ao MirrorMaker para enviar os dados duplicados (ou "espelhados") para o serviço Event Hubs. Especificamente, `bootstrap.servers` `sasl.jaas.config` mude e aponte para o seu ponto final do Event Hubs Kafka. O serviço Event Hubs requer uma comunicação segura (SASL), que é alcançada através da definição das últimas três propriedades na seguinte configuração: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Executar Kafka MirrorMaker

Execute o script Kafka MirrorMaker a partir do diretório root Kafka usando os ficheiros de configuração recentemente atualizados. Certifique-se de copiar os ficheiros de config para o diretório raiz kafka, ou atualizar os seus caminhos no seguinte comando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar se os eventos estão a chegar ao centro do evento, consulte as estatísticas de ingresso no [portal Azure,](https://azure.microsoft.com/features/azure-portal/)ou dirija um consumidor contra o centro do evento.

Com o MirrorMaker a funcionar, quaisquer eventos enviados para a fonte do cluster Kafka são recebidos tanto pelo cluster Kafka como pelo serviço de hub de eventos ativado por Kafka. Ao utilizar o MirrorMaker e um ponto final do Event Hubs Kafka, pode migrar um oleoduto Kafka existente para o serviço gerido do Azure Event Hubs sem alterar o cluster existente ou interromper qualquer fluxo de dados em curso.

## <a name="samples"></a>Amostras
Consulte as seguintes amostras no GitHub:

- [Código de amostra para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker em execução em uma instância de contentor estampada](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Criar um aglomerado de Kafka
> * Configure Kafka MirrorMaker
> * Executar Kafka MirrorMaker

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [Como criar um centro de eventos](event-hubs-create.md)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Ligue a Akka Streams a um centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
