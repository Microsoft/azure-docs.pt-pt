---
title: Gerir a simultaneidade
titleSuffix: Azure Storage
description: Saiba como gerenciar a simultaneidade para os serviços BLOB, fila, tabela e arquivo.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460480"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerir a Simultaneidade no Armazenamento do Microsoft Azure

Aplicativos modernos baseados na Internet normalmente têm vários usuários exibindo e atualizando dados simultaneamente. Isso exige que os desenvolvedores de aplicativos pensem atentamente sobre como fornecer uma experiência previsível para seus usuários finais, especialmente para cenários em que vários usuários podem atualizar os mesmos dados. Há três estratégias principais de simultaneidade de dados que os desenvolvedores normalmente consideram:  

1. Simultaneidade otimista – um aplicativo que executa uma atualização fará parte de sua atualização verificar se os dados foram alterados desde que o aplicativo leu a última vez esses dados. Por exemplo, se dois usuários que visualizam uma página wiki fizerem uma atualização para a mesma página, a plataforma wiki deverá garantir que a segunda atualização não substitua a primeira atualização – e que os dois usuários entendam se a atualização foi bem-sucedida ou não. Essa estratégia é usada com mais frequência em aplicativos Web.
2. Simultaneidade pessimista – um aplicativo que procura executar uma atualização usará um bloqueio em um objeto impedindo que outros usuários atualizem os dados até que o bloqueio seja liberado. Por exemplo, em um cenário de replicação de dados mestre/subordinado em que somente o mestre executará atualizações, o mestre normalmente manterá um bloqueio exclusivo por um período de tempo estendido nos dados para garantir que ninguém mais possa atualizá-lo.
3. Último gravador vence – uma abordagem que permite que todas as operações de atualização continuem sem verificar se algum outro aplicativo atualizou os dados desde que o aplicativo leu primeiro os dados. Normalmente, essa estratégia (ou falta de uma estratégia formal) é usada quando os dados são particionados de forma que não haja nenhuma probabilidade de que vários usuários acessem os mesmos dados. Ele também pode ser útil onde os fluxos de dados de curta duração estão sendo processados.  

Este artigo fornece uma visão geral de como a plataforma de armazenamento do Azure simplifica o desenvolvimento fornecendo suporte de primeira classe para todas as três estratégias de simultaneidade.  

## <a name="azure-storage-simplifies-cloud-development"></a>O armazenamento do Azure simplifica o desenvolvimento na nuvem

O serviço de armazenamento do Azure dá suporte a todas as três estratégias, embora seja distintivo em sua capacidade de fornecer suporte completo para simultaneidade otimista e pessimista porque foi projetada para adotar um modelo de consistência forte que garante que, quando o O serviço de armazenamento confirma uma operação de atualização ou inserção de dados, todos os acessos adicionais a esses dados verão a atualização mais recente. As plataformas de armazenamento que usam um modelo de consistência eventual têm um atraso entre quando uma gravação é executada por um usuário e quando os dados atualizados podem ser vistos por outros usuários, o que complica o desenvolvimento de aplicativos cliente para evitar inconsistências de afetar os usuários finais.  

Além de selecionar uma estratégia de simultaneidade apropriada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as alterações – particularmente as alterações no mesmo objeto entre as transações. O serviço de armazenamento do Azure usa o isolamento de instantâneo para permitir que as operações de leitura ocorram simultaneamente com operações de gravação em uma única partição. Ao contrário de outros níveis de isolamento, o isolamento de instantâneo garante que todas as leituras vejam um instantâneo consistente dos dados mesmo enquanto as atualizações estão ocorrendo – basicamente, retornando os últimos valores confirmados enquanto uma transação de atualização está sendo processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gerenciando a simultaneidade no armazenamento de BLOBs

Você pode optar por usar modelos de simultaneidade otimista ou pessimista para gerenciar o acesso a BLOBs e contêineres no serviço BLOB. Se você não especificar explicitamente uma estratégia, a última gravação vence será o padrão.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade otimista para BLOBs e contêineres

