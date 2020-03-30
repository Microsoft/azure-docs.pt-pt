---
title: Receba eventos utilizando o Anfitrião do Processador de Eventos - Hubs de Eventos Azure [ Microsoft Docs
description: Este artigo descreve o Anfitrião do Processador de Eventos em Azure Event Hubs, que simplifica a gestão de eventos de checkpointing, leasing e leitura ion paralelo.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372234"
---
# <a name="event-processor-host"></a>Anfitrião do processador de eventos
> [!NOTE]
> Este artigo aplica-se à versão antiga do Azure Event Hubs SDK. Para aprender a migrar o seu código para a versão mais recente do SDK, consulte estes guias de migração. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Pitão](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Roteiro de Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Além disso, consulte a carga de [partição Balance em várias instâncias da sua aplicação](event-processor-balance-partition-load.md).

O Azure Event Hubs é um poderoso serviço de ingestão de telemetria que pode ser usado para transmitir milhões de eventos a baixo custo. Este artigo descreve como consumir eventos ingeridos usando o Anfitrião do Processador de *Eventos* (EPH); um agente de consumo inteligente que simplifica a gestão de leitores de checkpointing, leasing e eventos paralelos.  

A chave para escalar para os Centros de Eventos é a ideia de consumidores divididos. Em contraste com o padrão dos [consumidores concorrentes,](https://msdn.microsoft.com/library/dn568101.aspx) o padrão de consumo dividido permite uma escala elevada, removendo o estrangulamento da contenção e facilitando o fim do paralelismo.

## <a name="home-security-scenario"></a>Cenário de segurança doméstica

Como cenário de exemplo, considere uma empresa de segurança doméstica que monitorize 100.000 casas. A cada minuto, obtém dados de vários sensores, tais como um detetor de movimento, sensor aberto porta/janela, detetor de rutura de vidro, etc., instalado em cada casa. A empresa fornece um site para os residentes monitorizarem a atividade da sua casa em tempo real.

Cada sensor empurra dados para um centro de eventos. O centro do evento está configurado com 16 divisórias. No lado consumista, é necessário um mecanismo que possa ler estes eventos, consolidá-los (filtro, agregado, etc.) e despejar o agregado numa bolha de armazenamento, que é então projetada para uma página web que seja fácil de utilizar.

## <a name="write-the-consumer-application"></a>Escreva a aplicação do consumidor

Ao conceber o consumidor num ambiente distribuído, o cenário deve tratar dos seguintes requisitos:

1. **Escala:** Crie vários consumidores, com cada consumidor a apropriar-se da leitura de algumas divisórias do Event Hubs.
2. **Balanço de carga:** Aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detetor de monóxido de carbono) é adicionado a cada casa, o número de eventos aumenta. Nesse caso, o operador (um ser humano) aumenta o número de casos de consumo. Em seguida, o conjunto de consumidores pode reequilibrar o número de divisórias que possuem, para partilhar a carga com os consumidores recém-adicionados.
3. **Currículo sem emenda sobre falhas:** Se um consumidor (**consumidor A**) falhar (por exemplo, a máquina virtual que alberga o consumidor de repente se despenha), então outros consumidores devem poder recolher as divisórias detidas pelo **consumidor A** e continuar. Além disso, o ponto de continuação, denominado ponto de *verificação* ou *compensação,* deve estar no ponto exato em que o **consumidor A** falhou, ou ligeiramente antes disso.
4. **Consumir eventos:** Embora os três pontos anteriores se desloquem à gestão do consumidor, deve haver código para consumir os acontecimentos e fazer algo de útil com ele; por exemplo, agrega-o e carregá-lo para o armazenamento de bolhas.

Em vez de construir a sua própria solução para isso, o Event Hubs fornece esta funcionalidade através da interface [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) e da classe [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)

## <a name="ieventprocessor-interface"></a>Interface IEventProcessador

Em primeiro lugar, aplicações consumistas implementam a interface [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que tem quatro métodos: [OpenAsync, CloseAsync, ProcessErrorAsync e ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Esta interface contém o código real para consumir os eventos que o Event Hubs envia. O seguinte código mostra uma implementação simples:

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

Em seguida, instantaneamente uma instância de [Anfitrião](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) de Processador de Eventos. Dependendo da sobrecarga, ao criar a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) no construtor, são utilizados os seguintes parâmetros:

- **nome anfitrião:** o nome de cada instância de consumo. Cada instância de **EventProcessorHost** deve ter um valor único para esta variável dentro de um grupo de consumidores, por isso não código duro este valor.
- **eventoHubPath:** O nome do centro de eventos.
- **consumerGroupName:** O Event Hubs usa **$Default** como nome do grupo de consumidores padrão, mas é uma boa prática criar um grupo de consumidores para o seu aspeto específico de processamento.
- **eventoHubConnectionString:** A cadeia de ligação ao centro do evento, que pode ser recuperada do portal Azure. Esta cadeia de ligação deve ter permissões **de escuta** no centro do evento.
- **armazenamentoConnectionString:** A conta de armazenamento utilizada para a gestão interna de recursos.

Finalmente, os consumidores registam a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) com o serviço Event Hubs. Registar uma classe de processador de eventos com uma instância de EventProcessorHost inicia o processamento de eventos. O registo instrui o serviço Event Hubs a esperar que a aplicação de consumo consuma eventos a partir de algumas das suas divisórias e invoque o código de implementação do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) sempre que pressione eventos a consumir. 


