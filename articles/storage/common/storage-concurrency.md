---
title: Gerir a Simultaneidade no Armazenamento do Microsoft Azure
description: Como gerir a simultaneidade para os serviços de Blob, fila, tabela e ficheiro
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.subservice: common
ms.openlocfilehash: c45061db77c21b82744f69f00265870d5e1a8d00
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883846"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerir a Simultaneidade no Armazenamento do Microsoft Azure
## <a name="overview"></a>Descrição geral
Aplicativos modernos de Internet, com base normalmente têm vários utilizadores, ver e atualizar dados em simultâneo. Isto requer que os desenvolvedores de aplicativos pensar com cuidado sobre como fornecer uma experiência previsível aos usuários finais, especialmente para cenários onde vários utilizadores podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principal que os desenvolvedores normalmente a considerar:  

1. A simultaneidade otimista – uma aplicação a efetuar uma atualização como parte da sua atualização verificará se os dados foram alterados desde o aplicativo pela última leitura desses dados. Por exemplo, se dois utilizadores visualizar uma página wiki fazem uma atualização para a mesma página, em seguida, a plataforma de wiki tem de garantir que a segunda atualização não substitui a primeira atualização – e que ambos os utilizadores compreendem se a sua atualização foi concluída com êxito ou não. Esta estratégia é usada com mais freqüência em aplicativos web.
2. Simultaneidade pessimista – uma aplicação à procura para realizar uma atualização irá demorar um bloqueio num objeto de impedir que outros utilizadores atualizar os dados até que o bloqueio seja liberado. Por exemplo, num cenário de replicação de dados mestre/subordinado em que apenas o mestre irá efetuar atualizações o mestre normalmente manterá um bloqueio exclusivo por um longo período de tempo dos dados para garantir que ninguém mais pode atualizá-la.
3. Última wins de escritor – uma abordagem que permite que qualquer operação de atualização continuar sem verificar se qualquer outro aplicativo atualizou os dados como o aplicativo em primeiro lugar, ler os dados. Esta estratégia (ou falta de uma estratégia de formal) é normalmente utilizada em que os dados estão particionados de forma que não existe nenhuma possibilidade de que vários usuários acessarão os mesmos dados. Também pode ser útil em que estão a ser processados fluxos de dados de curta duração.  

Este artigo fornece uma visão geral de como a plataforma de armazenamento do Azure simplifica o desenvolvimento ao fornecer suporte de primeira classe para todas as três essas estratégias de simultaneidade.  

## <a name="azure-storage--simplifies-cloud-development"></a>O armazenamento do Azure – simplifica o desenvolvimento da Cloud
O serviço de armazenamento do Azure suporta todas as três estratégias, embora seja distinta em sua capacidade de fornecer suporte total para a simultaneidade otimista e pessimista como ele foi projetado para adotar um modelo de consistência forte, que garante que, ao Serviço de armazenamento compromete-se uma inserção de dados ou operação de atualização de todos os acessos ainda mais para que os dados irão ver a atualização mais recente. Plataformas de armazenamento que utilizam um modelo de consistência eventual têm um atraso entre quando uma gravação é realizada por um utilizador e quando os dados atualizados podem ser vistos por outros utilizadores complicar, portanto, o desenvolvimento de aplicativos de cliente para evitar inconsistências de afetar os utilizadores finais.  

Além de selecionar uma estratégia de simultaneidade apropriado, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as alterações – particularmente alterações para o mesmo objeto em transações. O serviço de armazenamento do Azure utiliza o isolamento do instantâneo para permitir operações de leitura ocorrer simultaneamente com operações de escrita dentro de uma única partição. Ao contrário de outros níveis de isolamento, o isolamento do instantâneo garante que todas as leituras veem um instantâneo consistente dos dados, mesmo quando as atualizações estão a ocorrer – basicamente, ao devolver os valores de compromisso última durante uma atualização transação está a ser processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gerir a simultaneidade no armazenamento de BLOBs
Pode optar por usar qualquer um dos modelos de simultaneidade otimista ou pessimista para gerir o acesso a blobs e contentores no serviço de Blobs. Se não especificar explicitamente um wins de escritas de última de estratégia é a predefinição.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade otimista para blobs e contentores
O serviço de armazenamento atribui um identificador a cada objeto armazenado. Este identificador é atualizado sempre que uma operação de atualização é realizada num objeto. O identificador é retornado ao cliente como parte de uma resposta de HTTP GET usando o cabeçalho ETag (etiqueta de entidade) que é definido no protocolo HTTP. Um utilizador executar uma atualização num objeto desse tipo pode enviar a ETag original, juntamente com um cabeçalho condicional garantir que uma atualização só ocorrerá se uma determinada condição tiver sido cumprida – nesse caso, a condição é um cabeçalho de "If-Match", que requer o t do serviço de armazenamento o Certifique-se o valor de ETag especificado no pedido de atualização é o mesmo que armazenados no serviço de armazenamento.  

