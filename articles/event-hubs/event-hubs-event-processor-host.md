---
title: Receber eventos com o anfitrião do processador de eventos - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo descreve o anfitrião do processador de eventos no Event Hubs do Azure, que simplifica o gerenciamento de ponto de verificação, locação e a ler eventos paralelo ização.
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
ms.date: 07/16/2019
ms.author: shvija
ms.openlocfilehash: 013200295f3a6a48d6d96663f98bce506808cd70
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277363"
---
# <a name="event-processor-host"></a>Anfitrião do processador de eventos

Os Hubs de eventos do Azure é um serviço de ingestão de telemetria poderosa que pode ser utilizado para transmissão milhões de eventos um custo reduzido. Este artigo descreve como consumir eventos ingeridos com o *anfitrião do processador de eventos* (EPH); um agente de consumidor inteligente que simplifica a gestão dos pontos de verificação, leasing e os leitores dos eventos paralela.  

A chave ser dimensionada para os Hubs de eventos é a idéia de consumidores particionadas. Em comparação com o [consumidores concorrentes](https://msdn.microsoft.com/library/dn568101.aspx) padrão, o padrão de consumidor particionado permite criar uma escala elevada, removendo o afunilamento de contenção e promovendo o paralelismo de ponto a ponto.

## <a name="home-security-scenario"></a>Cenário de segurança inicial

Como um cenário de exemplo, considere uma empresa de segurança inicial que monitoriza a 100.000 casas. A cada minuto, obtém dados de vários sensores, como um detetor de movimento, sensor de porta de entrada/janela aberta, detetor de quebra de vidro, etc., instalado em cada página inicial. A empresa fornece um web site a residentes monitorizar a atividade de sua casa em tempo real.

Cada sensor envia por push dados para um hub de eventos. O hub de eventos é configurado com 16 partições. No final de consumo, é necessário um mecanismo que pode ler esses eventos, consolidá-los (filtrar, Agregar, etc.) e a agregação para um blob de armazenamento, em seguida, está projetada para uma página da web fácil de utilizar a cópia de segurança.

## <a name="write-the-consumer-application"></a>Escreva a aplicação de consumidor

Ao projetar o consumidor num ambiente distribuído, o cenário tem de lidar com os seguintes requisitos:

1. **Escalonáve** Crie vários consumidores, com cada consumidor assumindo a propriedade da leitura de algumas partições de hubs de eventos.
2. **Balanceamento de carga:** Aumente ou reduza os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detector de monóxido de carbono) é adicionado a cada página inicial, aumenta o número de eventos. Nesse caso, o operador (um ser humano) aumenta o número de instâncias de consumidor. Em seguida, o conjunto de consumidores pode reequilibrar o número de partições que ele possui, para partilhar a carga com os consumidores recém-adicionada.
3. **Retomada direta em caso de falhas:** Se um consumidor (**consumidor a**) falhar (por exemplo, a máquina virtual que hospeda o consumidor falhar repentinamente), outros consumidores deverão ser capazes de pegar as partições pertencentes ao **consumidor a** e continuar. Além disso, o continuação ponto, chamado um *ponto de verificação* ou *deslocamento*, deve estar no momento exato em que **consumidor A** com falha, ou um pouco antes disso.
4. **Eventos de consumo:** Embora os três pontos anteriores lidem com o gerenciamento do consumidor, deve haver código para consumir os eventos e fazer algo útil com ele; por exemplo, agregue-a e carregue-a no armazenamento de BLOBs.

