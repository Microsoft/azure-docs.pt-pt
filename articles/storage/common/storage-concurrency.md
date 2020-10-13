---
title: Gerir a simultaneidade
titleSuffix: Azure Storage
description: Saiba como gerir a concordância no Azure Storage para os serviços Blob, Queue, Table e File. Compreenda as três principais estratégias de concordância de dados utilizadas.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2732781d32e92c8ec03116988e33ec4fbe0b2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021566"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Managing Concurrency in Microsoft Azure Storage (Gerir a Simultaneidade no Armazenamento do Microsoft Azure)

As aplicações modernas baseadas na Internet normalmente têm vários utilizadores a visualizar e atualizar dados simultaneamente. Isto requer que os desenvolvedores de aplicações pensem cuidadosamente sobre como fornecer uma experiência previsível aos seus utilizadores finais, especialmente para cenários onde vários utilizadores podem atualizar os mesmos dados. Existem três principais estratégias de concuência de dados que os desenvolvedores normalmente consideram:  

1. Conúnva otimista – Uma aplicação que executa uma atualização irá verificar se os dados mudaram desde a última leitura da aplicação. Por exemplo, se dois utilizadores que estão a ver uma página wiki fizerem uma atualização para a mesma página, então a plataforma wiki deve garantir que a segunda atualização não substitui a primeira atualização – e que ambos os utilizadores entendam se a sua atualização foi ou não bem sucedida. Esta estratégia é mais utilizada em aplicações web.
2. Concuncy pessimista – Uma aplicação que pretenda realizar uma atualização irá bloquear um objeto que impede outros utilizadores de atualizarem os dados até que o bloqueio seja lançado. Por exemplo, num cenário de replicação de dados master/subordinado, onde apenas o mestre irá realizar atualizações, o mestre normalmente irá manter um bloqueio exclusivo durante um longo período de tempo nos dados para garantir que mais ninguém o possa atualizar.
3. Último escritor ganha – Uma abordagem que permite que quaisquer operações de atualização prossigam sem verificar se qualquer outra aplicação atualizou os dados desde que a aplicação leu pela primeira vez os dados. Esta estratégia (ou falta de uma estratégia formal) é geralmente utilizada onde os dados são divididos de forma a não haver probabilidade de vários utilizadores acederem aos mesmos dados. Também pode ser útil onde os fluxos de dados de curta duração estão sendo processados.  

Este artigo fornece uma visão geral de como a plataforma de armazenamento Azure simplifica o desenvolvimento, fornecendo suporte de primeira classe para todas estas três estratégias de concuência.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage simplifica o desenvolvimento da nuvem

O serviço de armazenamento Azure suporta as três estratégias, embora seja distinto na sua capacidade de fornecer apoio total para a concordância otimista e pessimista, pois foi projetado para abraçar um modelo de consistência forte que garante que quando o serviço de Armazenamento compromete uma inserção de dados ou atualizar a operação todos os acessos a esses dados verão a última atualização. As plataformas de armazenamento que utilizam um modelo de consistência eventual têm um desfasamento entre quando uma escrita é realizada por um utilizador e quando os dados atualizados podem ser vistos por outros utilizadores, complicando assim o desenvolvimento de aplicações do cliente de forma a evitar que inconsistências afetem os utilizadores finais.  

Além de selecionarem uma estratégia de concordância adequada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as mudanças – particularmente alterações no mesmo objeto através de transações. O serviço de armazenamento Azure utiliza o isolamento instantâneo para permitir que as operações de leitura ocorram simultaneamente com operações de escrita dentro de uma única divisória. Ao contrário de outros níveis de isolamento, o isolamento instantâneo garante que todas as leituras vêem uma imagem consistente dos dados mesmo quando estão a ocorrer atualizações – essencialmente devolvendo os últimos valores comprometidos enquanto uma transação de atualização está a ser processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gestão da concordância no armazenamento blob

Pode optar por utilizar modelos de conuncy otimistas ou pessimistas para gerir o acesso a bolhas e contentores no serviço Blob. Se não especificar explicitamente uma estratégia que escreve pela última vez, é o padrão.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Concordância otimista para bolhas e contentores