### <a name="example"></a>Exemplo

Como exemplo, imagine que existem 5 máquinas virtuais (VMs) dedicadas a consumir eventos, e uma simples aplicação de consola em cada VM, que faz o consumo real funcionar. Cada aplicação de consola cria então uma instância [do EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) e regista-a com o serviço Event Hubs.

Neste cenário de exemplo, digamos que 16 divisórias são atribuídas às 5 instâncias **do EventProcessorHost.** Alguns casos de Anfitriões de Processadores de **Eventos** podem possuir mais algumas divisórias do que outras. Para cada partição que uma instância do **EventProcessorHost** possui, cria uma instância da `SimpleEventProcessor` classe. Portanto, existem 16 casos de `SimpleEventProcessor` geral, com um atribuído a cada partição.

A seguinte lista resume este exemplo:

- 16 divisórias de Centros de Eventos.
- 5 VMs, 1 aplicação de consumo (por exemplo, Consumer.exe) em cada VM.
- 5 instâncias EPH registadas, 1 em cada VM por Consumer.exe.
- 16 `SimpleEventProcessor` objetos criados pelas 5 instâncias EPH.
- 1 A aplicação Consumer.exe pode conter 4 `SimpleEventProcessor` objetos, uma vez que a instância 1 EPH pode possuir 4 divisórias.

## <a name="partition-ownership-tracking"></a>Rastreio de propriedade de partição

A propriedade de uma partição para uma instância EPH (ou um consumidor) é rastreada através da conta de Armazenamento Azure que está prevista para o rastreio. Pode visualizar o rastreio como uma tabela simples, da seguinte forma. Pode ver a implementação real examinando as bolhas sob a conta de Armazenamento fornecida:

| **Nome do grupo de consumidores** | **ID de Partição** | **Nome anfitrião (proprietário)** | **Tempo de arrendamento (ou propriedade) adquirido** | **Offset na partilha (ponto de verificação)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | VM3 do consumidor\_ | 2018-04-15t01:23:45 | 156 |
| $Default | 1 | Consumidor\_VM4 | 2018-04-15t01:22:13 | 734 |
| $Default | 2 | VM0 do consumidor\_ | 2018-04-15t01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | VM3 do consumidor\_ | 2018-04-15t01:22:56 | 976 |

Aqui, cada anfitrião adquire a propriedade de uma partição por uma certa duração (a duração do arrendamento). Se um hospedeiro falhar (a VM encerra), então o contrato expira. Outros anfitriões tentam obter a posse da partição, e um dos anfitriões tem sucesso. Este processo repõe o arrendamento na partição com um novo proprietário. Desta forma, apenas um único leitor de cada vez pode ler de qualquer partição dentro de um grupo de consumidores.

## <a name="receive-messages"></a>Receber mensagens

Cada chamada para [processEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) entrega uma coleção de eventos. É da sua responsabilidade lidar com estes eventos. Se quiser certificar-se de que o anfitrião do processador processa todas as mensagens pelo menos uma vez, tem de escrever o seu próprio código de reexperimentação. Mas tenha cuidado com mensagens envenenadas.