Em vez de criar sua própria solução para isso, os Hubs de eventos fornece essa funcionalidade por meio da [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interface e o [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) classe.

## <a name="ieventprocessor-interface"></a>IEventProcessor interface

Primeiro, consumir aplicativos implementam a interface [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , que tem quatro métodos: [OpenAsync, CloseAsync, ProcessErrorAsync e ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Essa interface contém o código real para consumir os eventos que envia os Hubs de eventos. O código seguinte mostra uma implementação simples:

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

Em seguida, criar uma instância de um [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instância. Consoante a sobrecarga, ao criar o [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instância no construtor, são utilizados os seguintes parâmetros:

- **nome de anfitrião:** o nome de cada instância de consumidor. Cada instância de **EventProcessorHost** deve ter um valor exclusivo para essa variável dentro de um grupo de consumidores, portanto, não codifique esse valor.
- **eventHubPath:** O nome do hub de eventos.
- **consumerGroupName:** Os hubs de eventos usam **$Default** como o nome do grupo de consumidores padrão, mas é uma boa prática criar um grupo de consumidores para o seu aspecto específico de processamento.
- **eventHubConnectionString:** A cadeia de conexão para o Hub de eventos, que pode ser recuperada do portal do Azure. Esta cadeia de ligação deve ter **escutar** permissões no hub de eventos.
- **storageConnectionString:** A conta de armazenamento usada para o gerenciamento de recursos interno.

Por fim, os consumidores de registar o [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instância com o serviço de Hubs de eventos. Registar uma classe de processador de eventos com uma instância do EventProcessorHost inicia o processamento de eventos. Registar instrui o serviço de Hubs de eventos, esperar que a aplicação de consumidor consome eventos de algumas das respetivas partições e para invocar a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) código de implementação, sempre que enviar eventos para consumir. 


### <a name="example"></a>Exemplo

Por exemplo, imagine que existem 5 máquinas virtuais (VMs) dedicados para consumo de eventos e uma aplicação de consola simples em cada VM, que é o consumo real que funciona. Cada aplicação de consola, em seguida, cria um [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) de instância e regista-o com o serviço de Hubs de eventos.

Neste cenário de exemplo, digamos que 16 partições são alocadas para o 5 **EventProcessorHost** instâncias. Algumas **EventProcessorHost** instâncias podem possuir mais algumas partições que outros. Para cada partição que uma **EventProcessorHost** instância é proprietário, ele cria uma instância do `SimpleEventProcessor` classe. Portanto, existem 16 instâncias de `SimpleEventProcessor` geral, com um atribuídos a cada partição.

A lista seguinte resume neste exemplo:

- 16 partições dos Hubs de eventos.
- 5 VMs, a aplicação de 1 de consumidor (por exemplo, Consumer.exe) em cada VM.
- 5 EPH instâncias registradas, 1 em cada VM por Consumer.exe.
- 16 `SimpleEventProcessor` objetos criados por instâncias EPH 5.
- 1 aplicação de Consumer.exe pode conter 4 `SimpleEventProcessor` objetos, uma vez que o 1 instância EPH pode possuí 4 partições.

## <a name="partition-ownership-tracking"></a>Propriedade de partição de controlo

Propriedade de uma partição para uma instância EPH (ou um consumidor) é controlada através da conta de armazenamento do Azure que é fornecida para o controlo. É possível visualizar o rastreio como uma tabela simple, da seguinte forma. Pode ver a implementação real, examinando os blobs na conta de armazenamento fornecida:

| **Nome do grupo de consumidores** | **ID de partição** | **Nome de anfitrião (proprietário)** | **Tempo de concessão (ou propriedade) adquirido** | **Desvio na partição (ponto de verificação)** |
| --- | --- | --- | --- | --- |
| $Predefinição | 0 | Consumidor\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Predefinição | 1 | Consumidor\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Predefinição | 2 | Consumidor\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Predefinição | 15 | Consumidor\_VM3 | 2018-04-15T01:22:56 | 976 |

Aqui, cada anfitrião adquire a propriedade de uma partição para um determinado período de tempo (a duração da concessão). Se uma falha de anfitrião (VM encerra), em seguida, a concessão expira. Outros anfitriões tentarem obter a propriedade da partição e dos anfitriões for concluída com êxito. Este processo repõe a concessão na partição com um novo proprietário. Desta forma, apenas um único leitor ao mesmo tempo pode ler a partir de qualquer determinada partição dentro de um grupo de consumidores.

## <a name="receive-messages"></a>Receber mensagens

Cada chamada para [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) oferece uma coleção de eventos. É da responsabilidade do cliente para lidar com esses eventos. Se você quiser ter certeza de que o host do processador processa cada mensagem pelo menos uma vez, você precisa escrever sua própria manter código de nova tentativa. Mas tenha cuidado com as mensagens inviabilizada.

É recomendável que faça coisas relativamente rápidas; ou seja, fazer como processamento pequeno quanto possível. Em alternativa, utilize grupos de consumidores. Se você precisar gravar no armazenamento e fazer algum roteamento, é melhor usar dois grupos de consumidores e ter duas implementações de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que são executadas separadamente.

Em algum momento durante o processamento, poderá controlar o que tenha lido e concluída. Controlando os é crítico, se é necessário reiniciar leitura, pelo que não a voltar ao início da transmissão em fluxo. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) simplifica esse controle utilizando *pontos de verificação*. Um ponto de verificação é uma localização ou o deslocamento, de uma determinada partição, dentro de um determinado grupo de consumidores, na altura em que estiver satisfeito que têm de processar as mensagens. A marcação de um ponto de verificação **EventProcessorHost** é realizado chamando o [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) método no [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objeto. Esta operação é feita dentro de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) método, mas também pode ser feito [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Ponto de verificação

O [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) método possui duas sobrecargas: o primeiro, sem parâmetros, os pontos de verificação para o deslocamento de eventos mais alto dentro da coleção retornada por [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Este desvio é uma marca de "água alta"; parte do princípio de que ter de processar todos os eventos recentes quando chamá-lo. Se usar esse método desta forma, lembre-se de que é esperado para chamá-lo Depois devolveu o código de processamento de eventos. A segunda sobrecarga permite-lhe especificar uma [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) instância para o ponto de verificação. Este método permite-lhe utilizar um tipo diferente de marca d'água para o ponto de verificação. Com esta marca d'água, é possível implementar uma marca de "d'água": o evento mais baixas sequenciado em que tem a certeza de que tenha sido processado. Essa sobrecarga é fornecida para permitir flexibilidade na gestão de deslocamento.

Quando o ponto de verificação é executado, um ficheiro JSON com informações específicas da partição (especificamente, o deslocamento), é escrito para a conta de armazenamento fornecida no construtor para [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Este ficheiro é atualizado de forma contínua. É fundamental considerar o ponto de verificação no contexto - seria aconselhável ponto de verificação de cada mensagem. A conta de armazenamento utilizada para o ponto de verificação, provavelmente não seria processar esta carga, mas mais importante ponto de verificação cada evento individual é indicativo de um padrão de mensagens em fila para o qual uma fila do Service Bus pode ser uma opção melhor que um hub de eventos. A idéia por trás dos Hubs de eventos é que ", pelo menos, uma vez" entrega em grande escala. Ao fazer sua idempotentes downstream sistemas, é fácil de recuperar de falhas ou reiniciar esse resultado nos mesmos eventos a ser recebidos várias vezes.

## <a name="thread-safety-and-processor-instances"></a>Instâncias de processador e de segurança do thread

Por predefinição, [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) é o thread seguro e se comporta de forma síncrona em relação a instância do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Quando chegam aos eventos para uma partição [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) denomina-se no **IEventProcessor** instância para que a partição e bloqueará mais chamadas para **ProcessEventsAsync**para a partição. As mensagens subsequentes e chamadas para **ProcessEventsAsync** enfileirar em segundo plano, como o bombardeamento de mensagens continua a ser executado em segundo plano em outros threads. A segurança do thread remove a necessidade de coleções thread-safe e aumenta significativamente o desempenho.

## <a name="shut-down-gracefully"></a>Encerrar corretamente

Por fim, [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) permite que um encerramento correto de todos os leitores de partição e sempre deve ser chamado quando a encerrar uma instância de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Falha ao fazer isso pode causar atrasos quando a partir de outras instâncias de **EventProcessorHost** devido à expiração da concessão e conflitos de "Epoch". O gerenciamento de época é abordado em detalhes na seção [época](#epoch) do artigo. 

## <a name="lease-management"></a>Gestão da concessão
Registar uma classe de processador de eventos com uma instância do EventProcessorHost inicia o processamento de eventos. A instância de host obtém concessões em algumas partições do Hub de eventos, possivelmente Pegando algumas das restantes instâncias de anfitrião, de forma que converge numa distribuição uniforme das partições em todas as instâncias de anfitrião. Para cada partição de concessão, a instância de host cria uma instância da classe de processador de eventos fornecido, em seguida, recebe eventos de nessa partição e passa para a instância de processador de eventos. À medida que são adicionadas mais instâncias e mais concessões são pegou, o EventProcessorHost, eventualmente, equilibra a carga entre todos os consumidores.

Tal como explicado anteriormente, a tabela de controle simplifica bastante a natureza de dimensionamento automático dos [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Como uma instância do **EventProcessorHost** é iniciado, ele adquire concessões tantas quanto possível e começa a ler eventos. Como as concessões prestes a expirar, **EventProcessorHost** tenta renová-los ao colocar uma reserva. Se a concessão está disponível para a renovação, o processador continua a ler, mas se não estiver, o leitor está fechado e [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) é chamado. **CloseAsync** é um bom momento para realizar qualquer limpeza final para essa partição.

**EventProcessorHost** inclui um [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) propriedade. Esta propriedade permite um controlo sobre a gestão da concessão. Defina estas opções antes de registar sua [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementação.

## <a name="control-event-processor-host-options"></a>Opções de controlo de anfitrião do processador de eventos

Além disso, uma sobrecarga da [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) assume um [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objeto como um parâmetro. Utilize este parâmetro para controlar o comportamento da [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) em si. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) define quatro propriedades e um evento:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): O tamanho máximo da coleção que você deseja receber em uma invocação de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Este tamanho não é o mínimo, apenas o tamanho máximo. Se existirem menos mensagens a serem recebidos **ProcessEventsAsync** executa com o máximo como estavam disponíveis.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Um valor usado pelo canal AMQP subjacente para determinar o limite superior de quantas mensagens o cliente deve receber. Este valor deve ser maior ou igual a [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Se esse parâmetro for **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) será chamado quando a chamada subjacente para receber eventos em uma partição atingir o tempo limite. Este método é útil para realizar ações com base no tempo durante os períodos de inatividade na partição.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Permite que um ponteiro de função ou expressão lambda seja definido, que é chamado para fornecer o deslocamento inicial quando um leitor começa a ler uma partição. Sem especificar este desvio, o leitor é iniciado no evento mais antigo, a menos que um ficheiro JSON com um desvio já foi guardado na conta de armazenamento fornecida para o [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) construtor. Este método é útil quando deseja alterar o comportamento de inicialização do leitor. Quando esse método é invocado, o parâmetro de objeto contém o ID de partição para o qual o leitor está a ser iniciado.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Permite que você receba notificações de quaisquer exceções subjacentes que ocorram em [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se as coisas não estão funcionando como esperado, este evento é um bom lugar para começar a procurar.

## <a name="epoch"></a>Época

Veja como funciona a época de recebimento:

### <a name="with-epoch"></a>Com época
Época é um identificador exclusivo (valor de época) que o serviço usa para impor a propriedade de partição/concessão. Você cria um receptor baseado em época usando o método [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) . Esse método cria um receptor baseado em época. O receptor é criado para uma partição de Hub de eventos específica do grupo de consumidores especificado.

O recurso época fornece aos usuários a capacidade de garantir que haja apenas um destinatário em um grupo de consumidores em qualquer ponto no tempo, com as seguintes regras:

- Se não houver nenhum receptor existente em um grupo de consumidores, o usuário poderá criar um destinatário com qualquer valor de época.
- Se houver um receptor com um valor de época E1 e um novo destinatário for criado com um valor de época E2 em que E1 < = E2, o receptor com E1 será desconectado automaticamente, o destinatário com E2 será criado com êxito.
- Se houver um receptor com um valor de época E1 e um novo destinatário for criado com um valor de época E2 em que E1 > E2, a criação de E2 com falha com o erro: Já existe um receptor com a época E1.

### <a name="no-epoch"></a>Sem época
Você cria um receptor não baseado em época usando o método [](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) createreceiver. 

Há alguns cenários no processamento de fluxo em que os usuários gostariam de criar vários destinatários em um único grupo de consumidores. Para dar suporte a esses cenários, temos a capacidade de criar um receptor sem época e, nesse caso, permitimos 5 destinatários simultâneos no grupo de consumidores.

### <a name="mixed-mode"></a>Modo misto
Não recomendamos o uso do aplicativo no qual você cria um receptor com época e, em seguida, muda para nenhuma época ou vice-versa no mesmo grupo de consumidores. No entanto, quando esse comportamento ocorre, o serviço o trata usando as seguintes regras:

- Se houver um receptor já criado com a época E1 e estiver recebendo eventos ativamente e um novo destinatário for criado sem nenhuma época, a criação do novo receptor falhará. Os receptores de época sempre têm precedência no sistema.
- Se um receptor já tiver sido criado com a época E1 e tiver sido desconectado, e um novo destinatário for criado sem nenhuma época em um novo MessagingFactory, a criação do novo receptor terá êxito. Há uma limitação aqui que nosso sistema detectará a "desconexão do receptor" após cerca de 10 minutos.
- Se houver um ou mais receptores criados sem época e um novo receptor for criado com a época E1, todos os receptores antigos serão desconectados.


## <a name="next-steps"></a>Passos Seguintes

Agora que está familiarizado com o anfitrião do processador de eventos, consulte os seguintes artigos para saber mais sobre os Hubs de eventos:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Guia de programação dos Event Hubs](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de eventos no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