O serviço de armazenamento atribui um identificador a todos os objetos armazenados. Este identificador é atualizado sempre que uma operação de atualização é realizada num objeto. O identificador é devolvido ao cliente como parte de uma resposta HTTP GET utilizando o cabeçalho ETag (etiqueta de entidade) que é definido dentro do protocolo HTTP. Um utilizador que realize uma atualização sobre tal objeto pode enviar o ETag original juntamente com um cabeçalho condicional para garantir que uma atualização só ocorrerá se uma determinada condição tiver sido cumprida – neste caso, a condição é um cabeçalho "If-Match", que requer que o Serviço de Armazenamento garanta que o valor do ETag especificado no pedido de atualização é o mesmo que o armazenado no Serviço de Armazenamento.  

O esboço deste processo é o seguinte:  

1. Recupere uma bolha do serviço de armazenamento, a resposta inclui um valor http ETag Header que identifica a versão atual do objeto no serviço de armazenamento.
2. Quando atualizar a bolha, inclua o valor ETag que recebeu no passo 1 no cabeçalho condicional **If-Match** do pedido que envia para o serviço.
3. O serviço compara o valor ETag no pedido com o valor ETag atual da bolha.
4. Se o valor ETag atual da bolha for uma versão diferente do ETag no cabeçalho condicional **If-Match** no pedido, o serviço devolve um erro 412 ao cliente. Isto indica ao cliente que outro processo atualizou a bolha desde que o cliente o recuperou.
5. Se o valor ETag atual da bolha for a mesma versão que o ETag no cabeçalho condicional **If-Match** no pedido, o serviço executa a operação solicitada e atualiza o valor ETag atual da bolha para mostrar que criou uma nova versão.  

O seguinte corte C# (utilizando a Biblioteca de Armazenamento do Cliente 4.2.0) mostra um exemplo simples de como construir uma **Se-Match AccessCondition** com base no valor ETag que é acedido a partir das propriedades de uma bolha que foi previamente recuperada ou inserida. Em seguida, utiliza o objeto **AccessCondition** quando atualiza a bolha: o objeto **AccessCondition** adiciona o **cabeçalho Se-Match** ao pedido. Se outro processo tiver atualizado a bolha, o serviço Blob devolve uma mensagem de estado HTTP 412 (Pré-condição Falhada). Você pode baixar a amostra completa aqui: [Managing Concurrency using Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

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