A estrutura desse processo é o seguinte:  

1. Obter um blob do serviço de armazenamento, a resposta inclui um valor de cabeçalho ETag de HTTP que identifica a versão atual do objeto no serviço de armazenamento.
2. Ao atualizar o blob, incluir o valor de ETag recebido na etapa 1 da **If-Match** condicional cabeçalho do pedido, enviar para o serviço.
3. O serviço compara o valor de ETag no pedido com o valor de ETag atual do blob.
4. Se o valor de ETag atual do blob é uma versão diferente a ETag no **If-Match** condicional cabeçalho na solicitação, o serviço retorna um erro de 412 para o cliente. Isto indica ao cliente que outro processo tiver atualizado o blob, uma vez que o cliente obtido.
5. Se o valor de ETag atual do blob é a mesma versão que a ETag no **If-Match** condicional cabeçalho na solicitação, o serviço realiza a operação pedida e atualiza o valor de ETag atual do blob a mostrar que foi criado uma nova versão.  

O c# trecho a seguir (usando a biblioteca de armazenamento do cliente 4.2.0) mostra um exemplo simples de como construir uma **If-Match AccessCondition** baseado no valor de ETag que é acedido a partir das propriedades de um blob que foi anteriormente obter ou inserido. Depois, usa o **AccessCondition** objeto quando ele atualiza o blob: a **AccessCondition** objeto adiciona o **If-Match** cabeçalho para o pedido. Se outro processo tiver atualizado o blob, o serviço blob devolve uma mensagem de estado 412 HTTP (falha de pré-condição). Pode baixar o exemplo completo aqui: [Gerir a simultaneidade com o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
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