Recomenda-se que faça as coisas relativamente rápido; isto é, fazer o mínimo de processamento possível. Em vez disso, utilize grupos de consumidores. Se precisa de escrever para armazenar e fazer algum encaminhamento, é melhor utilizar dois grupos de consumidores e ter duas implementações [do IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que funcionam separadamente.

Em algum momento durante o seu processamento, talvez queira acompanhar o que leu e completou. Manter o rasto é fundamental se tiver de recomeçar a leitura, para não voltar ao início do fluxo. [O EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) simplifica este rastreio utilizando pontos de *verificação*. Um checkpoint é um local, ou compensado, para uma determinada partição, dentro de um determinado grupo de consumidores, altura em que está convencido de que processou as mensagens. A marcação de um checkpoint no **EventProcessorHost** é realizada ligando para o método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) no objeto [PartitionContext.](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) Esta operação é feita dentro do método [ProcessEventsAsync,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) mas também pode ser feita em [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Ponto de verificação

O método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) tem duas sobrecargas: a primeira, sem parâmetros, pontos de verificação para o evento mais alto compensado dentro da coleção devolvida pela [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Esta compensação é uma marca de "alta água"; assume que processou todos os eventos recentes quando lhe chama. Se utilizar este método desta forma, esteja ciente de que deverá chamá-lo depois de o seu outro código de processamento de eventos ter retornado. A segunda sobrecarga permite especificar uma instância [do EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) para o checkpoint. Este método permite-lhe utilizar um tipo diferente de marca de água para o checkpoint. Com esta marca de água, pode implementar uma marca de "água baixa": o evento de sequência mais baixo que tem a certeza foi processado. Esta sobrecarga é fornecida para permitir flexibilidade na gestão compensada.

Quando o ponto de verificação é realizado, um ficheiro JSON com informações específicas da partilha (especificamente, a compensação), é escrito na conta de armazenamento fornecida no construtor para [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Este ficheiro é continuamente atualizado. É fundamental considerar o controlo do controlo no contexto - seria imprudente fazer o checkpoint de todas as mensagens. A conta de armazenamento utilizada para o checkpoint provavelmente não lidaria com esta carga, mas, mais importante ainda, o checkpoint de cada evento é indicativo de um padrão de mensagens em fila para o qual uma fila de ônibus de serviço pode ser uma opção melhor do que um centro de eventos. A ideia por trás do Event Hubs é que recebes "pelo menos uma vez" a entrega em grande escala. Ao tornar os seus sistemas a jusante idempotentes, é fácil recuperar de falhas ou recomeços que resultam em que os mesmos eventos sejam recebidos várias vezes.

## <a name="thread-safety-and-processor-instances"></a>Instâncias de segurança e processador de fios

Por padrão, o [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) é seguro de fio e comporta-se de forma sincronizada no que diz respeito à instância do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Quando os eventos chegam para uma partição, [processEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) é chamado na instância **iEventProcessor** para essa partição e bloqueará novas chamadas para **ProcessEventsAsync** para a partição. Mensagens e chamadas subsequentes para a fila **ProcessEventsAsync** nos bastidores à medida que a bomba de mensagem continua a correr em segundo plano em outros fios. Esta segurança de rosca elimina a necessidade de coleções seguras e aumenta drasticamente o desempenho.

## <a name="shut-down-gracefully"></a>Desligar graciosamente

Por fim, [o EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) permite um encerramento limpo de todos os leitores de partição e deve ser sempre chamado ao desligar uma instância de [Anfitrião](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)do Processador de Eventos . Se não o fizer, pode causar atrasos ao iniciar outros casos de **EventProcessorHost** devido à expiração do aluguer e conflitos de época. A gestão da época é coberta em pormenor na secção [Época](#epoch) do artigo. 

## <a name="lease-management"></a>Gestão de arrendamento
Registar uma classe de processador de eventos com uma instância de EventProcessorHost inicia o processamento de eventos. A instância anfitriã obtém arrendamentos em algumas divisórias do Event Hub, possivelmente agarrando algumas de outras instâncias hospedeiras, de uma forma que converge numa distribuição uniforme de divisórias em todos os casos de hospedeiros. Para cada partição alugada, a instância anfitriã cria uma instância da classe de processador de eventos fornecida, recebe então eventos dessa divisória, e passa-os para a instância do processador do evento. À medida que mais casos são adicionados e mais arrendamentos são agarrados, o EventProcessorHost acaba por equilibrar a carga entre todos os consumidores.

Como explicado anteriormente, a tabela de rastreio simplifica muito a natureza de escala automática do [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Como um caso de **EventProcessorHost** começa, ele adquire o maior número possível de arrendamentos, e começa a ler eventos. À medida que os contratos de arrendamento estão perto da expiração, o **EventProcessorHost** tenta renová-los colocando uma reserva. Se o contrato de arrendamento estiver disponível para renovação, o processador continua a ler, mas se não estiver, o leitor está fechado e o [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) é chamado. **CloseAsync** é uma boa altura para fazer qualquer limpeza final para essa partição.

**O Anfitrião** do Processador de Eventos inclui uma propriedade [PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Esta propriedade permite o controlo sobre a gestão do arrendamento. Detete estas opções antes de registar a implementação do [IEventProcessor.](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

## <a name="control-event-processor-host-options"></a>Opções de anfitrião do processador de eventos de controlo

Além disso, uma sobrecarga do [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) leva um objeto De [Processadorde EventosComo](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) parâmetro. Utilize este parâmetro para controlar o comportamento do [EventProcessorHost.UnregisterEventProcessorAsync.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) define quatro propriedades e um evento:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): O tamanho máximo da coleção que pretende receber numa invocação de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Este tamanho não é o mínimo, apenas o tamanho máximo. Se houver menos mensagens a receber, **processEventsAsync** executa com tantas quantas estavam disponíveis.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Um valor utilizado pelo canal AMQP subjacente para determinar o limite superior de quantas mensagens o cliente deve receber. Este valor deve ser maior ou igual ao [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Se este parâmetro for **verdadeiro,** [processeventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) é chamado quando a chamada subjacente para receber eventos em uma partição tempos fora. Este método é útil para tomar ações baseadas no tempo durante períodos de inatividade na partição.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Permite definir um ponteiro de função ou expressão lambda, que é chamado para fornecer a compensação inicial quando um leitor começa a ler uma partição. Sem especificar esta compensação, o leitor começa no evento mais antigo, a menos que um ficheiro JSON com uma contrapartida já tenha sido guardado na conta de armazenamento fornecida ao construtor [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Este método é útil quando se pretende alterar o comportamento da startup do leitor. Quando este método é invocado, o parâmetro do objeto contém o ID de partição para o qual o leitor está a ser iniciado.
- [ExcepçõesRecebidasEventoArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Permite-lhe receber a notificação de quaisquer exceções subjacentes que ocorram no [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se as coisas não estão a funcionar como esperas, este evento é um bom lugar para começar a procurar.

## <a name="epoch"></a>Época

Eis como funciona a época de receção:

### <a name="with-epoch"></a>Com época
A época é um identificador único (valor da época) que o serviço utiliza, para impor a partilha/arrendamento. Cria-se um recetor baseado em epoch utilizando o método [CreateEpochReceiver.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) Este método cria um recetor baseado em época. O recetor é criado para uma partição específica do centro de eventos do grupo de consumidores especificado.

A funcionalidade de época proporciona aos utilizadores a capacidade de garantir que existe apenas um recetor num grupo de consumidores em qualquer momento, com as seguintes regras:

- Se não existir um recetor existente num grupo de consumidores, o utilizador pode criar um recetor com qualquer valor de época.
- Se houver um recetor com um valor de época e1 e um novo recetor for criado com um valor de época e2 onde e1 <= e2, o recetor com e1 será desligado automaticamente, o recetor com e2 é criado com sucesso.
- Se houver um recetor com um valor de época e1 e um novo recetor for criado com um valor de época e2 onde e1 > e2 e2, então a criação de e2 com falha com o erro: Já existe um recetor com época e1.

### <a name="no-epoch"></a>Sem época
Cria-se um recetor não baseado em epoch utilizando o método [CreateReceiver.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) 

Existem alguns cenários no processamento de fluxos onde os utilizadores gostariam de criar vários recetores num único grupo de consumidores. Para apoiar tais cenários, temos a capacidade de criar um recetor sem época e, neste caso, permitimos até 5 recetores simultâneos no grupo de consumidores.

### <a name="mixed-mode"></a>Modo Misto
Não recomendamos o uso da aplicação quando cria um recetor com época e, em seguida, muda para não época ou vice-versa no mesmo grupo de consumidores. No entanto, quando este comportamento ocorre, o serviço trata-o usando as seguintes regras:

- Se houver um recetor já criado com época e1 e estiver a receber eventos e for criado um novo recetor sem época, a criação de um novo recetor falhará. Os recetores de época têm sempre precedência no sistema.
- Se houvesse um recetor já criado com época e1 e se desligasse, e um novo recetor fosse criado sem época numa nova Fábrica de Mensagens, a criação de um novo recetor será bem sucedida. Há aqui uma ressalva de que o nosso sistema detetará a "desconexão do recetor" após ~10 minutos.
- Se houver um ou mais recetores criados sem época, e um novo recetor for criado com época e1, todos os recetores antigos ficam desligados.


> [!NOTE]
> Recomendamos a utilização de diferentes grupos de consumidores para aplicações que utilizem épocas e para aqueles que não usam épocas para evitar erros. 


## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o Anfitrião do Processador de Eventos, consulte os seguintes artigos para saber mais sobre os Hubs de Eventos:

- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Pitão](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Amostras de Hubs de Eventos no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
