---
title: Gerir a simultaneidade
titleSuffix: Azure Storage
description: Aprenda a gerir a moeda para os serviços Blob, Queue, Table e File.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373678"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gestão da Concurrency no Armazenamento Microsoft Azure

As aplicações modernas baseadas na Internet normalmente têm vários utilizadores visualizando e atualizando dados simultaneamente. Isto requer que os desenvolvedores de aplicações pensem cuidadosamente sobre como fornecer uma experiência previsível aos seus utilizadores finais, especialmente para cenários em que vários utilizadores possam atualizar os mesmos dados. Existem três principais estratégias de conmoedação de dados que os desenvolvedores normalmente consideram:  

1. Concurrency otimista – Uma aplicação que executa uma atualização irá como parte da sua atualização verificar se os dados mudaram desde que a aplicação leu pela última vez esses dados. Por exemplo, se dois utilizadores que visualizam uma página wiki fizerem uma atualização para a mesma página, então a plataforma wiki deve garantir que a segunda atualização não sobrepor a primeira atualização – e que ambos os utilizadores entendam se a sua atualização foi bem sucedida ou não. Esta estratégia é mais frequentemente utilizada em aplicações web.
2. Concurrency pessimista – Uma aplicação que pretenda realizar uma atualização irá travar um objeto que impeça outros utilizadores de atualizarem os dados até que o bloqueio seja lançado. Por exemplo, num cenário de replicação de dados master/subordinado onde apenas o mestre irá realizar atualizações, o mestre normalmente irá manter um bloqueio exclusivo por um longo período de tempo nos dados para garantir que mais ninguém pode atualizá-lo.
3. Último escritor vence – Uma abordagem que permite que quaisquer operações de atualização prossigam sem verificar se qualquer outra aplicação atualizou os dados desde que a aplicação leu os dados pela primeira vez. Esta estratégia (ou falta de uma estratégia formal) é geralmente utilizada quando os dados são divididos de modo a que não haja probabilidade de vários utilizadores acederem aos mesmos dados. Também pode ser útil onde fluxos de dados de curta duração estão sendo processados.  

Este artigo fornece uma visão geral de como a plataforma de armazenamento Azure simplifica o desenvolvimento, fornecendo suporte de primeira classe para todas estas três estratégias de conmoedação.  

## <a name="azure-storage-simplifies-cloud-development"></a>Armazenamento Azure simplifica desenvolvimento de nuvem

O serviço de armazenamento Azure suporta as três estratégias, embora seja distinto na sua capacidade de fornecer total apoio à conmoeda otimista e pessimista porque foi projetado para abraçar um forte modelo de consistência que garante que quando o O serviço de armazenamento compromete uma operação de inserção de dados ou atualização todos os acessos adicionais a esses dados verão a última atualização. As plataformas de armazenamento que utilizam um eventual modelo de consistência têm um desfasamento entre quando uma escrita é realizada por um utilizador e quando os dados atualizados podem ser vistos por outros utilizadores complicando assim o desenvolvimento das aplicações do cliente de forma a evitar inconsistências de afetando os utilizadores finais.  

Além de selecionar uma estratégia de condivisa apropriada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as mudanças – particularmente alterações no mesmo objeto através de transações. O serviço de armazenamento Azure utiliza o isolamento instantâneo para permitir que as operações de leitura ocorram simultaneamente com operações de escrita dentro de uma única divisória. Ao contrário de outros níveis de isolamento, o isolamento instantâneo garante que todas as leituras vêem uma imagem consistente dos dados mesmo quando estão a ocorrer atualizações – essencialmente devolvendo os últimos valores comprometidos enquanto uma transação de atualização está a ser processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gestão de moedas no armazenamento blob

Pode optar por utilizar modelos de conmoeda otimistas ou pessimistas para gerir o acesso a bolhas e contentores no serviço Blob. Se não especificar explicitamente uma estratégia que escreve pela última vez, as vitórias são o padrão.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Conmoeda otimista para bolhas e contentores

