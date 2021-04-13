---
title: Receba eventos usando o Host do Processador de Eventos - Azure Event Hubs | Microsoft Docs
description: Este artigo descreve o Anfitrião do Processador de Eventos em Azure Event Hubs, o que simplifica a gestão de checkpointing, leasing e leitura de eventos paralelos.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d97b446993d3f0a280c1f4fadb237726ac09228a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313424"
---
# <a name="event-processor-host"></a>Anfitrião do processador de eventos
> [!NOTE]
> Este artigo aplica-se à versão antiga do Azure Event Hubs SDK. Para a versão atual do SDK, consulte [a carga de partição balance em vários casos da sua aplicação.](event-processor-balance-partition-load.md) Para aprender a migrar o seu código para a versão mais recente do SDK, consulte estes guias de migração. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Roteiro de Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs é um poderoso serviço de ingestão de telemetria que pode ser usado para transmitir milhões de eventos a baixo custo. Este artigo descreve como consumir eventos ingeridos usando o *Anfitrião do Processador de Eventos* (EPH); um agente de consumo inteligente que simplifica a gestão de leitores de checkpoint, leasing e eventos paralelos.  

A chave para a escala para os Centros de Eventos é a ideia dos consumidores divididos. Em contraste com o padrão [dos consumidores concorrentes,](/previous-versions/msp-n-p/dn568101(v=pandp.10)) o padrão de consumidor dividido permite uma escala elevada, removendo o estrangulamento da contenção e facilitando o fim do paralelismo.

## <a name="home-security-scenario"></a>Cenário de segurança doméstica

Como cenário, considere uma empresa de segurança doméstica que monitoriza 100.000 casas. A cada minuto, obtém dados de vários sensores, tais como um detetor de movimento, sensor aberto porta/janela, detetor de rutura de vidro, etc., instalado em cada casa. A empresa fornece um site para os residentes monitorizarem a atividade da sua casa em tempo real.

Cada sensor empurra os dados para um centro de eventos. O centro de eventos está configurado com 16 divisórias. Na parte final, você precisa de um mecanismo que possa ler estes eventos, consolidá-los (filtro, agregado, etc.) e despejar o agregado para uma bolha de armazenamento, que é então projetada para uma página web amigável.

## <a name="write-the-consumer-application"></a>Escreva a aplicação do consumidor

Ao conceber o consumidor num ambiente distribuído, o cenário deve tratar dos seguintes requisitos:

1. **Escala:** Crie vários consumidores, com cada consumidor a apropriar-se da leitura de algumas divisórias do Event Hubs.
2. **Equilíbrio de carga:** Aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detetor de monóxido de carbono) é adicionado a cada casa, o número de eventos aumenta. Nesse caso, o operador (um ser humano) aumenta o número de casos de consumidores. Depois, o conjunto de consumidores pode reequilibrar o número de divisórias que possuem, para partilhar a carga com os novos consumidores.
3. **Retoma sem emenda sobre falhas:** Se um consumidor (**consumidor A)** falhar (por exemplo, a máquina virtual que acolhe o consumidor despenha-se subitamente), então outros consumidores devem poder recolher as divisões detidas pelo **consumidor A** e continuar. Além disso, o ponto de continuação, chamado *de ponto de verificação* ou *compensação,* deve estar no ponto exato em que o **consumidor A** falhou, ou ligeiramente antes disso.
4. **Consumir eventos:** Embora os três pontos anteriores tratem da gestão do consumidor, deve haver um código para consumir os acontecimentos e fazer algo de útil com ele; por exemplo, agrega-o e faça o upload para o armazenamento de bolhas.

Em vez de construir a sua própria solução para isso, o Event Hubs fornece esta funcionalidade através da interface [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) e da classe [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)

## <a name="ieventprocessor-interface"></a>Interface IEventProcessor

