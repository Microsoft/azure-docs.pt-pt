---
title: Criptografia do lado do cliente com .NET para Armazenamento do Microsoft Azure | Microsoft Docs
description: A biblioteca de cliente de armazenamento do Azure para .NET dá suporte à criptografia do lado do cliente e à integração com Azure Key Vault para obter segurança máxima para seus aplicativos de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 921ea148c12a23ece47688a26743e1195caf52f4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003793"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Criptografia do lado do cliente e Azure Key Vault para Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
A [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client) dá suporte à criptografia de dados em aplicativos cliente antes do carregamento no armazenamento do Azure e à descriptografia de dados durante o download para o cliente. A biblioteca também dá suporte à integração com o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves da conta de armazenamento.

Para obter um tutorial passo a passo que conduz o processo de criptografia de BLOBs usando criptografia do lado do cliente e Azure Key Vault, consulte [criptografar e descriptografar BLOBs no armazenamento do Microsoft Azure usando Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Para criptografia do lado do cliente com Java, consulte [criptografia do lado do cliente com Java para armazenamento do Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia por meio da técnica de envelope
Os processos de criptografia e descriptografia seguem a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia por meio da técnica de envelope
A criptografia por meio da técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente de armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo), que é uma chave simétrica de uso único.
2. Os dados do usuário são criptografados usando este CEK.
3. O CEK é então encapsulado (criptografado) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciado localmente ou armazenado em cofres de chaves do Azure.
   
    A própria biblioteca do cliente de armazenamento nunca tem acesso ao KEK. A biblioteca invoca o algoritmo de encapsulamento de chave que é fornecido pelo Key Vault. Os usuários podem optar por usar provedores personalizados para encapsulamento/desencapsulamento de chave, se desejado.

4. Os dados criptografados são então carregados no serviço de armazenamento do Azure. A chave encapsulada junto com alguns metadados de criptografia adicionais é armazenada como metadados (em um blob) ou interpolada com os dados criptografados (mensagens de fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia por meio da técnica de envelope
A descriptografia por meio da técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente pressupõe que o usuário está gerenciando a chave de criptografia de chave (KEK) localmente ou em cofres de chaves do Azure. O usuário não precisa saber a chave específica que foi usada para criptografia. Em vez disso, um resolvedor de chave que resolve diferentes identificadores de chave para chaves pode ser configurado e usado.
2. A biblioteca de cliente baixa os dados criptografados junto com qualquer material de criptografia armazenado no serviço.
3. A chave de criptografia de conteúdo encapsulado (CEK) é desencapsulada (descriptografada) usando a chave de criptografia de chave (KEK). Novamente, a biblioteca de cliente não tem acesso ao KEK. Ele simplesmente invoca o algoritmo de desempacotamento personalizado ou de Key Vault do provedor.
4. A CEK (chave de criptografia de conteúdo) é usada para descriptografar os dados de usuário criptografados.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa o [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar os dados do usuário. Especificamente, o modo [CBC (encadeamento de bloco de codificação)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de maneira um pouco diferente, então discutiremos cada um deles aqui.

### <a name="blobs"></a>Blobs
Atualmente, a biblioteca de cliente dá suporte apenas à criptografia de BLOBs inteiros. Especificamente, há suporte para a criptografia quando os usuários usam os métodos **uploadfrom** ou o método **OpenWrite** . Para downloads, há suporte para downloads completos e de intervalo.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatória (IV) de 16 bytes, junto com uma chave de criptografia de conteúdo aleatória (CEK) de 32 bytes e executará a criptografia de envelope dos dados de BLOB usando essas informações. O CEK encapsulado e alguns metadados de criptografia adicionais são armazenados como metadados de blob junto com o blob criptografado no serviço.

> [!WARNING]
> Se você estiver editando ou carregando seus próprios metadados para o blob, será necessário garantir que esses metadados sejam preservados. Se você carregar novos metadados sem esses metadados, o CEK encapsulado, o IV e outros metadados serão perdidos e o conteúdo do blob nunca será recuperável novamente.
> 
> 

O download de um blob criptografado envolve a recuperação do conteúdo do blob inteiro usando os métodos de conveniência **downloadto**/**BlobReadStream** . O CEK encapsulado é desencapsulado e usado junto com o IV (armazenado como metadados de blob nesse caso) para retornar os dados descriptografados aos usuários.

O download de um intervalo arbitrário (métodos**métodos downloadrange** ) no blob criptografado envolve o ajuste do intervalo fornecido pelos usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado.

Todos os tipos de BLOB (BLOBs de blocos, blobs de páginas e blobs de acréscimo) podem ser criptografados/descriptografados usando este esquema.

### <a name="queues"></a>Filas
Como as mensagens da fila podem ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia de conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa a criptografia de envelope do texto da mensagem da fila usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são adicionados à mensagem da fila criptografada. Essa mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante a descriptografia, a chave encapsulada é extraída da mensagem da fila e desencapsulada. O IV também é extraído da mensagem da fila e usado junto com a chave desencapsulada para descriptografar os dados da mensagem da fila. Observe que os metadados de criptografia são pequenos (menos de 500 bytes), portanto, embora ele seja contado para o limite de 64 KB para uma mensagem da fila, o impacto deve ser gerenciável.

### <a name="tables"></a>Tabelas
A biblioteca de cliente dá suporte à criptografia de propriedades de entidade para operações de inserção e substituição.

> [!NOTE]
> Merge não é atualmente suportada. Uma vez que um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente as novas propriedades de mesclagem e atualizar os metadados resultará na perda de dados. Intercalar o requer a fazer chamadas de serviço extra para ler a entidade já existente do serviço ou usando uma nova chave por propriedade, que não são adequados por motivos de desempenho.
> 
> 

A criptografia de dados de tabela funciona da seguinte maneira:  

1. Os usuários especificam as propriedades a serem criptografadas.
2. A biblioteca de cliente gera um vetor de inicialização aleatória (IV) de 16 bytes juntamente com uma CEK (chave de criptografia de conteúdo aleatória) de 32 bytes para cada entidade e executa a criptografia de envelope nas propriedades individuais a serem criptografadas derivando um novo IV por propriedade. A Propriedade Encrypted é armazenada como dados binários.
3. O CEK encapsulado e alguns metadados de criptografia adicionais são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém as informações sobre o IV, a versão e a chave encapsulada. A segunda propriedade reservada (_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são criptografadas. As informações nessa segunda Propriedade (_ClientEncryptionMetadata2) são criptografadas em si.
4. Devido a essas propriedades reservadas adicionais necessárias para a criptografia, os usuários agora podem ter apenas 250 Propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser menor que 1 MB.

Observe que apenas as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades forem criptografados, eles deverão ser convertidos em cadeias de caracteres. As cadeias de caracteres encriptadas são armazenadas no serviço como propriedades binárias e elas são convertidas para cadeias de caracteres após desencriptação.

Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito, qualquer um dos especificando um atributo [EncryptProperty] (para entidades POCO que derivam de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que assume uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Observe que não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

### <a name="batch-operations"></a>Operações em lote
Em operações em lote, o mesmo KEK será usado em todas as linhas nessa operação de lote porque a biblioteca de cliente só permite um objeto de opções (e, portanto, uma política/KEK) por operação em lote. No entanto, a biblioteca de cliente gerará internamente um novo IV aleatório e CEK aleatórios por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação no lote definindo esse comportamento no resolvedor de criptografia.

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades são criptografadas, você não pode executar consultas que filtram uma propriedade criptografada.  Se você tentar, os resultados estarão incorretos, pois o serviço estaria tentando comparar dados criptografados com dados não criptografados.
> 
> 
> Para executar operações de consulta, você deve especificar um resolvedor de chave capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida para um provedor, a biblioteca do cliente gerará um erro. Para qualquer consulta que executa projeções do lado do servidor, a biblioteca de cliente adicionará as propriedades especiais de metadados de criptografia (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por padrão às colunas selecionadas.

## <a name="azure-key-vault"></a>Azure Key Vault
O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Usando Azure Key Vault, os usuários podem criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para obter mais informações, consulte [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md).

A biblioteca de cliente de armazenamento usa a biblioteca de núcleos de Key Vault para fornecer uma estrutura comum no Azure para o gerenciamento de chaves. Os usuários também têm o benefício adicional de usar a biblioteca de extensões de Key Vault. A biblioteca de extensões fornece funcionalidade útil em relação a provedores de chave de nuvem e local simétricos e simples e contínuos, bem como com agregação e cache.

### <a name="interface-and-dependencies"></a>Interface e dependências
Há três pacotes de Key Vault:

* Microsoft. Azure. keyvault. Core contém IKey e IKeyResolver. É um pacote pequeno sem dependências. A biblioteca de cliente de armazenamento para .NET define como uma dependência.
* Microsoft. Azure. keyvault contém o cliente REST Key Vault.
* Microsoft. Azure. keyvault. Extensions contém o código de extensão que inclui implementações de algoritmos de criptografia e um RSAKey e um SymmetricKey. Depende dos namespaces principal e keyvault e fornece funcionalidade para definir um resolvedor de agregação (quando os usuários desejam usar vários provedores de chaves) e um resolvedor de chave de cache. Embora a biblioteca de cliente de armazenamento não dependa diretamente desse pacote, se os usuários quiserem usar Azure Key Vault para armazenar suas chaves ou usar as extensões de Key Vault para consumir os provedores criptográficos locais e na nuvem, eles precisarão desse pacote.

O Key Vault é projetado para chaves mestras de valor alto e limites de limitação por Key Vault são projetados com isso em mente. Ao executar a criptografia do lado do cliente com o Key Vault, o modelo preferido é usar chaves mestras simétricas armazenadas como segredos em Key Vault e armazenados em cache localmente. Os usuários devem fazer o seguinte:

1. Crie um segredo offline e carregue-o no Key Vault.
2. Use o identificador base do segredo como um parâmetro para resolver a versão atual do segredo para criptografia e armazenar essas informações em cache localmente. Usar CachingKeyResolver para cache; os usuários não devem implementar sua própria lógica de cache.
3. Use o resolvedor de cache como uma entrada ao criar a política de criptografia.

Mais informações sobre o uso de Key Vault podem ser encontradas nos [exemplos de código de criptografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Melhores práticas
O suporte à criptografia está disponível somente na biblioteca de cliente de armazenamento para .NET. Atualmente, Windows Phone e Windows Runtime não dão suporte à criptografia.

> [!IMPORTANT]
> Esteja atento a esses pontos importantes ao usar a criptografia do lado do cliente:
> 
> * Ao ler ou gravar em um blob criptografado, use comandos de carregamento de blob inteiros e comandos de download de blob completo e de intervalo. Evite gravar em um blob criptografado usando operações de protocolo como colocar bloco, colocar lista de blocos, gravar páginas, limpar páginas ou anexar bloco; caso contrário, você pode corromper o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar Propriedades criptografadas sem Atualizar os metadados de criptografia.
> * Se você definir metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, pois a definição de metadados não é aditiva. Isso também é verdadeiro para instantâneos; Evite especificar metadados ao criar um instantâneo de um blob criptografado. Se os metadados devem ser definidos, certifique-se de chamar primeiro o método **fetchattributes** para obter os metadados de criptografia atuais e evitar gravações simultâneas enquanto os metadados estão sendo definidos.
> * Habilite a propriedade **RequireEncryption** nas opções de solicitação padrão para usuários que devem funcionar apenas com dados criptografados. Consulte abaixo para obter mais informações.
> 
> 

## <a name="client-api--interface"></a>API/interface do cliente
Ao criar um objeto EncryptionPolicy, os usuários podem fornecer apenas uma chave (implementando IKey), apenas um resolvedor (implementando IKeyResolver) ou ambos. IKey é o tipo de chave básica que é identificado usando um identificador de chave e que fornece a lógica para encapsular/desencapsular. IKeyResolver é usado para resolver uma chave durante o processo de descriptografia. Ele define um método ResolveKey que retorna um IKey dado um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias chaves que são gerenciadas em vários locais.

* Para criptografia, a chave é usada sempre e a ausência de uma chave resultará em um erro.
* Para descriptografia:
  * O resolvedor de chave é invocado se especificado para obter a chave. Se o resolvedor for especificado, mas não tiver um mapeamento para o identificador de chave, um erro será gerado.
  * Se o resolvedor não for especificado, mas uma chave for especificada, a chave será usada se seu identificador corresponder ao identificador de chave necessário. Se o identificador não corresponder, um erro será gerado.

Os exemplos de código neste artigo demonstram como definir uma política de criptografia e trabalhar com dados criptografados, mas não demonstram trabalhar com Azure Key Vault. Os [exemplos de criptografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) no GitHub demonstram um cenário de ponta a ponta mais detalhado para BLOBs, filas e tabelas, juntamente com a integração do Key Vault.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Opcionalmente, os usuários podem habilitar um modo de operação em que todos os uploads e downloads devem ser criptografados. Nesse modo, tentativas de carregar dados sem uma política de criptografia ou baixar dados que não estão criptografados no serviço falharão no cliente. A propriedade **RequireEncryption** do objeto de opções de solicitação controla esse comportamento. Se seu aplicativo for criptografar todos os objetos armazenados no armazenamento do Azure, você poderá definir a propriedade **RequireEncryption** nas opções de solicitação padrão para o objeto de cliente de serviço. Por exemplo, defina **CloudBlobClient. Defaultsolicitoptions. RequireEncryption** como **true** para exigir criptografia para todas as operações de blob executadas por meio desse objeto de cliente.


### <a name="blob-service-encryption"></a>Criptografia do serviço blob
Crie um objeto **BlobEncryptionPolicy** e defina-o nas opções de solicitação (por API ou em um nível de cliente usando **defaultrequestoptions**). Todo o resto será manipulado pela biblioteca de cliente internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>serviço Fila criptografia
Crie um objeto **QueueEncryptionPolicy** e defina-o nas opções de solicitação (por API ou em um nível de cliente usando **defaultrequestoptions**). Todo o resto será manipulado pela biblioteca de cliente internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Criptografia do serviço tabela
Além de criar uma política de criptografia e defini-la nas opções de solicitação, você deve especificar um **EncryptionResolver** em **TableRequestOptions**ou definir o atributo [encryptproperty] na entidade.

#### <a name="using-the-resolver"></a>Usando o resolvedor

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Usando atributos
Conforme mencionado acima, se a entidade implementar TableEntity, as propriedades poderão ser decoradas com o atributo [Encryptproperty] em vez de especificar o **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Criptografia e desempenho
Observe que criptografar os dados de armazenamento resulta em sobrecarga de desempenho adicional. A chave de conteúdo e a IV devem ser geradas, o conteúdo em si deve ser criptografado e os metadados adicionais devem ser formatados e carregados. Essa sobrecarga variará dependendo da quantidade de dados que estão sendo criptografados. Recomendamos que os clientes sempre testem seus aplicativos quanto ao desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes
* [Tutorial: Criptografar e descriptografar BLOBs no Armazenamento do Microsoft Azure usando Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Baixar a [biblioteca de cliente do armazenamento do Azure para o pacote NuGet do .net](https://www.nuget.org/packages/WindowsAzure.Storage)
* Baixar os pacotes Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)e [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Visite a [documentação do Azure Key Vault](../../key-vault/key-vault-overview.md)