O serviço de armazenamento atribui um identificador a todos os objetos armazenados. Esse identificador é atualizado toda vez que uma operação de atualização é executada em um objeto. O identificador é retornado ao cliente como parte de uma resposta HTTP GET usando o cabeçalho ETag (marca de entidade) que é definido no protocolo HTTP. Um usuário que executa uma atualização em tal objeto pode enviar na ETag original juntamente com um cabeçalho condicional para garantir que uma atualização só ocorra se uma determinada condição for atendida – nesse caso, a condição é um cabeçalho "If-Match", que requer o serviço de armazenamento t o garante que o valor da ETag especificado na solicitação de atualização seja o mesmo que o armazenado no serviço de armazenamento.  

O contorno desse processo é o seguinte:  

1. Recuperar um blob do serviço de armazenamento, a resposta inclui um valor de cabeçalho HTTP ETag que identifica a versão atual do objeto no serviço de armazenamento.
2. Ao atualizar o blob, inclua o valor de ETag recebido na etapa 1 no cabeçalho condicional **If-Match** da solicitação que você envia para o serviço.
3. O serviço compara o valor de ETag na solicitação com o valor de ETag atual do blob.
4. Se o valor de ETag atual do blob for uma versão diferente da ETag no cabeçalho condicional **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Isso indica ao cliente que outro processo atualizou o blob desde que o cliente o recuperou.
5. Se o valor de ETag atual do blob for a mesma versão que a ETag no cabeçalho condicional **If-Match** na solicitação, o serviço executará a operação solicitada e atualizará o valor de eTag atual do blob para mostrar que criou uma nova versão.  

O trecho C# a seguir (usando a biblioteca de armazenamento do cliente 4.2.0) mostra um exemplo simples de como construir um **AccessCondition If-Match** com base no valor de eTag que é acessado das propriedades de um blob que foi anteriormente recuperado ou inserido. Em seguida, ele usa o objeto **AccessCondition** quando atualiza o blob: o objeto **AccessCondition** adiciona o cabeçalho **If-Match** à solicitação. Se outro processo tiver atualizado o blob, o serviço blob retornará uma mensagem de status HTTP 412 (falha na pré-condição). Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

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