Em primeiro lugar, as aplicações consumistas implementam a interface  [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que tem quatro métodos: [OpenAsync, CloseAsync, ProcessErrorAsync e ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Esta interface contém o código real para consumir os eventos que o Event Hubs envia. O seguinte código mostra uma simples implementação:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Em seguida, instantaneamente um [caso EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Dependendo da sobrecarga, ao criar a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) no construtor, são utilizados os seguintes parâmetros:

- **nome hospedeiro:** o nome de cada instância do consumidor. Cada instância do **EventProcessorHost** deve ter um valor único para esta variável dentro de um grupo de consumidores, por isso não código duro este valor.
- **eventHubPath:** O nome do centro de eventos.
- **nome do Grupo de Consumidores:** O Event Hubs usa **$Default** como nome do grupo de consumidores padrão, mas é uma boa prática criar um grupo de consumidores para o seu aspeto específico de processamento.
- **eventHubConnectionString:** A cadeia de ligação ao centro de eventos, que pode ser recuperada a partir do portal Azure. Esta cadeia de ligação deve ter **Permissões de escuta** no centro de eventos.
- **armazenamentoConnectionString:** A conta de armazenamento utilizada para a gestão interna de recursos.

Finalmente, os consumidores registam a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) com o serviço Event Hubs. Registar uma classe de processador de eventos com uma instância de EventProcessorHost inicia o processamento de eventos. O registo instrui o serviço Desempreimentos a esperar que a app de consumo consuma eventos de algumas das suas divisórias e que invoque o código de implementação do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) sempre que pressiona os eventos a consumir. 

> [!NOTE]
> O nome do Grupo de Consumidores é sensível a casos.  Alterações no consumidorGroupName podem resultar na leitura de todas as divisórias desde o início do fluxo.

### <a name="example"></a>Exemplo

Como exemplo, imagine que existem 5 máquinas virtuais (VMs) dedicadas a eventos consumistas, e uma simples aplicação de consola em cada VM, o que faz o consumo real funcionar. Cada aplicação de consola cria então uma instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) e regista-a com o serviço Event Hubs.

Neste cenário de exemplo, digamos que 16 divisórias são atribuídas aos 5 **casos eventProcessorHost.** Alguns **casos eventProcessorHost** podem possuir mais algumas divisórias do que outras. Para cada partição que um **eventProcessorHost** é proprietária, cria uma instância da `SimpleEventProcessor` classe. Portanto, existem 16 instâncias no `SimpleEventProcessor` geral, com uma atribuída a cada partição.

A lista que se segue resume este exemplo:

- 16 partições de Centros de Eventos.
- 5 VMs, 1 app de consumo (por exemplo, Consumer.exe) em cada VM.
- 5 casos de EPH registados, 1 em cada VM por Consumer.exe.
- 16 `SimpleEventProcessor` objetos criados pelas 5 instâncias EPH.
- 1 Consumer.exe aplicação pode conter 4 `SimpleEventProcessor` objetos, uma vez que a instância 1 EPH pode possuir 4 divisórias.

## <a name="partition-ownership-tracking"></a>Rastreio da propriedade da partição

A propriedade de uma partição a uma instância EPH (ou consumidor) é rastreada através da conta de Armazenamento Azure que é fornecida para rastreio. Pode visualizar o rastreio como uma mesa simples, da seguinte forma. Pode ver a implementação real examinando as bolhas na conta de Armazenamento fornecida:

| **Nome do grupo de consumidores** | **ID de Partição** | **Nome do anfitrião (proprietário)** | **Tempo de locação (ou propriedade) adquirido** | **Compensado em partição (ponto de verificação)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | \_Consumidor VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumidor \_ VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | \_Consumidor VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | \_Consumidor VM3 | 2018-04-15T01:22:56 | 976 |

Aqui, cada anfitrião adquire a propriedade de uma divisória por uma certa duração (a duração do arrendamento). Se um hospedeiro falhar (VM fecha), então o contrato expira. Outros anfitriões tentam obter a posse da partição, e um dos anfitriões tem sucesso. Este processo reinicia o arrendamento da partição com um novo proprietário. Desta forma, apenas um único leitor de cada vez pode ler de qualquer partição dentro de um grupo de consumidores.

## <a name="receive-messages"></a>Receber mensagens

Cada chamada para [o ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) entrega uma coleção de eventos. É sua responsabilidade lidar com estes eventos. Se quiser certificar-se de que o anfitrião do processador processa todas as mensagens pelo menos uma vez, tem de escrever o seu próprio código de reação. Mas tenha cuidado com as mensagens envenenadas.

