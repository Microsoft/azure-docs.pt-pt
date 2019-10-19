---
title: Usar Apache Kafka MirrorMaker-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar Kafka MirrorMaker para espelhar um cluster Kafka em hubs de AzureEvent.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555100"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Usar Kafka MirrorMaker com hubs de eventos para Apache Kafka

Este tutorial mostra como espelhar um agente do Kafka em um hub de eventos habilitado para Kafka usando Kafka MirrorMaker.

   ![Kafka MirrorMaker com hubs de eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar Kafka MirrorMaker
> * Executar Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Uma das principais considerações para aplicativos de escala de nuvem modernos é a capacidade de atualizar, melhorar e alterar a infraestrutura sem interromper o serviço. Este tutorial mostra como um hub de eventos habilitado para Kafka e o Kafka MirrorMaker podem integrar um pipeline Kafka existente ao Azure por meio do "espelhamento" do fluxo de entrada do Kafka no serviço de hubs de eventos. 

Um ponto de extremidade Kafka dos hubs de eventos do Azure permite que você se conecte aos hubs de eventos do Azure usando o protocolo Kafka (ou seja, clientes Kafka). Fazendo alterações mínimas em um aplicativo Kafka, você pode se conectar aos hubs de eventos do Azure e aproveitar os benefícios do ecossistema do Azure. Os hubs de eventos habilitados para Kafka atualmente dão suporte às versões 1,0 e posteriores do Kafka.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Para obter instruções sobre como conseguir um ponto final de Kafka dos Hubs de Eventos, veja [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Criar um hub de eventos com Kafka ativado). Certifique-se de copiar a cadeia de conexão dos hubs de eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de hubs de eventos habilitada para Kafka, clone os hubs de eventos do Azure para o repositório Kafka e navegue até a subpasta `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster Kafka

Use o [Guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as configurações desejadas (ou use um cluster Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar Kafka MirrorMaker

Kafka MirrorMaker habilita o "espelhamento" de um fluxo. Determinados clusters Kafka de origem e destino, o MirrorMaker garante que todas as mensagens enviadas para o cluster de origem sejam recebidas pelos clusters de origem e de destino. Este exemplo mostra como espelhar um cluster Kafka de origem com um hub de eventos de destino habilitado para Kafka. Esse cenário pode ser usado para enviar dados de um pipeline Kafka existente para os hubs de eventos sem interromper o fluxo de dados. 

Para obter informações mais detalhadas sobre o Kafka MirrorMaker, consulte o [Guia de espelhamento/MirrorMaker do Kafka](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dê a ele um cluster Kafka como seu consumidor/origem e um hub de eventos habilitado para Kafka como seu produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o arquivo de configuração do consumidor `source-kafka.config`, que informa MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>origem-Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora, atualize o arquivo de configuração do produtor `mirror-eventhub.config`, que diz ao MirrorMaker para enviar os dados duplicados (ou "espelhados") para o serviço de hubs de eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de extremidade Kafka dos hubs de eventos. O serviço de hubs de eventos requer comunicação segura (SASL), que é obtida definindo as três últimas Propriedades na seguinte configuração: 

##### <a name="mirror-eventhubconfig"></a>espelho-eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Executar Kafka MirrorMaker

Execute o script Kafka MirrorMaker do diretório raiz Kafka usando os arquivos de configuração recém-atualizados. Certifique-se de copiar os arquivos de configuração para o diretório raiz Kafka ou atualizar seus caminhos no comando a seguir.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar se os eventos estão atingindo o Hub de eventos habilitado para Kafka, consulte as estatísticas de entrada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/)ou execute um consumidor no Hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados para o cluster Kafka de origem são recebidos pelo cluster Kafka e pelo serviço Hub de eventos habilitado para Kafka espelhado. Usando o MirrorMaker e um ponto de extremidade Kafka de hubs de eventos, você pode migrar um pipeline Kafka existente para o serviço de hubs de eventos do Azure gerenciado sem alterar o cluster existente ou interromper qualquer fluxo de dados em andamento.

## <a name="samples"></a>Exemplos
Consulte os seguintes exemplos no GitHub:

- [Código de exemplo para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Hubs de eventos do Azure Kafka MirrorMaker em execução em uma instância de contêiner do Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar Kafka MirrorMaker
> * Executar Kafka MirrorMaker

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