O armazenamento do Azure também inclui suporte para cabeçalhos condicionais adicionais, como **If-Modified-Since**, **If-inmodified-Since** e **If-None-Match** , bem como combinações das mesmas. Para obter mais informações, consulte [especificando cabeçalhos condicionais para operações do serviço blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

A tabela a seguir resume as operações de contêiner que aceitam cabeçalhos condicionais, como **If-Match** na solicitação e que retornam um valor de eTag na resposta.  

| Operação | Retorna o valor de ETag do contêiner | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Criar contentor |Sim |Não |
| Obter propriedades do contêiner |Sim |Não |
| Obter metadados do contêiner |Sim |Não |
| Definir metadados do contêiner |Sim |Sim |
| Obter ACL do contêiner |Sim |Não |
| Definir ACL do contêiner |Sim |Sim (*) |
| Eliminar contentor |Não |Sim |
| Contêiner de concessão |Sim |Sim |
| Listar BLOBs |Não |Não |

(*) As permissões definidas por SetContainerACL são armazenadas em cache e as atualizações para essas permissões levam 30 segundos para serem propagadas durante o qual não há garantia de que as atualizações de período sejam consistentes.  

A tabela a seguir resume as operações de BLOB que aceitam cabeçalhos condicionais, como **If-Match** na solicitação e que retornam um valor de eTag na resposta.

| Operação | Retorna o valor de ETag | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Colocar blob |Sim |Sim |
| Obter blob |Sim |Sim |
| Obter propriedades de BLOB |Sim |Sim |
| Definir propriedades de BLOB |Sim |Sim |
| Obter metadados de BLOB |Sim |Sim |
| Definir metadados de BLOB |Sim |Sim |
| Blob de concessão (*) |Sim |Sim |
| Blob de instantâneo |Sim |Sim |
| Copiar Blob |Sim |Sim (para o blob de origem e de destino) |
| Anular cópia de BLOB |Não |Não |
| Eliminar Blob |Não |Sim |
| Colocar bloco |Não |Não |
| Colocar lista de blocos |Sim |Sim |
| Obter lista de blocos |Sim |Não |
| Colocar página |Sim |Sim |
| Obter intervalos de página |Sim |Sim |

(*) O blob de concessão não altera a ETag em um blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para BLOBs

Para bloquear um blob para uso exclusivo, você pode adquirir uma [concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx) nele. Ao adquirir uma concessão, você especifica por quanto tempo precisa da concessão: isso pode ser entre 15 a 60 segundos ou infinito, que é o valor de um bloqueio exclusivo. Você pode renovar uma concessão finita para estendê-la e pode liberar qualquer concessão quando tiver terminado. O serviço blob lança automaticamente concessões finitas quando elas expiram.  

As concessões permitem que diferentes estratégias de sincronização tenham suporte, incluindo gravação exclusiva/leitura compartilhada, gravação exclusiva/leitura exclusiva e gravação compartilhada/leitura exclusiva. Quando uma concessão existe, o serviço de armazenamento impõe gravações exclusivas (operações put, set e Delete), no entanto, garantir que exclusividade para operações de leitura exija que o desenvolvedor garanta que todos os aplicativos cliente usem uma ID de concessão e que apenas um cliente de cada vez tenha um ID de concessão válida. As operações de leitura que não incluem uma ID de concessão resultam em leituras compartilhadas.  

O trecho C# a seguir mostra um exemplo de aquisição de uma concessão exclusiva por 30 segundos em um blob, atualizando o conteúdo do blob e, em seguida, liberando a concessão. Se já houver uma concessão válida no blob quando você tentar adquirir uma nova concessão, o serviço blob retornará um resultado de status "conflito de HTTP (409)". O trecho a seguir usa um objeto **AccessCondition** para encapsular as informações de concessão quando faz uma solicitação para atualizar o blob no serviço de armazenamento.  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Se você tentar uma operação de gravação em um blob concedido sem passar a ID de concessão, a solicitação falhará com um erro 412. Observe que, se a concessão expirar antes de chamar o método **UploadText** , mas você ainda passar a ID de concessão, a solicitação também falhará com um erro **412** . Para obter mais informações sobre como gerenciar tempos de expiração de concessão e IDs de concessão, consulte a documentação REST do [blob de concessão](https://msdn.microsoft.com/library/azure/ee691972.aspx) .  

As operações de blob a seguir podem usar concessões para gerenciar a simultaneidade pessimista:  

* Colocar blob
* Obter blob
* Obter propriedades de BLOB
* Definir propriedades de BLOB
* Obter metadados de BLOB
* Definir metadados de BLOB
* Eliminar Blob
* Colocar bloco
* Colocar lista de blocos
* Obter lista de blocos
* Colocar página
* Obter intervalos de página
* Instantâneo blob-ID de concessão opcional se houver uma concessão
* Copiar blob-ID de concessão necessária se houver uma concessão no blob de destino
* Anular cópia de blob-ID de concessão necessária se houver uma concessão infinita no blob de destino
* Lease Blob (Blob de Concessão)  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista para contêineres

As concessões em contêineres permitem que as mesmas estratégias de sincronização tenham suporte como em BLOBs (gravação exclusiva/leitura compartilhada, gravação exclusiva/leitura exclusiva e gravação compartilhada/leitura exclusiva), no entanto, ao contrário dos BLOBs, o serviço de armazenamento impõe apenas exclusividade em operações de exclusão. Para excluir um contêiner com uma concessão ativa, um cliente deve incluir a ID de concessão ativa com a solicitação de exclusão. Todas as outras operações de contêiner têm sucesso em um contêiner concedido sem incluir a ID de concessão, caso em que são operações compartilhadas. Se exclusividade de atualização (Put ou Set) ou operações de leitura forem necessárias, os desenvolvedores devem garantir que todos os clientes usem uma ID de concessão e que apenas um cliente de cada vez tenha uma ID de concessão válida.  

As operações de contêiner a seguir podem usar concessões para gerenciar a simultaneidade pessimista:  

* Eliminar contentor
* Obter propriedades do contêiner
* Obter metadados do contêiner
* Definir metadados do contêiner
* Obter ACL do contêiner
* Definir ACL do contêiner
* Contêiner de concessão  

Para obter mais informações, veja:  

* [Specifying Conditional Headers for Blob Service Operations](https://msdn.microsoft.com/library/azure/dd179371.aspx) (Especificar Cabeçalhos Condicionais para Operações do Serviço Blob)
* [Contêiner de concessão](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) (Blob de Concessão)

## <a name="managing-concurrency-in-table-storage"></a>Gerenciando a simultaneidade no armazenamento de tabelas

O serviço tabela usa verificações de simultaneidade otimista como o comportamento padrão quando você está trabalhando com entidades, diferentemente do serviço BLOB, onde você deve optar explicitamente por executar verificações de simultaneidade otimistas. A outra diferença entre os serviços de tabela e blob é que você só pode gerenciar o comportamento de simultaneidade de entidades, enquanto com o serviço BLOB, você pode gerenciar a simultaneidade de contêineres e blobs.  

Para usar a simultaneidade otimista e verificar se outro processo modificou uma entidade desde que você a recuperou do serviço de armazenamento de tabela, você pode usar o valor de ETag que você recebe quando o serviço tabela retorna uma entidade. O contorno desse processo é o seguinte:  

1. Recuperar uma entidade do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2. Quando você atualizar a entidade, inclua o valor de ETag recebido na etapa 1 no cabeçalho obrigatório **If-Match** da solicitação enviada para o serviço.
3. O serviço compara o valor de ETag na solicitação com o valor de ETag atual da entidade.
4. Se o valor de ETag atual da entidade for diferente do ETag no cabeçalho obrigatório **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Isso indica ao cliente que outro processo atualizou a entidade desde que o cliente a recuperou.
5. Se o valor de ETag atual da entidade for o mesmo que o ETag no cabeçalho obrigatório **If-Match** na solicitação ou o cabeçalho **If-Match** contiver o caractere curinga (*), o serviço executará a operação solicitada e atualizará o valor de eTag atual da entidade para mostrar que ela foi atualizada.  

Observe que, diferentemente do serviço BLOB, o serviço tabela requer que o cliente inclua um cabeçalho **If-Match** em solicitações de atualização. No entanto, é possível forçar uma atualização incondicional (estratégia do último gravador vence) e ignorar verificações de simultaneidade se o cliente definir o cabeçalho **If-Match** para o caractere curinga (*) na solicitação.  

O trecho C# a seguir mostra uma entidade de cliente que foi anteriormente criada ou recuperada com seu endereço de email atualizado. A operação de inserção ou de recuperação inicial armazena o valor de ETag no objeto Customer e, como o exemplo usa a mesma instância de objeto quando ele executa a operação Replace, ele envia automaticamente o valor de ETag de volta para o serviço tabela, permitindo que o serviço Verifique se há violações de simultaneidade. Se outro processo tiver atualizado a entidade no armazenamento de tabela, o serviço retornará uma mensagem de status HTTP 412 (falha na pré-condição).  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Para desabilitar explicitamente a verificação de simultaneidade, você deve definir a propriedade **ETag** do objeto **Employee** como "*" antes de executar a operação de substituição.  

```csharp
customer.ETag = "*";  
```

A tabela a seguir resume como as operações de entidade de tabela usam valores de ETag:

| Operação | Retorna o valor de ETag | Requer cabeçalho de solicitação If-Match |
|:--- |:--- |:--- |
| Consultar entidades |Sim |Não |
| Inserir entidade |Sim |Não |
| Atualizar entidade |Sim |Sim |
| Mesclar entidade |Sim |Sim |
| Excluir entidade |Não |Sim |
| Inserir ou substituir entidade |Sim |Não |
| Inserir ou mesclar entidade |Sim |Não |

Observe que as operações **Inserir ou substituir entidade** e **Inserir ou mesclar entidade** *não executam* nenhuma verificação de simultaneidade, pois não enviam um valor de eTag para o serviço tabela.  

Em geral, os desenvolvedores que usam tabelas devem contar com simultaneidade otimista ao desenvolver aplicativos escalonáveis. Se o bloqueio pessimista for necessário, uma abordagem que os desenvolvedores podem tomar ao acessar tabelas é atribuir um blob designado para cada tabela e tentar fazer uma concessão no blob antes de operar na tabela. Essa abordagem exige que o aplicativo garanta que todos os caminhos de acesso a dados obtenham a concessão antes de operar na tabela. Você também deve observar que o tempo mínimo de concessão é de 15 segundos, o que exige uma consideração cuidadosa para a escalabilidade.  

Para obter mais informações, veja:  

* [Operações em entidades](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerenciando a simultaneidade no serviço fila

Um cenário no qual a simultaneidade é uma preocupação no serviço de enfileiramento é onde vários clientes estão recuperando mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recebimento pop, que é necessário para excluir a mensagem. A mensagem não é excluída automaticamente da fila, mas depois de ser recuperada, ela não fica visível para outros clientes pelo intervalo de tempo especificado pelo parâmetro visibilitytimeout. Espera-se que o cliente que recupera a mensagem exclua a mensagem depois que ela foi processada e antes da hora especificada pelo elemento TimeNextVisible da resposta, que é calculada com base no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado à hora em que a mensagem é recuperada para determinar o valor de TimeNextVisible.  

O serviço fila não tem suporte para a simultaneidade otimista ou pessimista e, por esse motivo, os clientes que processam mensagens recuperadas de uma fila devem garantir que as mensagens sejam processadas de maneira idempotente. Uma estratégia do último gravador vence é usada para operações de atualização, como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, veja:  

* [API REST de Serviço de Filas](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Gerenciando a simultaneidade em arquivos do Azure

O serviço de arquivo pode ser acessado usando dois pontos de extremidade de protocolo diferentes – SMB e REST. O serviço REST não tem suporte para bloqueio otimista ou pessimista e todas as atualizações seguirão uma estratégia do último gravador vence. Os clientes SMB que montam os compartilhamentos de arquivos podem aproveitar os mecanismos de bloqueio do sistema de arquivos para gerenciar o acesso a arquivos compartilhados, incluindo a capacidade de executar o bloqueio pessimista. Quando um cliente SMB abre um arquivo, ele especifica o modo de compartilhamento e o acesso a arquivos. A definição de uma opção de acesso ao arquivo de "gravação" ou "leitura/gravação" junto com um modo de compartilhamento de arquivos "nenhum" fará com que o arquivo seja bloqueado por um cliente SMB até que o arquivo seja fechado. Se a operação REST for tentada em um arquivo em que um cliente SMB tem o arquivo bloqueado, o serviço REST retornará o código de status 409 (conflito) com o código de erro SharingViolation.  

Quando um cliente SMB abre um arquivo para exclusão, ele marca o arquivo como exclusão pendente até que todos os outros identificadores abertos do cliente SMB nesse arquivo sejam fechados. Enquanto um arquivo é marcado como exclusão pendente, qualquer operação REST nesse arquivo retornará o código de status 409 (conflito) com o código de erro SMBDeletePending. O código de status 404 (não encontrado) não é retornado, pois é possível que o cliente SMB remova o sinalizador de exclusão pendente antes de fechar o arquivo. Em outras palavras, o código de status 404 (não encontrado) só é esperado quando o arquivo foi removido. Observe que, enquanto um arquivo estiver em um estado de exclusão pendente de SMB, ele não será incluído nos resultados da lista de arquivos. Além disso, observe que as operações REST excluir arquivo e excluir diretório REST são confirmadas atomicamente e não resultam em um estado de exclusão pendente.  

Para obter mais informações, veja:  

* [Gerenciando bloqueios de arquivo](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Passos seguintes

Para o aplicativo de exemplo completo referenciado neste blog:  

* [Gerenciando a simultaneidade usando o armazenamento do Azure-aplicativo de exemplo](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre o armazenamento do Azure, consulte:  

* [Página inicial do Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Introdução de armazenamento para [blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabela](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../storage-dotnet-how-to-use-queues.md)e [arquivos](../storage-dotnet-how-to-use-files.md)
* Arquitetura de armazenamento – [armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