O serviço de armazenamento atribui um identificador a todos os objetos armazenados. Este identificador é atualizado sempre que uma operação de atualização é realizada num objeto. O identificador é devolvido ao cliente como parte de uma resposta HTTP GET utilizando o cabeçalho ETag (marca de entidade) que é definido dentro do protocolo HTTP. Um utilizador que efetua uma atualização sobre tal objeto pode enviar o ETag original juntamente com um cabeçalho condicional para garantir que uma atualização só ocorrerá se uma determinada condição for satisfeita – neste caso, a condição é um cabeçalho "If-Match", que requer o Serviço de Armazenamento t o garantir que o valor do ETag especificado no pedido de atualização é o mesmo que o armazenado no Serviço de Armazenamento.  

O esboço deste processo é o seguinte:  

1. Recuperar uma bolha do serviço de armazenamento, a resposta inclui um valor http ETag Header que identifica a versão atual do objeto no serviço de armazenamento.
2. Quando atualizar a bolha, inclua o valor ETag que recebeu no passo 1 no cabeçalho condicional **If-Match** do pedido que envia ao serviço.
3. O serviço compara o valor ETag no pedido com o valor atual do ETag da bolha.
4. Se o valor atual do ETag da bolha for uma versão diferente do ETag no cabeçalho condicional **If-Match** no pedido, o serviço devolve um erro de 412 ao cliente. Isto indica ao cliente que outro processo atualizou a bolha desde que o cliente a recuperou.
5. Se o valor atual do ETag da bolha for a mesma versão do ETag no cabeçalho condicional **If-Match** no pedido, o serviço realiza a operação solicitada e atualiza o valor atual do ETag da bolha para mostrar que criou uma nova versão.  

O C# seguinte corte (utilizando a Biblioteca de Armazenamento de Clientes 4.2.0) mostra um simples exemplo de como construir uma Condição de **Acesso If-Match** com base no valor ETag que é acedido a partir das propriedades de uma bolha que foi previamente recuperada ou inserida. Em seguida, utiliza o objeto **AccessCondition** quando atualiza a bolha: o objeto **AccessCondition** adiciona o cabeçalho **If-Match** ao pedido. Se outro processo tiver atualizado a bolha, o serviço Blob devolve uma mensagem de estado HTTP 412 (Pré-condição Falhada). Pode descarregar a amostra completa aqui: [Gerir a Concurrency utilizando o Armazenamento Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