Recomenda-se que faça as coisas relativamente rápido; isto é, fazer o mínimo de processamento possível. Em vez disso, utilize grupos de consumidores. Se precisar de escrever para armazenamento e fazer algum encaminhamento, é melhor utilizar dois grupos de consumidores e ter duas implementações [do IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que funcionam separadamente.

Em algum momento durante o seu processamento, talvez queira acompanhar o que leu e completou. Manter o registo é fundamental se tiver de reiniciar a leitura, para não voltar ao início do fluxo. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) simplifica este rastreio utilizando *pontos de verificação*. Um ponto de verificação é uma localização, ou compensação, para uma determinada partição, dentro de um determinado grupo de consumidores, altura em que está convencido de que processou as mensagens. A marcação de um ponto de verificação no **EventProcessorHost** é realizada através da chamada do método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) no objeto [PartitionContext.](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) Esta operação é feita dentro do método [ProcessEventsAsync,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) mas também pode ser feita em [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Pontos de verificação

O método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) tem duas sobrecargas: a primeira, sem parâmetros, verifica pontos de verificação para o evento mais elevado compensado dentro da coleção devolvida pelo [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Esta compensação é uma marca de "água alta"; assume que processou todos os eventos recentes quando o chama. Se utilizar este método desta forma, esteja ciente de que deve chamá-lo depois de o seu outro código de processamento de eventos ter devolvido. A segunda sobrecarga permite especificar uma instância [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) para o ponto de verificação. Este método permite-lhe utilizar um tipo diferente de marca de água para o ponto de verificação. Com esta marca de água, pode implementar uma marca de "água baixa": o evento sequenciado mais baixo que tem a certeza foi processado. Esta sobrecarga é fornecida para permitir a flexibilidade na gestão compensada.

Quando o ponto de verificação é realizado, um ficheiro JSON com informações específicas da partição (especificamente, a compensação), é escrito na conta de armazenamento fornecida no construtor ao [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Este ficheiro é continuamente atualizado. É fundamental considerar a verificação de pontos de controlo em contexto - seria imprudente pingesse todas as mensagens. A conta de armazenamento utilizada para checkpoint provavelmente não lidaria com esta carga, mas o mais importante é checkpoint de cada evento é indicativo de um padrão de mensagens em fila para o qual uma fila de ônibus de serviço pode ser uma opção melhor do que um centro de eventos. A ideia por trás do Event Hubs é que você recebe entrega "pelo menos uma vez" em grande escala. Ao tornar os seus sistemas a jusante idempotentes, é fácil recuperar de falhas ou reiniciões que resultam em que os mesmos eventos sejam recebidos várias vezes.

## <a name="thread-safety-and-processor-instances"></a>Casos de segurança e de processador de roscas

Por predefinição, [o EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) é seguro de linha e comporta-se de forma sincronizada no que diz respeito à instância do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Quando os eventos chegam para uma partição, [o ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) é chamado à instância **IEventProcessor** para essa partição e bloqueará mais chamadas para **o ProcessEventsAsync** para a partição. Mensagens e chamadas subsequentes para **ProcessEventsAsync** fazem fila nos bastidores à medida que a bomba de mensagem continua a funcionar em segundo plano em outros fios. Esta segurança do fio elimina a necessidade de coleções seguras e aumenta drasticamente o desempenho.

## <a name="shut-down-gracefully"></a>Desligue graciosamente

Por fim, [o EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) permite uma paragem limpa de todos os leitores de partição e deve ser sempre chamado ao encerrar uma instância do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se não o fizer, pode causar atrasos ao iniciar outras instâncias do **EventProcessorHost** devido à expiração do contrato de arrendamento e conflitos de época. A gestão da época é abordada em detalhe na secção [de Época](#epoch) do artigo. 

## <a name="lease-management"></a>Gestão de arrendamento
Registar uma classe de processador de eventos com uma instância de EventProcessorHost inicia o processamento de eventos. A instância anfitriã obtém arrendamentos em algumas divisórias do Event Hub, possivelmente agarrando algumas de outros casos de anfitrião, de uma forma que converge para uma distribuição uniforme de divisórias em todos os casos hospedeiros. Para cada divisória alugada, a instância anfitriã cria uma instância da classe de processador de eventos fornecida, em seguida, recebe eventos dessa partição, e passa-os para a instância do processador do evento. À medida que mais casos são adicionados e mais arrendamentos são agarrados, o EventProcessorHost acaba por equilibrar a carga entre todos os consumidores.

Como explicado anteriormente, a tabela de rastreio simplifica consideravelmente a natureza autoescalada do [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). À medida que um caso de **EventProcessorHost** começa, adquire o maior número possível de arrendamentos e começa a ler eventos. À medida que os contratos de arrendamento se aproximam da expiração, **o EventProcessorHost** tenta renová-los colocando uma reserva. Se o arrendamento estiver disponível para renovação, o processador continua a ler, mas se não estiver, o leitor está fechado e [o CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) é chamado. **CloseAsync** é uma boa altura para fazer qualquer limpeza final para essa partição.

**EventProcessorHost** inclui uma propriedade [PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Esta propriedade permite o controlo sobre a gestão do arrendamento. Descreva estas opções antes de registar a sua implementação [iEventProcessor.](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

## <a name="control-event-processor-host-options"></a>Opções de anfitrião do processador de eventos de controlo

Além disso, uma sobrecarga de [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) toma um objeto [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) como parâmetro. Utilize este parâmetro para controlar o comportamento do [EventProcessorHost.UnregisterEventProcessorAsync.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) define quatro propriedades e um evento:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): O tamanho máximo da coleção que pretende receber numa invocação do [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Este tamanho não é o mínimo, apenas o tamanho máximo. Se houver menos mensagens a serem **recebidas, o ProcessEventsAsync** executa com tantas como estavam disponíveis.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Valor utilizado pelo canal AMQP subjacente para determinar o limite superior de quantas mensagens o cliente deve receber. Este valor deve ser superior ou igual ao [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [Invocar ProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Se este parâmetro for **verdadeiro,** [o ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) é chamado quando a chamada subjacente para receber eventos em tempos de partição. Este método é útil para tomar ações baseadas no tempo durante períodos de inatividade na partição.
- [InitialOffSetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Permite definir um ponteiro de função ou expressão lambda, que é chamado para fornecer o offset inicial quando um leitor começa a ler uma partição. Sem especificar esta compensação, o leitor começa no evento mais antigo, a menos que um ficheiro JSON com uma compensação já tenha sido guardado na conta de armazenamento fornecida ao construtor [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Este método é útil quando pretende alterar o comportamento da startup do leitor. Quando este método é invocado, o parâmetro do objeto contém o ID de partição para o qual o leitor está a ser iniciado.
- [ExcepçõesReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Permite-lhe receber a notificação de quaisquer exceções subjacentes que ocorram no [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se as coisas não estão funcionando como você espera, este evento é um bom lugar para começar a procurar.

## <a name="epoch"></a>Época

Eis como funciona a época de receção:

### <a name="with-epoch"></a>Com Época
A Época é um identificador único (valor de época) que o serviço utiliza, para impor a propriedade de partição/arrendamento. Cria um recetor baseado em Epoch utilizando o método [CreateEpochReceiver.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) Este método cria um recetor baseado em Epoch. O recetor é criado para uma partição específica do centro de eventos do grupo de consumidores especificado.

A funcionalidade de época proporciona aos utilizadores a capacidade de garantir que existe apenas um recetor num grupo de consumidores a qualquer momento, com as seguintes regras:

- Se não existir um recetor existente num grupo de consumidores, o utilizador pode criar um recetor com qualquer valor de época.
- Se houver um recetor com um valor de época e1 e um novo recetor é criado com um valor de época e2 onde e1 <= e2, o recetor com e1 será desligado automaticamente, o recetor com e2 é criado com sucesso.
- Se houver um recetor com um valor de época e1 e um novo recetor é criado com um valor de época e2 onde e1 > e2, então a criação de e2 com falha com o erro: Um recetor com época e1 já existe.

### <a name="no-epoch"></a>Sem Época
Cria um recetor não baseado em Epoch utilizando o método [CreateReceiver.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) 

Existem alguns cenários no processamento de fluxos onde os utilizadores gostariam de criar vários recetores num único grupo de consumidores. Para apoiar estes cenários, temos a capacidade de criar um recetor sem época e neste caso permitimos até 5 recetores simultâneos no grupo de consumidores.

### <a name="mixed-mode"></a>Modo Misto
Não recomendamos a utilização da aplicação quando se cria um recetor com época e depois muda para nenhuma época ou vice-versa no mesmo grupo de consumidores. No entanto, quando este comportamento ocorre, o serviço trata-o utilizando as seguintes regras:

- Se houver um recetor já criado com a época e1 e estiver a receber ativamente eventos e for criado um novo recetor sem época, a criação de um novo recetor falhará. Os recetores da época têm sempre precedência no sistema.
- Se já havia um recetor criado com a época e1 e foi desligado, e um novo recetor é criado sem nenhuma época numa nova MessagingFactory, a criação de um novo recetor será bem sucedida. Há uma ressalva aqui que o nosso sistema irá detetar a "desconexão do recetor" após ~10 minutos.
- Se houver um ou mais recetores criados sem época, e um novo recetor for criado com época e1, todos os recetores antigos são desligados.


> [!NOTE]
> Recomendamos a utilização de diferentes grupos de consumidores para aplicações que utilizem épocas e para aqueles que não usam épocas para evitar erros. 


## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o Anfitrião do Processador de Eventos, consulte os seguintes artigos para saber mais sobre os Centros de Eventos:

- Introdução ao Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.yml)
* [Amostras de Centros de Eventos no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
