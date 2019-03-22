---
title: Integrar com os Hubs de eventos do Azure ligar do Apache Kafka | Documentos da Microsoft
description: Este artigo fornece informações sobre como utilizar o Apache Spark com os Hubs de eventos do Azure para o Kafka.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 2ed4432aec9b833efe6b521b4452177088d21d70
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119416"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Integrar o Apache Kafka Connect nos Hubs de Eventos do Azure (Pré-visualização)
À medida que as necessidades de ingestão das empresas aumenta, também o mesmo se passa relativamente aos requisitos para ingerir de várias origens e sinks externos. O [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) proporciona uma arquitetura para ligar e importar/exportar dados de/para qualquer sistema externo, como o MySQL, o HDFS e qualquer sistema de ficheiros, através de um cluster do Kafka. Este tutorial mostra-lhe como utilizar a arquitetura Kafka Connect com os Hubs de Eventos com Kafka ativado.

Este tutorial orienta-o ao longo da integração do Kafka Connect num hub de eventos com Kafka ativado e da implementação dos conectores básicos FileStreamSource e FileStreamSink. Esta funcionalidade encontra-se em pré-visualização. Embora estes conectores não se destinem para utilização em produção, demonstram um cenário completo do Kafka Connect no qual os Hubs de Eventos do Azure funcionam como um mediador do Kafka.

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar o Kafka Connect para os Hubs de Eventos
> * Executar o Kafka Connect
> * Criar conectores

## <a name="prerequisites"></a>Pré-requisitos
Para concluir esta orientação, confirme que tem os seguintes pré-requisitos:

- Subscrição do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Versão do Kafka (versão 1.1.1, Scala versão 2.11), disponível em [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1).
- Ler o artigo de introdução dos [Event Hubs for Apache Kafka](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview) (Hubs de Eventos para Apache Kafka).

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Para obter instruções sobre como conseguir um ponto final de Kafka dos Hubs de Eventos, veja [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Criar um hub de eventos com Kafka ativado). Obtenha a cadeia de ligação dos Hubs de Eventos e o nome de domínio completamente qualificado (FQDN), para utilizar mais tarde. Para obter instruções, veja [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Obter uma cadeia de ligação dos Hubs de Eventos). 

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo
Clone o repositório dos Hubs de Eventos do Azure e navegue para a subpasta tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Configurar o Kafka Connect para os Hubs de Eventos
Ao redirecionar o débito do Kafka Connect do Kafka para os Hubs de Eventos, é necessário fazer uma reconfiguração mínima.  O exemplo seguinte, `connect-distributed.properties`, ilustra como configurar o Connect para se autenticar e comunicar com o ponto final do Kafka nos Hubs de Eventos:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Executar o Kafka Connect

Neste passo, é iniciada uma função de trabalho do Kafka Connect localmente no modo distribuído e são utilizados os Hubs de Eventos para manter o estado do cluster.

1. Guarde o ficheiro anterior, `connect-distributed.properties`, localmente.  Confirme que substitui todos os valores dentro das chavetas.
2. Navegue para a localização da versão do Kafka no computador.
4. Execute `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  Quando vir `'INFO Finished starting connectors and tasks'`, significa que a API REST da função de trabalho do Connect está pronta para interação. 

> [!NOTE]
> Os Hubs de Eventos suportam clientes Kafka que criem tópicos automaticamente. Uma rápida observação ao espaço de nomes no portal do Azure mostra que os tópicos internos da função de trabalho do Connect foram criados de forma automática.

### <a name="create-connectors"></a>Criar conectores
Esta secção mostra-lhe como criar rapidamente os conectores FileStreamSource e FileStreamSink. 

1. Crie um diretório para os ficheiros de dados de entrada e de saída.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Crie dois ficheiros. Um com dados de entrada a partir do qual o conector FileStreamSource lê e outro no qual o conector FileStreamSink escreve.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Crie um conector FileStreamSource.  Confirme que substitui as chavetas pelo caminho do seu diretório de raiz.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Depois de executar o comando acima, deverá ver o hub de eventos `connect-quickstart` na instância dos Hubs de Eventos.
4. Verifique o estado do conector de origem.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Opcionalmente, pode utilizar o [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) para verificar se os eventos chegaram ao tópico `connect-quickstart`.

5. Crie um conector FileStreamSink.  Mais uma vez, confirme que substitui as chavetas pelo caminho do seu diretório de raiz.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Verifique o estado do conector sink.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Confirme que os dados foram replicados entre os ficheiros e que são idênticos em ambos os ficheiros.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Limpeza
O Kafka Connect cria tópicos dos Hubs de Eventos para armazenar configurações, deslocamentos e estados que persistem mesmo depois de o cluster do Connect ter sido removido. A menos que essa persistência seja pretendida, recomenda-se que os tópicos sejam eliminados. Também pode ser útil eliminar o hub de eventos `connect-quickstart` que foi criado no decorrer desta orientação.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)