O serviço de armazenamento também inclui suporte para cabeçalhos condicionais adicionais, como **If-Modified-Since**, **If-sem modificações-Since** e **If-None-Match** , bem como combinações delas. Para obter mais informações, consulte [especificando condicional cabeçalhos para operações de serviço Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.  

A tabela seguinte resume as operações de contentor, como a cabeçalhos condicionais de aceitação **If-Match** na solicitação e que retornar um valor de ETag na resposta.  

| Operação | Devolve o valor de ETag do contentor | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Criar contentor |Sim |Não |
| Obter as propriedades do contentor |Sim |Não |
| Obter metadados do contentor |Sim |Não |
| Metadados do conjunto de contentor |Sim |Sim |
| Obter o contentor ACL |Sim |Não |
| Definir o contentor de ACL |Sim |Sim (*) |
| Eliminar contentor |Não |Sim |
| Contentor de concessão |Sim |Sim |
| Listar os Blobs |Não |Não |

(*) As permissões definidas pelo SetContainerACL são colocadas em cache e atualizações para estas permissões que demore 30 segundos para propagar durante o período de atualizações não são garantidas consistentes.  

A tabela seguinte resume as operações de BLOBs, tal como a cabeçalhos condicionais de aceitação **If-Match** na solicitação e que retornar um valor de ETag na resposta.

| Operação | Devolve o valor de ETag | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Colocar o Blob |Sim |Sim |
| Obter o Blob |Sim |Sim |
| Obter as propriedades do Blob |Sim |Sim |
| Definir as propriedades do Blob |Sim |Sim |
| Obter Metadados do Blob |Sim |Sim |
| Definir metadados do Blob |Sim |Sim |
| Blob de concessão (*) |Sim |Sim |
| Blob de instantâneo |Sim |Sim |
| Copiar Blob |Sim |Sim (para o blob de origem e de destino) |
| Abortar o Blob de cópia |Não |Não |
| Eliminar Blob |Não |Sim |
| Colocar o bloco |Não |Não |
| Colocar a lista de bloqueios |Sim |Sim |
| Obter a lista de bloqueios |Sim |Não |
| Colocar a página |Sim |Sim |
| Os intervalos obter páginas |Sim |Sim |

(*) Blob de concessão não altera a ETag num blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para blobs
Para bloquear um blob para uso exclusivo, pode adquirir uma [concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx) nele. Quando adquire uma concessão, que especificar durante quanto tempo tem da concessão: Isto pode ser de entre 15 a 60 segundos ou infinito, o que equivale a um bloqueio exclusivo. Pode renovar uma concessão finita estendê-lo e pode libertar qualquer concessão quando tiver terminado com ele. O serviço blob automaticamente libera concessões finitos quando expiram.  

Concessões ativar estratégias de sincronização diferentes para ter suporte, incluindo escrita exclusivo / partilhado a escrita de leitura e exclusiva / exclusiva de ler e partilhados escrita / leitura exclusiva. Sempre que existe uma concessão o serviço de armazenamento impõe escritas exclusivas (colocar, defina e operações de eliminação) no entanto garantir a exclusividade para operações de leitura requer que o desenvolvedor garantir que todas as aplicações de cliente utilizam um ID de concessão e que o cliente apenas um por vez tem um ID de concessão válido. Operações de leitura que não inclua um resultado de ID de concessão no leituras partilhadas.  

O seguinte trecho do c# mostra um exemplo de adquirir uma concessão exclusiva durante 30 segundos num blob, a atualizar o conteúdo do blob e, em seguida, libertar a concessão. Se já existe uma concessão válida no blob ao tentar adquirir uma concessão de novo, o serviço blob devolve um resultado de estado de "Conflito do HTTP (409)". O fragmento seguinte utiliza uma **AccessCondition** objeto para encapsular as informações de concessão quando faz um pedido para atualizar o blob no serviço de armazenamento.  Pode baixar o exemplo completo aqui: [Gerir a simultaneidade com o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Se tentar uma operação de escrita num blob de concessão sem passar o ID da concessão, o pedido falha com um erro de 412. Tenha em atenção que se a concessão expirar antes de chamar o **UploadText** método mas ainda transmitir o ID da concessão, o pedido também falha com um **412** erro. Para obter mais informações sobre a gestão de tempos de expiração da concessão e concessão IDs, consulte a [Blob de concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx) documentação REST.  

As seguintes operações de BLOBs podem utilizar as concessões para gerir a simultaneidade pessimista:  

* Colocar o Blob
* Obter o Blob
* Obter as propriedades do Blob
* Definir as propriedades do Blob
* Obter Metadados do Blob
* Definir metadados do Blob
* Eliminar Blob
* Colocar o bloco
* Colocar a lista de bloqueios
* Obter a lista de bloqueios
* Colocar a página
* Os intervalos obter páginas
* Blob de instantâneo - o ID da concessão opcional se existe uma concessão
* ID da concessão do Blob de cópia - necessária se existe uma concessão no blob de destino
* Necessário de ID de concessão de Blob de cópia de abortar - se uma concessão infinita existe no blob de destino
* Blob de concessão  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista para contentores
Concessões em contentores de ativar as mesmas estratégias de sincronização ser suportado como blobs (exclusiva de escrita / partilhado a escrita de leitura e exclusiva / exclusiva de ler e partilhados escrita / leitura exclusiva) no entanto ao contrário dos blobs o serviço de armazenamento apenas impõe exclusividade no operações de eliminação. Para eliminar um contentor com uma concessão ativa, um cliente tem de incluir o ID de concessão ativa com o pedido de eliminação. Todas as outras operações de contentor com êxito num contêiner de concessão sem incluir o ID da concessão caso em que são partilhados operações. Se for necessária a exclusividade de atualização (put ou conjunto) ou operações de leitura, em seguida, os desenvolvedores devem certificar-se que todos os clientes utilizem uma ID da concessão e que apenas um cliente cada vez tem um ID de concessão válido.  

As seguintes operações de contentor podem utilizar as concessões para gerir a simultaneidade pessimista:  

* Eliminar contentor
* Obter as propriedades do contentor
* Obter metadados do contentor
* Metadados do conjunto de contentor
* Obter o contentor ACL
* Definir o contentor de ACL
* Contentor de concessão  

Para obter mais informações, consulte:  

* [Especificar cabeçalhos condicionais para operações de serviço Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Contentor de concessão](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blob de concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gerir a simultaneidade no serviço tabela
O serviço de tabela usa otimista simultaneidade verifica, enquanto o comportamento predefinido quando estiver a trabalhar com entidades, ao contrário do serviço de BLOBs onde explicitamente deve optar por executar verificações de simultaneidade otimista. A outra diferença entre os serviços de blob e tabela é que só pode gerir o comportamento de simultaneidade de entidades, ao passo que com o serviço blob pode gerir a simultaneidade de contentores e blobs.  

Para usar a simultaneidade otimista e para verificar se outro processo modificado de uma entidade, uma vez que obteve a partir do serviço de armazenamento de tabela, pode utilizar o valor de ETag que recebe quando o serviço de tabela retorna uma entidade. A estrutura desse processo é o seguinte:  

1. Recuperar uma entidade a partir do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado com essa entidade no serviço de armazenamento.
2. Ao atualizar a entidade, incluir o valor de ETag recebido na etapa 1 em obrigatórias **If-Match** cabeçalho do pedido, enviar para o serviço.
3. O serviço compara o valor de ETag no pedido com o valor de ETag atual da entidade.
4. Se o valor de ETag atual da entidade é diferente da ETag no obrigatórias **If-Match** cabeçalho na solicitação, o serviço retorna um erro de 412 para o cliente. Isto indica ao cliente que outro processo tenha atualizado a entidade, uma vez que o cliente obtido.
5. Se o valor de ETag atual da entidade é o mesmo que a ETag no obrigatórias **If-Match** cabeçalho no pedido ou o **If-Match** cabeçalho contém o caráter universal (*), o serviço efetua a a operação pedida e atualiza o valor de ETag atual da entidade para mostrar que foi atualizado.  

Tenha em atenção que, ao contrário do serviço blob, serviço tabela requer o cliente incluir uma **If-Match** cabeçalho em pedidos de atualização. No entanto, é possível forçar um incondicional atualizar (último escritor wins estratégia) e ignorar as verificações de simultaneidade, se o cliente estabelece o **If-Match** cabeçalho para o caráter universal (*) no pedido.  

O seguinte trecho do c# mostra uma entidade de cliente que foi anteriormente criada ou obtidos com o respetivo endereço de e-mail atualizado. Inicial inserir ou recuperar o valor de ETag no objeto de cliente de arquivos de operação e como o exemplo usa a mesma instância de objeto, quando executa a operação de substituição, envia automaticamente o valor de ETag volta para o serviço de tabela, permitindo que o serviço verificar a existência de violações de simultaneidade. Se outro processo tem de atualizar a entidade no armazenamento de tabelas, o serviço devolve uma mensagem de estado 412 HTTP (falha de pré-condição).  Pode baixar o exemplo completo aqui: [Gerir a simultaneidade com o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Para desativar explicitamente a verificação de simultaneidade, deve definir os **ETag** propriedade da **funcionário** de objeto para "*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

A tabela seguinte resume como as operações de entidade de tabela utilizam valores de ETag:

| Operação | Devolve o valor de ETag | Requer o cabeçalho de pedido de If-Match |
|:--- |:--- |:--- |
| Consultar entidades |Sim |Não |
| Inserir Entidade |Sim |Não |
| Atualizar a entidade |Sim |Sim |
| Intercalar Entidade |Sim |Sim |
| Eliminar Entidade |Não |Sim |
| Inserir ou Substituir Entidade |Sim |Não |
| Inserir ou Intercalar Entidade |Sim |Não |

Tenha em atenção que o **Insert ou entidade de substituir** e **Insert ou intercalar entidade** fazer operações *não* efetuar qualquer verificação de simultaneidade, porque eles não enviar um valor de ETag para a tabela serviço.  

Em geral os desenvolvedores que usam tabelas deverá depender de simultaneidade otimista durante o desenvolvimento de aplicativos escalonáveis. Se necessitar de bloqueio pessimista, os desenvolvedores de uma abordagem podem demorar quando aceder a tabelas é atribuir um blob designado para cada tabela e tentar tirar uma concessão no blob antes de operar na tabela. Essa abordagem requer o aplicativo para garantir que todos os caminhos de acesso de dados obter a concessão antes da operação na tabela. Também deve observar que o tempo de concessão mínimo é 15 segundos, que requer consideração cuidadosa para escalabilidade.  

Para obter mais informações, consulte:  

* [Operações em entidades](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerir a simultaneidade no serviço fila
Um cenário que no qual a simultaneidade é uma preocupação no serviço de colocação em fila é onde vários clientes são obtenção de uma fila de mensagens. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de receção de pop, que é necessário para excluir a mensagem. A mensagem não é automaticamente eliminada da fila, mas depois de ter sido recuperada, não é visível para outros clientes para o intervalo de tempo especificado pelo parâmetro visibilitytimeout. O cliente que obtém a mensagem deve eliminar a mensagem depois de terem sido processados e antes da hora especificada pelo TimeNextVisible o elemento da resposta, que é calculado com base no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado ao tempo em que a mensagem é recuperada para determinar o valor de TimeNextVisible.  

O serviço de fila não tem suporte para a simultaneidade otimista ou pessimista e para isso obtidos a partir de uma fila de mensagens de processamento de clientes de motivo devem certificar-se as mensagens são processadas de forma idempotentes. Uma estratégia de wins último escritor é utilizada para operações de atualização como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

* [API de REST do serviço de fila](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gerir a simultaneidade no serviço ficheiro
O serviço de ficheiros pode ser acedido através de dois, protocolo diferentes pontos finais – SMB e REST. O serviço REST não tem suporte para o bloqueio otimista ou pessimista bloqueio e todas as atualizações irão seguir uma estratégia de wins último escritor. Clientes SMB que montagem partilhas de ficheiros podem tirar partido dos mecanismos de bloqueio de sistema de ficheiros para gerir o acesso aos ficheiros partilhados – incluindo a capacidade de executar o bloqueio pessimista. Quando um cliente do SMB abre um ficheiro, especifica o acesso a ficheiros e a partilha de modo. Definir uma opção de acesso a ficheiros de "Gravação" ou "Leitura/escrita" juntamente com um modo de partilha de ficheiros de "None" irá resultar no ficheiro que está a ser bloqueado por um cliente do SMB, até que o ficheiro seja fechado. Se a operação REST for tentada num ficheiro em que um cliente do SMB tem o arquivo bloqueado o serviço REST irá devolver o código de estado 409 (conflito) com o código de erro SharingViolation.  

Quando um cliente do SMB abre um ficheiro para eliminar, marca o ficheiro como pendentes delete até que todos os outros clientes SMB são fechados identificadores abertos nesse ficheiro. Embora um ficheiro é marcado como pendentes delete, qualquer operação REST nesse ficheiro irá devolver o código de estado 409 (conflito) com o código de erro SMBDeletePending. Não é devolvido o código de estado 404 (não encontrado), uma vez que é possível que o cliente SMB remover o sinalizador de eliminação pendente antes de fechar o ficheiro. Em outras palavras, o código de estado 404 (não encontrado) é esperado apenas quando o ficheiro foi removido. Tenha em atenção que enquanto um arquivo está em estado de eliminação pendente SMB, ele não será incluído nos resultados da lista de ficheiros. Além disso, tenha em atenção que as operações REST eliminar ficheiro e diretório de eliminar REST são confirmadas atomicamente e resulta num Estado de eliminação pendente.  

Para obter mais informações, consulte:  

* [Gerir ficheiros bloqueia](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumo e os passos seguintes
O serviço de armazenamento do Microsoft Azure foi concebido para satisfazer as necessidades das aplicações mais complexas online sem forçar os desenvolvedores a comprometer ou repensar suposições de design de chave, como a simultaneidade e consistência dos dados que podem levar concedido.  

Para a aplicação de exemplo completo referenciada neste blogue:  

* [Gerir a simultaneidade com o armazenamento do Azure - aplicação de exemplo](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre armazenamento do Azure, veja:  

* [Página inicial de armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Armazenamento de introdução para [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabela](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../storage-dotnet-how-to-use-queues.md), e [ficheiros](../storage-dotnet-how-to-use-files.md)
* Arquitetura de armazenamento – [armazenamento do Azure: Um serviço de armazenamento de Cloud de elevada disponibilidade com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

