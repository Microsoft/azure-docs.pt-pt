---
title: Padrões de tarefa de replicação de eventos - Azure Event Hubs Microsoft Docs
description: Este artigo fornece orientações detalhadas para implementar padrões específicos de tarefas de replicação de eventos
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 370c0f2d5c5c591668aa2dadf0512760a4a9b2f5
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663648"
---
# <a name="event-replication-tasks-patterns"></a>Padrões de tarefas de replicação de eventos

A [visão geral da federação](event-hubs-federation-overview.md) e as [funções do replicador](event-hubs-federation-replicator-functions.md) explicam a lógica e os elementos básicos das tarefas de replicação, e recomenda-se que se familiarize com elas antes de continuar com este artigo.

Neste artigo, detalhamos a orientação de implementação de vários dos padrões destacados na secção de visão geral.

## <a name="replication"></a>Replicação

O padrão de replicação copia eventos de um Event Hub para o outro, ou de um Event Hub para outro destino como uma fila de ônibus de serviço. Os eventos são reencaminhados sem fazer qualquer modificação na carga útil do evento.

A implementação deste padrão é coberta pela replicação do [Evento entre os Centros de Eventos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) e a [replicação do Evento entre os Centros de Eventos e as amostras de Service Bus.](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

### <a name="streams-and-order-preservation"></a>Riachos e preservação da ordem

A replicação, seja através de Azure Functions ou Azure Stream Analytics, não tem como objetivo assegurar a criação de clones exatos 1:1 de um Centro de Eventos de origem num Centro de Eventos alvo, mas foca-se em preservar a ordem relativa dos eventos onde a aplicação o necessita. A aplicação comunica-o através do agrupamento de eventos relacionados com a mesma chave de partição e [o Event Hubs organiza mensagens com a mesma chave de partição sequencialmente na mesma partição.](event-hubs-features.md#partitions)

> [!IMPORTANT] 
> A informação "offset" é única para cada Event Hub e as compensações para os mesmos eventos diferirão em todas as instâncias do Event Hub. Para localizar uma posição num fluxo de eventos copiado, utilize compensações baseadas no tempo e consulte os [metadados de serviço propagados](#service-assigned-metadata).
>
> Compensações baseadas no tempo iniciam o seu recetor num determinado ponto do tempo:
> - *EventPosition.FromStart()* - Leia todos os dados retidos novamente.
> - *EventPosition.FromEnd()* - Leia todos os novos dados a partir do momento da ligação.
> - *EventPosition.FromEnqueuedTime (dataTime)* - Todos os dados a partir de uma determinada data e hora.
>
> No EventProcessor, define a posição através do InitialOffsetProvider nas Opições de Processo de Eventos. Com as outras APIs recetores, a posição é passada através do construtor. 


Os ajudantes de função de replicação pré-construídos [fornecidos como amostras](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) que são usadas na orientação baseada em Funções Azure garantem que os fluxos de eventos com a mesma chave de partição recuperada de uma partição de origem são submetidos no Centro de Eventos alvo como um lote no fluxo original e com a mesma chave de partição.

Se a contagem de divisórias do centro de eventos de origem e alvo for idêntica, todos os fluxos no alvo irão mapear para as mesmas divisórias que fizeram na fonte.
Se a contagem de divisórias for diferente, o que importa em alguns dos padrões adicionais descritos no seguinte, o mapeamento será diferente, mas os fluxos são sempre mantidos juntos e em ordem.

A ordem relativa dos acontecimentos pertencentes a diferentes fluxos ou eventos independentes sem uma chave de partição numa partição-alvo pode sempre diferir da partição de origem.

### <a name="service-assigned-metadata"></a>Metadados atribuídos pelo serviço

Os metadados atribuídos pelo serviço de um evento obtido a partir do Centro de Eventos de origem, o tempo de enques, o número de sequência e offset originais, são substituídos por novos valores atribuídos ao serviço no Centro de Eventos alvo, mas com as [funções de ajudante,](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)as tarefas de replicação que são fornecidas nas nossas amostras, os valores originais são preservados nas propriedades do utilizador: `repl-enqueue-time` (cadeia ISO8601), `repl-sequence` `repl-offset` .

Essas propriedades são de tipo string e contêm o valor stringified das respetivas propriedades originais. Se o evento for reencaminhado várias vezes, os metadados atribuídos ao serviço da fonte imediata são anexados às propriedades já existentes, com valores separados por pontos-e-vírgula.

### <a name="failover"></a>Ativação pós-falha

Se estiver a utilizar a replicação para fins de recuperação de desastres, para proteger contra eventos de disponibilidade regional no serviço Event Hubs, ou contra interrupções de rede, qualquer cenário de falha deste tipo exigirá a realização de uma falha de um Event Hub para o próximo, dizendo aos produtores e/ou consumidores para usarem o ponto final secundário.

Para todos os cenários de failover, presume-se que os elementos necessários dos espaços de nome são estruturalmente idênticos, o que significa que os Centros de Eventos e grupos de consumidores são nomeados de forma idêntica e que as regras de acesso partilhado e/ou regras de controlo de acesso baseadas em funções são criadas da mesma forma. Pode criar (e atualizar) um espaço de nome secundário seguindo as [orientações para mover espaços de nome](move-across-regions.md) e omitir o passo de limpeza.

Para forçar os produtores e consumidores a mudar, é necessário disponibilizar a informação sobre qual o espaço de identificação a utilizar para a procura num local de fácil acesso e atualização. Se os produtores ou consumidores encontrarem erros frequentes ou persistentes, devem consultar esse local e ajustar a sua configuração. Existem inúmeras formas de partilhar essa configuração, mas apontamos duas no seguinte: DNS e partilhas de ficheiros.

#### <a name="dns-based-failover-configuration"></a>Configuração de failover baseada em DNS

Uma abordagem de candidato é manter a informação nos registos DNS SRV num DNS que controla e apontar para os respetivos pontos finais do Event Hub. 

> [!IMPORTANT] 
> Tenha em atenção que os Centros de Eventos não permitem que os seus pontos finais sejam diretamente aliasados com registos CNAME, o que significa que utilizará o DNS como um mecanismo de procura resiliente para endereços de ponto final e não para resolver diretamente as informações do endereço IP.

Assuma que é dono do domínio `example.com` e, para a sua aplicação, de uma `test.example.com` zona. Para dois Centros de Eventos alternativos, irá agora criar mais duas zonas aninhadas e um recorde DEV em cada uma.

Os registos SRV são, seguindo convenção comum, prefixados `_azure_eventhubs._amqp` e detêm dois registos de ponto final: um para AMQP-over-TLS na porta 5671 e outro para AMQP-over-WebSockets na porta 443, ambos apontando para o ponto final dos Centros de Eventos do espaço de nome correspondente à zona.

| Zona                   | Registo SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Na zona da sua aplicação, irá então criar uma entrada CNAME que aponta para a zona subordinada correspondente ao seu Principal Centro de Eventos:

| Registo CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Utilizando um cliente DNS que permite consultar explicitamente os registos CNAME e SRV (os clientes incorporados da Java e .NET apenas permitem uma simples resolução de nomes para endereços IP), pode então resolver o ponto final pretendido. Com [DnsClient.NET,](https://dnsclient.michaco.net/)por exemplo, a função de procuração é:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

A função devolve o nome de anfitrião-alvo registado para a porta 5671 da zona atualmente aliasada com o CNAME, como mostrado acima.

A realização de uma falha requer a edição do registo CNAME e apontá-lo para a zona alternativa.

A vantagem de usar DNS, e especificamente [Azure DNS,](../dns/dns-overview.md)é que a informação do Azure DNS é globalmente replicada e, portanto, resiliente contra falhas de uma região única.

Este procedimento é semelhante ao funcionamento do [Event Hubs Geo-DR,](event-hubs-geo-dr.md) mas totalmente sob o seu próprio controlo e também funciona com cenários ativos/ativos.

#### <a name="file-share-based-failover-configuration"></a>Configuração de falha baseada em partilha de ficheiros

A alternativa mais simples de usar DNS para partilhar informações de ponto final é colocar o nome do ponto final primário num ficheiro de texto simples e servir o ficheiro a partir de uma infraestrutura que é robusta contra falhas e ainda permite atualizações.

Se já executou uma infraestrutura de web site altamente disponível com disponibilidade global e replicação de conteúdos, adicionando tal ficheiro e republicar o ficheiro se for necessário um switch.

> [!CAUTION]
> Só deve publicar o nome do ponto final desta forma, e não uma cadeia de conexão completa, incluindo segredos.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Considerações adicionais por falta de consumidores

Para os consumidores do Event Hub, as considerações adicionais para a estratégia de failover dependem das necessidades do processador do evento.

Se houver uma catástrofe que exija a reconstrução de um sistema, incluindo bases de dados, a partir de dados de backup, e as bases de dados são alimentadas diretamente ou através do processamento intermédio dos eventos realizados no Event Hub, irá restaurar a cópia de segurança e depois pretender começar a reproduzir eventos no sistema a partir do momento em que a cópia de segurança da base de dados foi criada e não a partir do momento em que o sistema original foi destruído.

Se uma falha afetar apenas uma fatia de um sistema ou apenas um único Event Hub, que se tornou inacessível, provavelmente irá querer continuar a processar eventos a partir da mesma posição onde o processamento foi interrompido.

Para realizar qualquer um dos cenários e utilizar o processador de eventos do respetivo Azure SDK, [irá criar uma nova loja de pontos de verificação](event-processor-balance-partition-load.md#checkpointing) e fornecer uma posição inicial de partição, com base no tempo _que_ pretende retomar o processamento.

Se ainda tiver acesso à loja de checkpoint do Centro de Eventos de que está a mudar, os [metadados propagados](#service-assigned-metadata) acima discutidos irão ajudá-lo a saltar eventos que já foram tratados e retomar precisamente de onde acabou.

## <a name="merge"></a>Intercalar

O padrão de fusão tem uma ou mais tarefas de replicação que apontam para um alvo, possivelmente em simultâneo com os produtores regulares que também enviam eventos para o mesmo alvo.

As variações destes patters são:

- Duas ou mais funções de replicação simultaneamente adquirem eventos de fontes separadas e enviam-nos para o mesmo alvo.
- Mais uma função de replicação que adquire eventos de uma fonte enquanto o alvo também é usado diretamente pelos produtores.
- O padrão anterior, mas espelhado entre dois ou mais Centros de Eventos, resultando nesses Centros de Eventos contendo os mesmos fluxos, independentemente do local onde os eventos são produzidos.

As duas primeiras variações de padrões são triviais e não diferem das tarefas de replicação simples.

O último cenário requer que os eventos já replicados sejam replicados novamente. A técnica é demonstrada e explicada na amostra [EventHubToEventHubMerge.](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/main/code/EventHubToEventHubMerge)

## <a name="editor"></a>Editor

O padrão do editor baseia-se no padrão [de replicação,](#replication) mas as mensagens são modificadas antes de serem reencaminhadas. 

Exemplos para tais modificações são:

- **_Transcoding_** - Se o conteúdo do evento (também designado por "corpo" ou "carga útil") chegar da fonte codificada utilizando o formato _Apache Avro_ ou algum formato de serialização proprietário, mas a expectativa do sistema que possui o alvo é que o conteúdo seja codificado _JSON,_ uma tarefa de replicação transcodificante irá primeiro deserializar a carga útil do _Apache Avro_ para um gráfico de objeto na memória e, em seguida, serializar esse gráfico no formato _JSON_ para o evento que está sendo encaminhado. A transcoding também inclui tarefas de compressão e descompressão de **conteúdos.**
- **_Transformação_** - Os eventos que contenham dados estruturados podem exigir a reformulação desses dados para facilitar o consumo por parte dos consumidores a jusante. Isto pode envolver trabalho como achatamento de estruturas aninhadas, poda de elementos de dados extraérs, ou remodelar a carga útil para exatamente encaixar num determinado esquema.
- **_Batching_** - Os eventos podem ser recebidos em lotes (múltiplos eventos numa única transferência) de uma fonte, mas têm de ser encaminhados singly para um alvo, ou vice-versa. Uma tarefa pode, portanto, transmitir múltiplos eventos com base numa única transferência de eventos de entrada ou agregar um conjunto de eventos que são depois transferidos em conjunto.
- **_Validação_** - Os dados do evento de fontes externas precisam frequentemente de ser verificados se estão em conformidade com um conjunto de regras antes de poderem ser reencaminhados. As regras podem ser expressas com recurso a esquemas ou códigos. Os acontecimentos que não estejam em conformidade podem ser eliminados, com a questão anotado em registos, ou podem ser encaminhados para um destino-alvo especial para os tratar mais.
- **_Enriquecimento_** - Os dados do evento provenientes de algumas fontes podem exigir enriquecimento com mais contexto para que seja utilizável em sistemas-alvo. Isto pode implicar procurar dados de referência e incorporar esses dados com o evento, ou adicionar informações sobre a fonte que é conhecida da tarefa de replicação, mas não contida nos eventos.
- **_Filtragem_** - Alguns eventos que chegam de uma fonte podem ter de ser retidos do alvo com base em alguma regra. Um filtro testa o evento contra uma regra e deixa cair o evento se o evento não corresponder à regra. Filtrar eventos duplicados observando determinados critérios e deixando cair eventos subsequentes com os mesmos valores é uma forma de filtragem.
- **_Criptografia_** - Uma tarefa de replicação pode ter de desencriptar os conteúdos que chegam da fonte e/ou encriptar o conteúdo reencaminhado para um alvo, e/ou pode ter de verificar a integridade do conteúdo e dos metadados em relação a uma assinatura transportada no evento, ou anexar tal assinatura.
- **_Attestation_** - Uma tarefa de replicação pode anexar metadados, potencialmente protegidos por uma assinatura digital, a um evento que ateste que o evento foi recebido através de um canal específico ou num momento específico.
- **_Chaining_** - Uma tarefa de replicação pode aplicar assinaturas em fluxos de eventos de modo a que a integridade do fluxo seja protegida e eventos em falta possam ser detetados.

Os padrões de transformação, lote e enriquecimento são geralmente melhor implementados com empregos [Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md) 

Todos estes padrões podem ser implementados usando as Funções Azure, utilizando o [Event Hubs Trigger](../azure-functions/functions-bindings-event-hubs-trigger.md) para adquirir eventos e a [ligação](../azure-functions/functions-bindings-event-hubs-output.md) de saída do Event Hub para os entregar.

## <a name="routing"></a>Encaminhamento

O padrão de encaminhamento baseia-se no padrão de [replicação,](#replication) mas em vez de ter uma fonte e um alvo, a tarefa de replicação tem vários alvos, ilustrados aqui em C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

A função de encaminhamento considerará os metadados de mensagens e/ou a carga útil da mensagem e, em seguida, escolherá um dos destinos disponíveis para enviar.

No Azure Stream Analytics, pode conseguir o mesmo com a definição de múltiplas saídas e, em seguida, executar uma consulta por saída.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projeção de registo

O padrão de projeção de registo achata o fluxo do evento numa base de dados indexada, com os eventos a tornarem-se registos na base de dados. Tipicamente, os eventos são adicionados à mesma coleção ou mesa, e a chave de partição do Event Hub torna-se parte da chave primária procurando tornar o disco único.

A projeção de registos pode produzir um historiador da série de tempo dos dados do seu evento ou uma visão compactada, pelo que apenas o evento mais recente é retido para cada chave de partição. A forma da base de dados-alvo é, em última análise, da sua mente e das necessidades da sua aplicação. Este padrão também é referido como "sourcing de eventos".

> [!TIP]
> Pode facilmente criar projeções de registo na [Base de Dados Azure SQL](../stream-analytics/sql-database-output.md) e [no Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) em Azure Stream Analytics e deve preferir essa opção.

A função Azure seguinte projeta o conteúdo de um Centro de Eventos compactado numa coleção Azure CosmosDB.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- [Aplicações de replicador de eventos em Funções Azure][1]
- [Replicação de eventos entre centros de eventos][2]
- [Replicando eventos para a Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