O Azure Storage também inclui suporte para cabeçalhos condicionais adicionais, tais como **If-Modificado-Since,** **If-Unmodified-Since** e **If-None-Match,** bem como combinações dos mesmos. Para obter mais informações, consulte [especificar cabeçalhos condicional para operações de serviço de bolhas](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

A tabela seguinte resume as operações do contentor que aceitam cabeçalhos condicional, como **o If-Match** no pedido e que devolvem um valor ETag na resposta.  

| Operação | Devolução do valor ETag do contentor | Aceita cabeçalhos condicional |
|:--- |:--- |:--- |
| Criar Recipiente |Sim |Não |
| Obter propriedades de contentores |Sim |Não |
| Obtenha metadados de contentores |Sim |Não |
| Conjunto de metadados de contentores |Sim |Sim |
| Obter contentor ACL |Sim |Não |
| Conjunto de recipiente ACL |Sim |Sim (*) |
| Eliminar Contentor |Não |Sim |
| Recipiente de Arrendamento |Sim |Sim |
| Blobs de lista |Não |Não |

(*) As permissões definidas pelo SetContainerACL estão em cache e as atualizações a estas permissões demoram 30 segundos a propagar-se durante as quais as atualizações do período não são garantidas como consistentes.  

A tabela seguinte resume as operações blob que aceitam cabeçalhos condicional como **o If-Match** no pedido e que devolvem um valor ETag na resposta.

| Operação | Valor ETag de retorna | Aceita cabeçalhos condicional |
|:--- |:--- |:--- |
| Coloque Blob |Sim |Sim |
| Obter Blob |Sim |Sim |
| Get Blob Properties (Obter Propriedades do Blob) |Sim |Sim |
| Definir propriedades blob |Sim |Sim |
| Obtenha metadados blob |Sim |Sim |
| Definir metadados blob |Sim |Sim |
| Bolha de arrendamento (*) |Sim |Sim |
| Blob de Instantâneo |Sim |Sim |
| Bolha de cópia |Sim |Sim (para a fonte e a bolha de destino) |
| Abortar Bolha de Cópia |Não |Não |
| Eliminar Blob |Não |Sim |
| Colocar Bloco |Não |Não |
| Colocar lista de blocos |Sim |Sim |
| Obter Lista de Blocos |Sim |Não |
| Colocar página |Sim |Sim |
| Obter Gamas de Páginas |Sim |Sim |

(*) O Lease Blob não altera o ETag numa bolha.  

### <a name="pessimistic-concurrency-for-blobs"></a>Concuência pessimista para bolhas

Para bloquear uma bolha para uso exclusivo, pode adquirir um [contrato de arrendamento.](https://msdn.microsoft.com/library/azure/ee691972.aspx) Ao adquirir um contrato de arrendamento, especifique por quanto tempo precisa do arrendamento: este pode ser entre 15 a 60 segundos ou infinito, o que equivale a uma fechadura exclusiva. Você pode renovar um contrato de arrendamento finito para estendê-lo, e você pode liberar qualquer arrendamento quando você terminar com ele. O serviço Blob liberta automaticamente locações finitas quando expiram.  

Os arrendamentos permitem apoiar diferentes estratégias de sincronização, incluindo escrita/leitura partilhada exclusiva, escrita exclusiva/leitura exclusiva e leitura partilhada/leitura exclusiva. Quando existe um arrendamento, o serviço de armazenamento executa as escritas exclusivas (colocar, definir e apagar operações), no entanto, garantir a exclusividade das operações de leitura requer que o desenvolvedor garanta que todas as aplicações do cliente utilizem um ID de locação e que apenas um cliente de cada vez tenha uma identificação de locação válida. Leia as operações que não incluem um ID de arrendamento resultam em leituras partilhadas.  

O seguinte corte C# mostra um exemplo de adquirir um contrato de arrendamento exclusivo por 30 segundos numa bolha, atualizar o conteúdo da bolha e, em seguida, liberar o arrendamento. Se já houver um arrendamento válido na bolha quando tentar adquirir um novo contrato de arrendamento, o serviço Blob devolve um resultado de estado "HTTP (409) Conflict". O seguinte snippet usa um objeto **AccessCondition** para encapsular a informação de locação quando faz um pedido para atualizar a bolha no serviço de armazenamento.  Você pode baixar a amostra completa aqui: [Managing Concurrency using Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Se tentar uma operação de escrita numa bolha alugada sem passar a identificação do contrato de arrendamento, o pedido falha com um erro 412. Note que se o contrato de arrendamento expirar antes de ligar para o método **UploadText** mas ainda assim passar o ID do arrendamento, o pedido também falha com um erro **412.** Para obter mais informações sobre a gestão dos prazos de validade do contrato de arrendamento e iDs de locação, consulte a documentação [do Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST.  

As seguintes operações blob podem utilizar contratos de arrendamento para gerir a concordância pessimista:  

* Coloque Blob
* Obter Blob
* Get Blob Properties (Obter Propriedades do Blob)
* Definir propriedades blob
* Obtenha metadados blob
* Definir metadados blob
* Eliminar Blob
* Colocar Bloco
* Colocar lista de blocos
* Obter Lista de Blocos
* Colocar página
* Obter Gamas de Páginas
* Snapshot Blob - alugar ID opcional se um contrato de arrendamento existe
* Copy Blob - ID de locação exigido se existe um arrendamento na bolha de destino
* Abort Copy Blob - ID de arrendamento necessário se existe um arrendamento infinito na bolha de destino
* Lease Blob (Blob de Concessão)  

### <a name="pessimistic-concurrency-for-containers"></a>Concordância pessimista para contentores

As locações em contentores permitem que as mesmas estratégias de sincronização sejam suportadas como nas bolhas (escrita/leitura partilhada exclusiva, leitura exclusiva/leitura exclusiva e leitura partilhada/leitura exclusiva) no entanto, ao contrário de blobs, o serviço de armazenamento apenas impõe exclusividade nas operações de eliminação. Para eliminar um recipiente com um arrendamento ativo, um cliente deve incluir o ID de locação ativa com o pedido de eliminação. Todas as outras operações de contentores são bem sucedidas num contentor alugado sem incluir a identificação do arrendamento, caso em que são operações partilhadas. Se for necessária exclusividade de atualização (colocação ou conjunto) ou operações de leitura, os desenvolvedores devem garantir que todos os clientes utilizem um ID de locação e que apenas um cliente de cada vez tenha um ID de locação válido.  

As seguintes operações de contentores podem utilizar locações para gerir a concordância pessimista:  

* Eliminar Contentor
* Obter propriedades de contentores
* Obtenha metadados de contentores
* Conjunto de metadados de contentores
* Obter contentor ACL
* Conjunto de recipiente ACL
* Recipiente de Arrendamento  

Para obter mais informações, consulte:  

* [Specifying Conditional Headers for Blob Service Operations](https://msdn.microsoft.com/library/azure/dd179371.aspx) (Especificar Cabeçalhos Condicionais para Operações do Serviço Blob)
* [Recipiente de Arrendamento](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) (Blob de Concessão)

## <a name="managing-concurrency-in-table-storage"></a>Gestão da concordância no armazenamento de mesa

O serviço Table utiliza verificações de concordância otimistas como o comportamento padrão quando está a trabalhar com entidades, ao contrário do serviço Blob onde deve explicitamente optar por realizar verificações de concordância otimistas. A outra diferença entre a mesa e os serviços Blob é que só pode gerir o comportamento de conuncy das entidades, enquanto que com o serviço Blob pode gerir a concordância de ambos os contentores e bolhas.  

Para utilizar a concordância otimista e para verificar se outro processo modificou uma entidade desde que o recuperou do serviço de armazenamento de mesa, pode utilizar o valor ETag que recebe quando o serviço de mesa devolver uma entidade. O esboço deste processo é o seguinte:  

1. Recuperar uma entidade do serviço de armazenamento de mesa, a resposta inclui um valor ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2. Ao atualizar a entidade, inclua o valor ETag que recebeu no passo 1 no cabeçalho **If-Match** obrigatório do pedido que envia para o serviço.
3. O serviço compara o valor ETag no pedido com o valor ETag atual da entidade.
4. Se o valor ETag atual da entidade for diferente do ETag no cabeçalho **If-Match** obrigatório no pedido, o serviço devolve um erro 412 ao cliente. Isto indica ao cliente que outro processo atualizou a entidade desde que o cliente o recuperou.
5. Se o valor ETag atual da entidade for o mesmo que o ETag no cabeçalho **If-Match** obrigatório no pedido ou o cabeçalho **If-Match** contém o caráter wildcard (*), o serviço executa a operação solicitada e atualiza o valor ETag atual da entidade para mostrar que foi atualizado.  

Note que, ao contrário do serviço Blob, o serviço de mesa requer que o cliente inclua um cabeçalho **If-Match** em pedidos de atualização. No entanto, é possível forçar uma atualização incondicional (o último escritor ganha estratégia) e contornar verificações de concordância se o cliente definir o cabeçalho **If-Match** para o personagem wildcard (*) no pedido.  

O seguinte snippet C# mostra uma entidade do cliente que foi previamente criada ou recuperada tendo o seu endereço de e-mail atualizado. A operação inicial de inserção ou recuperação armazena o valor ETag no objeto do cliente, e como a amostra utiliza a mesma instância do objeto quando executa a operação de substituição, envia automaticamente o valor ETag de volta para o serviço de mesa, permitindo que o serviço verifique se há violações de concordância. Se outro processo tiver atualizado a entidade no armazenamento de mesas, o serviço devolve uma mensagem de estado HTTP 412 (Pré-condição Falhada).  Você pode baixar a amostra completa aqui: [Managing Concurrency using Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Para desativar explicitamente a verificação de concordância, deverá definir a propriedade **ETag** do objeto **do empregado** para "*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

O quadro que se segue resume a forma como as operações da entidade de tabela utilizam valores ETag:

| Operação | Valor ETag de retorna | Requer If-Match cabeçalho de pedido |
|:--- |:--- |:--- |
| Entidades de Consulta |Sim |Não |
| Inserir Entidade |Sim |Não |
| Entidade de Atualização |Sim |Sim |
| Entidade de Fusão |Sim |Sim |
| Eliminar Entidade |Não |Sim |
| Inserir ou Substituir Entidade |Sim |Não |
| Inserir ou Fundir Entidade |Sim |Não |

Note que as operações **de Inserção ou Substituição** de Entidade e **Inserção ou Fusão** *não* efetuam quaisquer verificações de concordância porque não enviam um valor ETag para o serviço de tabela.  

Em geral, os desenvolvedores que utilizam tabelas devem confiar na concordância otimista no desenvolvimento de aplicações escaláveis. Se for necessário um bloqueio pessimista, uma abordagem que os desenvolvedores podem ter ao aceder às Tabelas é atribuir uma bolha designada para cada mesa e tentar arrendar a bolha antes de operar em cima da mesa. Esta abordagem requer a aplicação para garantir que todas as vias de acesso aos dados obtenham o arrendamento antes de operar em cima da mesa. Deve também ter em conta que o tempo mínimo de locação é de 15 segundos, o que requer uma cuidadosa consideração pela escalabilidade.  

Para obter mais informações, consulte:  

* [Operações em Entidades](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gestão da Concurrency no Serviço de Fila

Um dos cenários em que a concordância é uma preocupação no serviço de fila é onde vários clientes estão a recuperar mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recibo pop, que é necessário para apagar a mensagem. A mensagem não é automaticamente apagada da fila, mas depois de recuperada, não é visível para outros clientes pelo intervalo de tempo especificado pelo parâmetro de visibilidade. Espera-se que o cliente que recupere a mensagem apague a mensagem depois de processada, e antes do tempo especificado pelo elemento TimeNextVisible da resposta, que é calculado com base no valor do parâmetro de visibilidade. O valor do tempo de visibilidade é adicionado ao momento em que a mensagem é recuperada para determinar o valor do TimeNextVisible.  

O serviço de fila não tem suporte para uma concordância otimista ou pessimista e, por isso, os clientes que processam mensagens recuperadas de uma fila devem garantir que as mensagens são processadas de forma idempotente. Uma última estratégia de vitórias é usada para operações de atualização como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

* [API REST de Serviço de Filas](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Receber mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Gestão da concordância em Ficheiros Azure

O serviço de ficheiros pode ser acedido utilizando dois pontos finais de protocolo diferentes – SMB e REST. O serviço REST não tem suporte para bloqueio otimista ou bloqueio pessimista e todas as atualizações seguirão uma estratégia de vitórias do último escritor. Os clientes SMB que montam ações de ficheiros podem alavancar mecanismos de bloqueio do sistema de ficheiros para gerir o acesso a ficheiros partilhados – incluindo a capacidade de realizar bloqueios pessimistas. Quando um cliente SMB abre um ficheiro, especifica tanto o modo de acesso ao ficheiro como o modo de partilha. A definição de uma opção de acesso a ficheiros de "Escrever" ou "Ler/Escrever" juntamente com um modo de partilha de ficheiros de "Nenhum" resultará na bloqueio do ficheiro por um cliente SMB até que o ficheiro seja fechado. Se a operação REST for tentada num ficheiro em que um cliente SMB tem o ficheiro bloqueado, o serviço REST devolverá o código de estado 409 (Conflito) com código de erro SharingViolation.  

Quando um cliente SMB abre um ficheiro para apagar, marca o ficheiro como pendente de exclusão até que todos os outros clientes SMB abram as pegas desse ficheiro. Enquanto um ficheiro estiver marcado como eliminação pendente, qualquer operação REST nesse ficheiro devolverá o código de estado 409 (Conflito) com o código de erro SMBDeletePending. O código de estado 404 (Não Encontrado) não é devolvido, uma vez que é possível ao cliente SMB remover a bandeira de eliminação pendente antes de fechar o ficheiro. Por outras palavras, o código de estado 404 (Não Encontrado) só é esperado quando o ficheiro tiver sido removido. Note que enquanto um ficheiro estiver num estado de exclusão pendente de SMB, este não será incluído nos resultados dos Ficheiros de Lista. Além disso, note que as operações DE EXCLUIR FICHEIRO e REST Delete Diretório são comprometidas atomicamente e não resultam num estado de exclusão pendente.  

Para obter mais informações, consulte:  

* [Gestão de fechaduras de ficheiros](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Passos seguintes

Para a aplicação completa da amostra referenciada neste blog:  

* [Gestão da Concurrency utilizando o Azure Storage - Aplicação da amostra](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre o Azure Storage consulte:  

* [Página inicial do armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Armazenamento Começando para [Blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [Mesa,](../../cosmos-db/table-storage-how-to-use-dotnet.md)Filas e [Ficheiros](../storage-dotnet-how-to-use-queues.md) [Files](../storage-dotnet-how-to-use-files.md)
* Arquitetura de Armazenamento – [Armazenamento Azure: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)

