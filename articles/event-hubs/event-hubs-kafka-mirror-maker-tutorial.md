---
title: Utilizar MirrorMaker do Apache Kafka com os Hubs de eventos do Azure para o ecossistema de Kafka | Documentos da Microsoft
description: Utilize o Kafka MirrorMaker para espelhar um cluster do Kafka nos Hubs de eventos.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 08/07/2018
ms.author: bahariri
ms.openlocfilehash: e9ae6349d149c90fddc29d5cc1fb3c15f20b8e17
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420579"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Utilizar Kafka MirrorMaker com os Hubs de eventos para o Apache Kafka

Este tutorial mostra como espelhar um mediador Kafka um hub de eventos ativado do Kafka com o Kafka MirrorMaker.

   ![Kafka MirrorMaker com os Hubs de eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster do Kafka
> * Configurar o MirrorMaker do Kafka
> * Execute o MirrorMaker do Kafka

## <a name="introduction"></a>Introdução
Uma das principais considerações para aplicações de escala de cloud modernas é a capacidade de atualizar, melhorar e alterar a infraestrutura sem interromper o serviço. Este tutorial mostra como um hub de eventos habilitados no Kafka e Kafka MirrorMaker, pode integrar um pipeline existente do Kafka no Azure pelo fluxo de entrada de Kafka no serviço Hubs de eventos de "espelhamento". 

Um ponto de extremidade do Kafka de Hubs de eventos do Azure permite-lhe ligar ao Azure Event Hubs utilizando o protocolo de Kafka (ou seja, clientes de Kafka). Ao fazer alterações mínimas para um aplicativo de Kafka, pode ligar para os Hubs de eventos do Azure e Aproveite os benefícios de ecossistema do Azure. Kafka ativado os Hubs de eventos atualmente suporta as versões 1.0 e posteriores do Kafka.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Baixe](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) num arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes de Hubs de eventos é necessário para enviar e receber a partir de qualquer serviço de Hubs de eventos. Ver [criar um Kafka ativado para o Hub de eventos](event-hubs-create.md) para obter instruções sobre como obter um ponto de extremidade do Kafka de Hubs de eventos. Certifique-se copiar a cadeia de ligação de Hubs de eventos para utilização posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem um Kafka ativada a cadeia de ligação de Hubs de eventos, clonar os Hubs de eventos do Azure para o repositório do Kafka e navegue para o `mirror-maker` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster do Kafka

Utilizar o [guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as definições pretendidas (ou utilizar um cluster de Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar o MirrorMaker do Kafka

Kafka MirrorMaker permite que o "espelhamento" de um fluxo. Dada a origem e destino clusters do Kafka, MirrorMaker garante que todas as mensagens enviadas para o cluster de origem são recebidas pelos clusters de origem e de destino. Este exemplo mostra como espelhar um cluster do Kafka com um hub de eventos habilitados no Kafka de destino de origem. Este cenário pode ser utilizado para enviar dados a partir de um pipeline de Kafka existente para os Hubs de eventos sem interromper o fluxo de dados. 

Para obter mais informações sobre o Kafka MirrorMaker, consulte a [Kafka. o espelhamento/MirrorMaker guia](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dar a ele um cluster de Kafka como consumidor/origem e um hub de eventos de Kafka-ativado como o produtor/destino.

#### <a name="consumer-configuration"></a>Configuração de consumidor

Atualizar o ficheiro de configuração de consumidor `source-kafka.config`, que informa o MirrorMaker as propriedades da origem do cluster do Kafka.

##### <a name="source-kafkaconfig"></a>kafka.config de origem

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração de produtor

Agora, atualizar o ficheiro de configuração de produtor `mirror-eventhub.config`, que informa o MirrorMaker para enviar os dados duplicados (ou "espelhados") para o serviço de Hubs de eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de final do Kafka de Hubs de eventos. O serviço de Hubs de eventos exige (SASL) uma comunicação segura, que é feita definindo as últimas três propriedades na configuração do seguinte: 

##### <a name="mirror-eventhubconfig"></a>eventhub.config de espelho

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Execute o MirrorMaker do Kafka

Execute o script de Kafka MirrorMaker da raiz do diretório de Kafka com os ficheiros de configuração recém-atualizado. Certifique-se copiar os ficheiros de configuração para a raiz do diretório de Kafka, ou atualizar seus caminhos no comando seguinte.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar que eventos estão a atingir o hub de eventos habilitados no Kafka, veja as estatísticas de entrada na [portal do Azure](https://azure.microsoft.com/features/azure-portal/), ou executar um consumidor do hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados para a origem de cluster de Kafka são recebidos pelo cluster de Kafka e o Kafka espelhado ativado o serviço do hub de eventos. Ao utilizar MirrorMaker e um ponto de extremidade do Kafka de Hubs de eventos, pode migrar um pipeline de Kafka existente para o serviço de Event Hubs do Azure gerido sem alterar do cluster existente ou interromper qualquer fluxo de dados em curso.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster do Kafka
> * Configurar o MirrorMaker do Kafka
> * Execute o MirrorMaker do Kafka

Para saber mais sobre os Hubs de eventos e os Hubs de eventos para o Kafka, consulte o tópico seguinte:  

* [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Explore mais exemplos sobre os Hubs de Eventos do GitHub do Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Aprenda a transmitir para o Kafka através de Hubs de eventos ativados [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), ou [Akka fluxos](event-hubs-kafka-akka-streams-tutorial.md)
