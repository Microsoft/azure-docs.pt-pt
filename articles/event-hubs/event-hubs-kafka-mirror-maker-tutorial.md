---
title: Use Apache Kafka MirrorMaker - Azure Event Hubs Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka MirrorMaker para espelhar um cluster Kafka em AzureEvent Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: ab0f78adeeff34334c9800632fc58ab634b4fab6
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308346"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Use Kafka MirrorMaker com centros de eventos para Apache Kafka

Este tutorial mostra como espelhar um corretor Kafka num centro de eventos usando Kafka MirrorMaker.

   ![Kafka MirrorMaker com centros de eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Criar um cluster Kafka
> * Configurar Kafka MirrorMaker
> * Executar Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Uma das principais considerações para as aplicações modernas em nuvem é a capacidade de atualizar, melhorar e alterar infraestruturas sem interromper o serviço. Este tutorial mostra como um centro de eventos e Kafka MirrorMaker podem integrar um oleoduto Kafka existente em Azure ao "espelhar" o fluxo de entrada kafka no serviço Event Hubs. 

Um ponto final do Azure Event Hubs Kafka permite-lhe ligar-se aos Azure Event Hubs utilizando o protocolo Kafka (ou seja, clientes Kafka). Ao fazer alterações mínimas numa aplicação kafka, pode ligar-se aos Azure Event Hubs e usufruir dos benefícios do ecossistema Azure. O Event Hubs suporta atualmente as versões Kafka 1.0 e posterior.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Kit de Desenvolvimento java (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Faça o download](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Rio Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Consulte [a criação de um centro de eventos](event-hubs-create.md) para obter instruções para criar um espaço de nome e um centro de eventos. Certifique-se de que copia o fio de ligação Do Event Hubs para utilização posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem uma cadeia de conexão Event Hubs, clone os Hubs de Eventos Azure para o repositório de Kafka e navegue até à `mirror-maker` sub-ligação:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Criar um cluster Kafka

Utilize o [guia de arranque rápido kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as definições desejadas (ou utilizar um cluster Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar Kafka MirrorMaker

Kafka MirrorMaker permite o "espelhamento" de um fluxo. Dada a fonte e destino dos clusters Kafka, o MirrorMaker garante que quaisquer mensagens enviadas para o cluster de origem são recebidas tanto pelos clusters de origem como de destino. Este exemplo mostra como espelhar um cluster kafka de origem com um centro de eventos de destino. Este cenário pode ser usado para enviar dados de um oleoduto kafka existente para Os Centros de Eventos sem interromper o fluxo de dados. 

Para obter informações mais detalhadas sobre o Kafka MirrorMaker, consulte o [guia Kafka Mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar a Kafka MirrorMaker, dê-lhe um cluster Kafka como consumidor/fonte e um centro de eventos como produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o ficheiro de configuração do `source-kafka.config` consumidor, que diz ao MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora atualize o ficheiro de configuração do `mirror-eventhub.config` produtor, que diz ao MirrorMaker para enviar os dados duplicados (ou "espelhados") para o serviço Event Hubs. Especificamente, mude `bootstrap.servers` e `sasl.jaas.config` aponte para o seu ponto final de Eventos Kafka. O serviço Event Hubs requer uma comunicação segura (SASL), que é conseguida através da definição das últimas três propriedades na seguinte configuração: 

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

Execute o script Kafka MirrorMaker a partir do diretório de raiz kafka usando os ficheiros de configuração recentemente atualizados. Certifique-se de copiar os ficheiros config para o diretório de raiz de Kafka ou atualizar os seus caminhos no comando seguinte.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar se os eventos estão a chegar ao centro de eventos, consulte as estatísticas de entrada no [portal Azure,](https://azure.microsoft.com/features/azure-portal/)ou gere um consumidor contra o centro de eventos.

Com o MirrorMaker em execução, quaisquer eventos enviados para o cluster kafka de origem são recebidos tanto pelo cluster Kafka como pelo centro de eventos espelhado. Ao utilizar o MirrorMaker e um ponto final de Event Hubs Kafka, pode migrar um oleoduto Kafka existente para o serviço gerido Azure Event Hubs sem alterar o cluster existente ou interromper qualquer fluxo de dados em curso.

## <a name="samples"></a>Amostras
Veja as seguintes amostras no GitHub:

- [Código de amostra para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker correndo em uma instância de recipiente Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integre kafka Connect com um centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
- [Ligar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia de desenvolvimento apache Kafka para hubs de eventos Azure](apache-kafka-developer-guide.md)