O Armazenamento Azure também inclui suporte para cabeçalhos condicionais adicionais, tais como **If-Modificado-Since**, **If-Unmodificad-Since** e **If-None-Match,** bem como combinações dos mesmos. Para mais informações, consulte a especificação de [cabeçalhos condicionais para operações](https://msdn.microsoft.com/library/azure/dd179371.aspx)de serviço blob .  

O quadro seguinte resume as operações de contentores que aceitam cabeçalhos condicionais, tais como **O Se-Match** no pedido e que devolvem um valor ETag na resposta.  

| Operação | Valor de Retorna do Recipiente ETag | Aceita cabeçalhos condicional |
|:--- |:--- |:--- |
| Criar contentor |Sim |Não |
| Obter propriedades de contentores |Sim |Não |
| Obter Metadados de Contentores |Sim |Não |
| Definir metadados de contentores |Sim |Sim |
| Obter contentor ACL |Sim |Não |
| Set Container ACL |Sim |Sim (*) |
| Eliminar contentor |Não |Sim |
| Contentor de arrendamento |Sim |Sim |
| Lista blobs |Não |Não |

(*) As permissões definidas pela SetContainerACL são em cache e as atualizações destas permissões demoram 30 segundos a propagar-se durante as quais as atualizações do período não são garantidas como consistentes.  

O quadro seguinte resume as operações blob que aceitam cabeçalhos condicionais como **o If-Match** no pedido e que devolvem um valor ETag na resposta.

| Operação | Valor de Devoluções ETag | Aceita cabeçalhos condicional |
|:--- |:--- |:--- |
| Coloque Blob |Sim |Sim |
| Obter Blob |Sim |Sim |
| Obter Propriedades Blob |Sim |Sim |
| Definir propriedades blob |Sim |Sim |
| Obter Metadados Blob |Sim |Sim |
| Definir Metadados Blob |Sim |Sim |
| Arrendamento Blob (*) |Sim |Sim |
| Snapshot Blob |Sim |Sim |
| Copiar Blob |Sim |Sim (para fonte e bolha de destino) |
| Abortar copy blob |Não |Não |
| Eliminar Blob |Não |Sim |
| Bloquear |Não |Não |
| Lista de blocos |Sim |Sim |
| Obter lista de blocos |Sim |Não |
| Página de colocação |Sim |Sim |
| Obtenha gamas de páginas |Sim |Sim |

(*) Lease Blob não muda o ETag numa bolha.  

### <a name="pessimistic-concurrency-for-blobs"></a>Conmoeda pessimista para bolhas

Para bloquear uma bolha para uso exclusivo, pode adquirir um contrato de [arrendamento.](https://msdn.microsoft.com/library/azure/ee691972.aspx) Ao adquirir um contrato de arrendamento, especifice quanto tempo precisa do arrendamento: isto pode ser entre 15 a 60 segundos ou infinito, o que equivale a um bloqueio exclusivo. Você pode renovar um arrendamento finito para estender, e você pode liberar qualquer arrendamento quando você terminar com ele. O serviço Blob lança automaticamente arrendamentos finitos quando expiram.  

Os arrendamentos permitem apoiar diferentes estratégias de sincronização, incluindo escrita exclusiva/leitura partilhada, escrita exclusiva/leitura exclusiva e escrita partilhada /leitura exclusiva. Quando existe um contrato de arrendamento, o serviço de armazenamento impõe escritas exclusivas (put, set e delete operations) no entanto, garantir a exclusividade para as operações de leitura requer que o desenvolvedor garanta que todas as aplicações de clientes utilizem um ID de locação e que apenas um cliente de cada vez tem um identificação válida do arrendamento. Leia as operações que não incluem um resultado de ID de locação em leituras partilhadas.  

O C# seguinte corte mostra um exemplo de adquirir um contrato de arrendamento exclusivo por 30 segundos numa bolha, atualizar o conteúdo da bolha e, em seguida, libertar o arrendamento. Se já existe um contrato de arrendamento válido na bolha quando tenta adquirir um novo contrato de arrendamento, o serviço Blob devolve um resultado de situação de conflito "HTTP (409)." O seguinte snippet utiliza um objeto **AccessCondition** para encapsular as informações de locação quando faz um pedido para atualizar a bolha no serviço de armazenamento.  Pode descarregar a amostra completa aqui: [Gerir a Concurrency utilizando o Armazenamento Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Se tentar uma operação de escrita numa bolha alugada sem passar o ID de locação, o pedido falha com um erro de 412. Note que se o contrato expirar antes de ligar para o método **UploadText** mas ainda passar o ID de locação, o pedido também falha com um erro **de 412.** Para obter mais informações sobre a gestão dos prazos de validade do arrendamento e do arrendamento de IDs, consulte a documentação [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST.  

As seguintes operações blob podem usar locações para gerir a moeda pessimista:  

* Coloque Blob
* Obter Blob
* Obter Propriedades Blob
* Definir propriedades blob
* Obter Metadados Blob
* Definir Metadados Blob
* Eliminar Blob
* Bloquear
* Lista de blocos
* Obter lista de blocos
* Página de colocação
* Obtenha gamas de páginas
* Snapshot Blob - locação opcional se existe um contrato de arrendamento
* Copy Blob - locação de identidade necessária se existir um contrato de arrendamento na bolha de destino
* Abortcopy Blob - locação de identidade necessária se existir um arrendamento infinito na bolha de destino
* Arrendamento Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Conmoeda pessimista para contentores

As locações em contentores permitem que as mesmas estratégias de sincronização sejam suportadas como em blobs (escrita exclusiva/leitura partilhada, leitura exclusiva/ escrita exclusiva/ leitura exclusiva/leitura exclusiva) no entanto, ao contrário das bolhas, o serviço de armazenamento apenas impõe exclusividade nas operações de exclusão. Para eliminar um contentor com locação ativa, o cliente deve incluir o ID de locação ativa com o pedido de eliminação. Todas as outras operações de contentores têm sucesso num contentor alugado sem incluir o ID de locação, caso em que são operações partilhadas. Se for necessária exclusividade de atualização (colocação ou conjunto) ou de leitura de operações, os desenvolvedores devem garantir que todos os clientes utilizem um ID de locação e que apenas um cliente de cada vez tem um ID de locação válido.  

As seguintes operações de contentores podem utilizar locações para gerir a moeda pessimista:  

* Eliminar contentor
* Obter propriedades de contentores
* Obter Metadados de Contentores
* Definir metadados de contentores
* Obter contentor ACL
* Set Container ACL
* Contentor de arrendamento  

Para obter mais informações, consulte:  

* [Especificação de cabeçalhos condicionais para operações de serviço blob](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Contentor de arrendamento](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Arrendamento Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Gestão da moeda no armazenamento de mesa

O serviço Table utiliza cheques de moeda otimistas como o comportamento padrão quando está a trabalhar com entidades, ao contrário do serviço Blob onde deve escolher explicitamente realizar controlos de moeda otimistas. A outra diferença entre a tabela e os serviços Blob é que você só pode gerir o comportamento de condivisa de entidades enquanto com o serviço Blob você pode gerir a conmoeda de ambos os recipientes e bolhas.  

Para utilizar a conmoeda otimista e verificar se outro processo modificou uma entidade desde que a recuperou do serviço de armazenamento de mesa, pode utilizar o valor ETag que recebe quando o serviço de mesa devolve uma entidade. O esboço deste processo é o seguinte:  

1. Recuperar uma entidade do serviço de armazenamento de mesa, a resposta inclui um valor ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2. Quando atualizar a entidade, inclua o valor ETag que recebeu no passo 1 no cabeçalho obrigatório **se-Match** do pedido que envia para o serviço.
3. O serviço compara o valor ETag no pedido com o valor atual do ETag da entidade.
4. Se o valor Atual eTag da entidade for diferente do ETag no cabeçalho obrigatório **if-Match** no pedido, o serviço devolve um erro de 412 ao cliente. Isto indica ao cliente que outro processo atualizou a entidade desde que o cliente o recuperou.
5. Se o valor Atual do ETag da entidade for o mesmo que o ETag no cabeçalho obrigatório **if-Match** no pedido ou o cabeçalho **If-Match** contiver o caráter wildcard (*), o serviço realiza a operação solicitada e atualiza o valor Atual eTag da entidade para mostrar que foi atualizado.  

Note que, ao contrário do serviço Blob, o serviço de mesa requer que o cliente inclua um cabeçalho **If-Match** em pedidos de atualização. No entanto, é possível forçar uma atualização incondicional (último escritor ganha estratégia) e contornar as verificações de condivisões se o cliente definir o cabeçalho **If-Match** para o personagem wildcard (*) no pedido.  

O C# seguinte snippet mostra uma entidade cliente que foi previamente criada ou recuperada tendo o seu endereço de e-mail atualizado. A operação inicial de inserção ou recuperação armazena o valor ETag no objeto do cliente, e porque a amostra utiliza a mesma instância de objeto quando executa a operação de substituição, envia automaticamente o valor ETag de volta para o serviço de mesa, permitindo que o serviço verificar se há violações de moedas. Se outro processo tiver atualizado a entidade no armazenamento de mesa, o serviço devolve uma mensagem de estado HTTP 412 (Pré-condição Falhada).  Pode descarregar a amostra completa aqui: [Gerir a Concurrency utilizando o Armazenamento Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Para desativar explicitamente a verificação da moeda, deverá definir a propriedade **ETag** do objeto **do empregado** para "*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

O quadro seguinte resume como as operações da entidade de tabela utilizam os valores DoETag:

| Operação | Valor de Devoluções ETag | Requer cabeçalho de pedido se-Jogo |
|:--- |:--- |:--- |
| Consultas |Sim |Não |
| Inserir Entidade |Sim |Não |
| Entidade de Atualização |Sim |Sim |
| Entidade de Fusão |Sim |Sim |
| Excluir Entidade |Não |Sim |
| Inserir ou substituir entidade |Sim |Não |
| Entidade de Inserção ou Fusão |Sim |Não |

Note que as operações de **Inserção ou Substituição de Entidades** e **Inserção ou Fusão** *não* realizam quaisquer controlos de moeda porque não enviam um valor ETag para o serviço de mesa.  

Em geral, os desenvolvedores que utilizam tabelas devem basear-se em conmoedas otimistas no desenvolvimento de aplicações escaláveis. Se for necessário um bloqueio pessimista, uma abordagem que os desenvolvedores podem tomar ao aceder às Tabelas é atribuir uma bolha designada para cada mesa e tentar arrendar a bolha antes de operar na mesa. Esta abordagem requer que a aplicação garanta que todas as vias de acesso aos dados obtenham o arrendamento antes de operarem em cima da mesa. Note também que o tempo mínimo de arrendamento é de 15 segundos, o que requer uma cuidadosa consideração pela escalabilidade.  

Para obter mais informações, consulte:  

* [Operações em Entidades](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gestão da Concurrency no Serviço de Fila

Um dos cenários em que a moeda é uma preocupação no serviço de fila é onde vários clientes estão a recuperar mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recibo pop, que é necessário para apagar a mensagem. A mensagem não é automaticamente apagada da fila, mas depois de recuperada, não é visível para outros clientes para o intervalo de tempo especificado pelo parâmetro timeout de visibilidade. Espera-se que o cliente que recupere a mensagem apague a mensagem depois de processada, e antes do tempo especificado pelo elemento TimeNextVisible da resposta, que é calculada com base no valor do parâmetro timeout de visibilidade. O valor do tempo de tempo de visibilidade é adicionado ao momento em que a mensagem é recuperada para determinar o valor do TimeNextVisible.  

O serviço de fila não tem suporte para uma conmoeda otimista ou pessimista e, por isso, os clientes que processam mensagens recuperadas de uma fila devem garantir que as mensagens são processadas de forma idempotente. Um último escritor ganha estratégia é usado para operações de atualização como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

* [Serviço de Fila REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Obter Mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Gestão de moedas em Ficheiros Azure

O serviço de ficheiros pode ser acedido utilizando dois pontos finais de protocolo diferentes – SMB e REST. O serviço REST não tem apoio para bloqueio suportógico ou bloqueio pessimista e todas as atualizações seguirão uma última estratégia de vitórias de um escritor. Os clientes SMB que montam ações de ficheiros podem alavancar mecanismos de bloqueio do sistema de ficheiros para gerir o acesso a ficheiros partilhados – incluindo a capacidade de realizar bloqueios pessimistas. Quando um cliente SMB abre um ficheiro, especifica tanto o acesso ao ficheiro como o modo de partilha. A definição de uma opção de acesso a ficheiros de "Write" ou "Read/Write" juntamente com um modo de partilha de ficheiros de "Nenhum" resultará em que o ficheiro seja bloqueado por um cliente SMB até que o ficheiro esteja fechado. Se a operação REST for tentada num ficheiro em que um cliente SMB tenha o ficheiro bloqueado, o serviço REST devolverá o código de estado 409 (Conflito) com código de erro SharingViolation.  

Quando um cliente SMB abre um ficheiro para eliminação, marca o ficheiro como pendente de exclusão até que todos os outros cabos abertos do cliente SMB nesse ficheiro estejam fechados. Enquanto um ficheiro estiver marcado como exclusão pendente, qualquer operação REST nesse ficheiro devolverá o código de estado 409 (Conflito) com código de erro SMBDeletePending. O código de estado 404 (Não Encontrado) não é devolvido, uma vez que é possível ao cliente SMB remover a bandeira de eliminação pendente antes de fechar o ficheiro. Por outras palavras, o código de estado 404 (Não Encontrado) só é esperado quando o ficheiro tiver sido removido. Note que enquanto um ficheiro estiver num estado de exclusão de SMB pendente, este não será incluído nos resultados dos Ficheiros da Lista. Além disso, note que as operações rest Delete File e REST Delete Diretório são cometidas atomicamente e não resultam num estado de exclusão pendente.  

Para obter mais informações, consulte:  

* [Gestão de Fechaduras de Ficheiros](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Passos Seguintes

Para a aplicação de amostra completa referenciada neste blog:  

* [Gerir a Concurrency usando o Armazenamento Azure - Aplicação de amostras](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para mais informações sobre o Armazenamento Azure consulte:  

* [Página inicial do armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Armazenamento Começando para [Blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [Mesa,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [Filas](../storage-dotnet-how-to-use-queues.md)e [Arquivos](../storage-dotnet-how-to-use-files.md)
* Arquitetura de Armazenamento – [Armazenamento Azure: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

