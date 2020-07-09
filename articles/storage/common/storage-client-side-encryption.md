---
title: Encriptação do lado do cliente com .NET para Armazenamento Microsoft Azure / Microsoft Docs
description: A Biblioteca do Cliente de Armazenamento Azure para .NET suporta encriptação e integração do lado do cliente com o Azure Key Vault para máxima segurança para as suas aplicações de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 26592b94ce13f73192890601811d22b2fd06fbe2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105018"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Encriptação do lado do cliente e cofre de chave Azure para armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
A [Biblioteca do Cliente de Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) suporta encriptar dados dentro das aplicações do cliente antes de fazer o upload para o Azure Storage e desencriptar dados enquanto faz o download para o cliente. A biblioteca também suporta a integração com [o Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

Para um tutorial passo a passo que o conduz através do processo de encriptação de bolhas usando encriptação do lado do cliente e cofre de chave Azure, consulte [blobs encriptadas e desencriptadas no Microsoft Azure Storage usando o Cofre da Chave Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Para encriptação do lado do cliente com a Java, consulte [a encriptação do lado do cliente com a Java para o Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através da técnica do envelope
Os processos de encriptação e desencriptação seguem a técnica do envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica do envelope
A encriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca de clientes de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.
2. Os dados do utilizador são encriptados utilizando este CEK.
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em Cofres chave Azure.
   
    A biblioteca do cliente de armazenamento nunca tem acesso à KEK. A biblioteca invoca o algoritmo de embrulho chave que é fornecido pelo Key Vault. Os utilizadores podem optar por utilizar fornecedores personalizados para o embrulho/desembrulhar se desejar.

4. Os dados encriptados são então enviados para o serviço de Armazenamento Azure. A chave embrulhada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (numa bolha) ou interpolada com os dados encriptados (mensagens de fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através da técnica do envelope
A desencriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação (KEK) localmente ou em Azure Key Vaults. O utilizador não precisa de saber a chave específica que foi usada para encriptação. Em vez disso, pode ser configurado e utilizado um resolver-se de chave que resolva diferentes identificadores-chave para as chaves.
2. A biblioteca do cliente descarrega os dados encriptados juntamente com qualquer material de encriptação que seja armazenado no serviço.
3. A chave de encriptação de conteúdo embrulhado (CEK) é então desembrulhada (desencriptado) utilizando a chave de encriptação (KEK). Aqui novamente, a biblioteca de clientes não tem acesso à KEK. Simplesmente invoca o algoritmo de desembrulho do fornecedor de cofres personalizados ou do fornecedor key vault.
4. A chave de encriptação de conteúdo (CEK) é então usada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca do cliente de armazenamento utiliza [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados do utilizador. Especificamente, o modo [de cadeia de blocos cifra (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de forma um pouco diferente, por isso vamos discutir cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca do cliente suporta atualmente a encriptação de bolhas inteiras apenas. Especificamente, a encriptação é suportada quando os utilizadores usam os métodos **UploadFrom** ou o método **OpenWrite.** Para downloads, os downloads completos e de gama são suportados.

Durante a encriptação, a biblioteca do cliente gerará um Vetor de Inicialização aleatória (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes, e executará encriptação do envelope dos dados blob usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como metadados blob juntamente com a bolha encriptada no serviço.

> [!WARNING]
> Se estiver a editar ou a carregar os seus próprios metadados para a bolha, tem de se certificar de que estes metadados são preservados. Se carregar novos metadados sem estes metadados, perder-se-ão os CEK, IV e outros metadados embrulhados e o conteúdo blob nunca mais será recuperado.
> 
> 

O download de uma bolha encriptada envolve a recuperação do conteúdo de toda a bolha utilizando os métodos de conveniência **DownloadTo** / **BlobReadStream.** O CEK embrulhado é desembrulhado e utilizado juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

O download de uma gama arbitrária **(Métodos DownloadRange)** na bolha encriptada envolve ajustar a gama fornecida pelos utilizadores para obter uma pequena quantidade de dados adicionais que podem ser usados para desencriptar com sucesso a gama solicitada.

Todos os tipos de bolhas (blobs de blocos, bolhas de página e bolhas de apêndice) podem ser encriptados/desencriptados utilizando este esquema.

### <a name="queues"></a>Filas
Uma vez que as mensagens de fila podem ser de qualquer formato, a biblioteca do cliente define um formato personalizado que inclui o Vetor de Inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca do cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa encriptação de envelope do texto de mensagem de fila usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são adicionados à mensagem de fila encriptada. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Durante a desencriptação, a chave embrulhada é extraída da mensagem de fila e desembrulhada. O IV também é extraído da mensagem de fila e utilizado juntamente com a chave desembrulhada para desencriptar os dados da mensagem da fila. Note que os metadados de encriptação são pequenos (abaixo de 500 bytes), pelo que, embora conte para o limite de 64KB para uma mensagem de fila, o impacto deve ser gerível.

### <a name="tables"></a>Tabelas
A biblioteca do cliente suporta a encriptação das propriedades da entidade para inserir e substituir operações.

> [!NOTE]
> A fusão não é suportada atualmente. Uma vez que um subconjunto de propriedades pode ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resultará na perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente a partir do serviço, ou usar uma nova chave por propriedade, ambas as quais não são adequadas por razões de desempenho.
> 
> 

A encriptação de dados de tabela funciona da seguinte forma:  

1. Os utilizadores especificam as propriedades a encriptar.
2. A biblioteca do cliente gera um vetor de inicialização aleatória (IV) de 16 bytes juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes para cada entidade, e executa encriptação de envelope nas propriedades individuais a ser encriptada através da derivação de um novo IV por propriedade. A propriedade encriptada é armazenada como dados binários.
3. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cordas que detém a informação sobre IV, versão e chave embrulhada. A segunda propriedade reservada (_ClientEncryptionMetadata2) é uma propriedade binária que detém a informação sobre as propriedades que são encriptadas. A informação nesta segunda propriedade (_ClientEncryptionMetadata2) está por si própria encriptada.
4. Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores podem agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser inferior a 1 MB.

Note que apenas as propriedades de cordas podem ser encriptadas. Para que outros tipos de propriedades sejam encriptados, devem ser convertidos em cordas. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta em cordas após a desencriptação.

Para as tabelas, além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito especificando um atributo [EncryptProperty] (para entidades POCO que derivam da TableEntity) ou uma encriptação resolver nas opções de pedido. Um resolver de encriptação é um delegado que pega numa chave de partição, chave de linha e nome de propriedade e devolve um Boolean que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usará esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve para o fio. O delegado também prevê a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar a propriedade A; caso contrário, encriptar propriedades A e B.) Note que não é necessário fornecer esta informação durante a leitura ou consulta de entidades.

### <a name="batch-operations"></a>Operações de Lote
Nas operações de lote, o mesmo KEK será utilizado em todas as linhas dessa operação do lote, porque a biblioteca do cliente só permite um objeto de opções (e, portanto, uma política/KEK) por operação de lote. No entanto, a biblioteca do cliente irá gerar internamente um novo IV aleatório e um CEK aleatório por linha no lote. Os utilizadores também podem optar por encriptar diferentes propriedades para cada operação no lote, definindo este comportamento na encriptação.

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades estão encriptadas, não é possível executar consultas que filtram uma propriedade encriptada.  Se tentar, os resultados serão incorretos, porque o serviço estaria a tentar comparar dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, tem de especificar uma chave que seja capaz de resolver todas as teclas no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida a um fornecedor, a biblioteca do cliente lançará um erro. Para qualquer consulta que execute projeções do lado do servidor, a biblioteca do cliente adicionará as propriedades especiais de metadados de encriptação (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por padrão às colunas selecionadas.

## <a name="azure-key-vault"></a>Azure Key Vault
O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Ao utilizar o Azure Key Vault, os utilizadores podem encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para obter mais informações, veja [O que é o Azure Key Vault?](../../key-vault/general/overview.md)

A biblioteca do cliente de armazenamento utiliza a biblioteca central key vault de forma a fornecer uma estrutura comum em todo o Azure para gerir chaves. Os utilizadores também obtêm o benefício adicional de usar a biblioteca de extensões Key Vault. A biblioteca de extensões fornece uma funcionalidade útil em torno de fornecedores simples e sem costura symmetric/RSA locais e cloud key providers, bem como com agregação e caching.

### <a name="interface-and-dependencies"></a>Interface e dependências
Há três pacotes key vault:

* Microsoft.Azure.KeyVault.Core contém o IKey e o IKeyResolver. É um pequeno pacote sem dependências. A biblioteca de clientes de armazenamento para .NET define-a como uma dependência.
* Microsoft.Azure.KeyVault contém o cliente Key Vault REST.
* Microsoft.Azure.KeyVault.Extensions contém código de extensão que inclui implementações de algoritmos criptográficos e um RSAKey e uma SymmetricKey. Depende dos espaços de nomes Core e KeyVault e fornece funcionalidade para definir um conjunto de resolver (quando os utilizadores querem usar vários fornecedores chave) e uma chave de caching resolver. Embora a biblioteca do cliente de armazenamento não dependa diretamente deste pacote, se os utilizadores quiserem usar o Cofre chave Azure para armazenar as suas chaves ou para usar as extensões key Vault para consumir os fornecedores criptográficos locais e em nuvem, eles vão precisar deste pacote.

O Key Vault é projetado para chaves master de alto valor, e os limites de estrangulamento por Key Vault são projetados com isso em mente. Ao executar a encriptação do lado do cliente com o Key Vault, o modelo preferido é usar chaves principais simétricas armazenadas como segredos no Cofre de Chaves e em cache localmente. Os utilizadores devem fazer o seguinte:

1. Crie um segredo offline e faça o upload para o Key Vault.
2. Use o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para encriptação e cache esta informação localmente. Utilize o CachingKeyResolver para o caching; não se espera que os utilizadores implementem a sua própria lógica de caching.
3. Utilize o resolver do caching como uma entrada enquanto cria a política de encriptação.

Mais informações sobre a utilização do Key Vault podem ser encontradas nas amostras de [código de encriptação.](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)

## <a name="best-practices"></a>Melhores práticas
O suporte de encriptação só está disponível na biblioteca do cliente de armazenamento para .NET. O Windows Phone e o Windows Runtime não suportam atualmente a encriptação.

> [!IMPORTANT]
> Esteja atento a estes pontos importantes ao utilizar a encriptação do lado do cliente:
> 
> * Ao ler ou escrever para uma bolha encriptada, utilize comandos inteiros de upload de blob e comandos de descarregamento de gama/blob inteiro. Evite escrever para uma bolha encriptada utilizando operações de protocolo como Put Block, Put Block List, Write Pages, Clear Pages ou Append Block; caso contrário, pode corromper a bolha encriptada e torná-la ilegível.
> * Para as tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar as propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir metadados na bolha encriptada, poderá substituir os metadados relacionados com encriptação necessários para a desencriptação, uma vez que a definição de metadados não é aditivo. Isto também é verdade para instantâneos; evite especificar metadados enquanto cria uma imagem instantânea de uma bolha encriptada. Se os metadados embora tão devem ser definidos, certifique-se de que liga primeiro para o método **FetchAttributes** para obter os metadados de encriptação atuais e evite escrever simultâneos enquanto os metadados estão a ser definidos.
> * Ativar a propriedade **RequireEncryption** nas opções de pedido padrão para utilizadores que devem funcionar apenas com dados encriptados. Veja abaixo mais informações.
> 
> 

## <a name="client-api--interface"></a>API / Interface do Cliente
Ao criar um objeto EncryptionPolicy, os utilizadores podem fornecer apenas uma Chave (implementando o IKey), apenas um resolver (implementando iKeyResolver), ou ambos. IKey é o tipo de chave básica que é identificado usando um identificador chave e que fornece a lógica para embrulhar/desembrulhar. IKeyResolver é usado para resolver uma chave durante o processo de desencriptação. Define um método ResolveKey que devolve um IKey dado um identificador chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias teclas que são geridas em vários locais.

* Para encriptação, a chave é sempre utilizada e a ausência de uma chave resultará num erro.
* Para desencriptação:
  * A chave é invocada se especificada para obter a chave. Se o resolver for especificado mas não tiver um mapeamento para o identificador de chave, é lançado um erro.
  * Se o resolver não for especificado, mas uma chave for especificada, a chave é utilizada se o seu identificador corresponder ao identificador de teclas necessário. Se o identificador não corresponder, é lançado um erro.

Os exemplos de código neste artigo demonstram a definição de uma política de encriptação e o trabalho com dados encriptados, mas não demonstram trabalhar com o Azure Key Vault. As amostras de [encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) no GitHub demonstram um cenário mais detalhado de ponta a ponta para bolhas, filas e tabelas, juntamente com a integração do Key Vault.

### <a name="requireencryption-mode"></a>Requereno modo desencriptação
Os utilizadores podem opcionalmente ativar um modo de funcionamento onde todos os uploads e downloads devem ser encriptados. Neste modo, as tentativas de carregar dados sem uma política de encriptação ou de descarregar dados que não sejam encriptados no serviço falharão no cliente. A **propriedade requereencrição** do objeto de opções de pedido controla este comportamento. Se a sua aplicação encriptar todos os objetos armazenados no Azure Storage, então pode definir a propriedade **RequireEncrypation** nas opções de pedido predefinido para o objeto do cliente de serviço. Por exemplo, definir **CloudBlobClient.DefaultRequestOptions.RequireEncryption** **para** exigir encriptação para todas as operações blob realizadas através desse objeto do cliente.


### <a name="blob-service-encryption"></a>Encriptação do serviço blob
Crie um objeto **BlobEncrycryptionPolicy** e coloque-o nas opções de pedido (por API ou a nível de cliente, utilizando **Opições Descodiástas Padrão).** Todo o resto será tratado pela biblioteca do cliente internamente.

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

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila
Crie um objeto **QueueEncrypationPolicy** e desafiá-lo nas opções de pedido (por API ou a nível de cliente, utilizando **Opições Descodiástas Padrão).** Todo o resto será tratado pela biblioteca do cliente internamente.

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

### <a name="table-service-encryption"></a>Encriptação do serviço de mesa
Além de criar uma política de encriptação e defini-la nas opções de pedido, deve especificar um **EncriptaçãoResolver** em **Opções DeEsquest opções**de Tabela, ou definir o atributo [EncryptProperty] na entidade.

#### <a name="using-the-resolver"></a>Usando o resolver

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

#### <a name="using-attributes"></a>Utilização de atributos
Como mencionado acima, se a entidade implementar a TableEntity, então as propriedades podem ser decoradas com o atributo [EncryptProperty] em vez de especificar o **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Encriptação e desempenho
Note que encriptar os seus dados de armazenamento resulta em despesas adicionais de desempenho. A chave de conteúdo e IV devem ser gerados, o conteúdo em si deve ser encriptado, e os meta-dados adicionais devem ser formatados e carregados. Esta sobrecarga variará dependendo da quantidade de dados que estão a ser encriptados. Recomendamos que os clientes testem sempre as suas aplicações para desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximos passos
* [Tutorial: Criptografe e desencriptar bolhas no Microsoft Azure Storage usando o Cofre da Chave Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Descarregue a [Biblioteca do Cliente de Armazenamento Azure para pacote .NET NuGet](https://www.nuget.org/packages/WindowsAzure.Storage)
* Descarregue os pacotes Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Cliente](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)e [Extensões](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Visite a documentação do [cofre da chave Azure](../../key-vault/general/overview.md)
