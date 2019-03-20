---
title: Encriptação do lado do cliente com o Java para o armazenamento do Microsoft Azure | Documentos da Microsoft
description: Biblioteca de cliente de armazenamento do Azure para Java suporta a encriptação do lado do cliente e a integração com o Azure Key Vault para segurança máxima para as suas aplicações de armazenamento do Azure.
services: storage
author: lakasa
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 0a2088e603828a7850cb250c1874008d63fe9c89
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992459"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Encriptação do lado do cliente e o Azure Key Vault com o Java para o armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
O [biblioteca de clientes de armazenamento do Azure para Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) suporta a encriptação de dados dentro de aplicativos de cliente antes de carregar para o armazenamento do Azure e a desencriptação de dados durante a transferência para o cliente. A biblioteca também suporta a integração com [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação via a técnica de envelope
Os processos de criptografia e descriptografia siga a técnica de envelope.  

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica de envelope
Encriptação através da técnica de envelope funciona da seguinte forma:  

1. A biblioteca de cliente de armazenamento do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uma único uso.  
2. Dados de utilizador são encriptados utilizando este CEK.   
3. O CEK, em seguida, é encapsulado (encriptada) com a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada em cofres de chaves do Azure.  
   A biblioteca de cliente de armazenamento em si nunca tem acesso a KEK. A biblioteca invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os utilizadores podem optar por utilizar fornecedores personalizados para o encapsulamento de chave/abertura se assim o desejar.  
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. A chave encapsulada, juntamente com alguns metadados de encriptação adicional é armazenada como metadados (num blob) ou interpolada com os dados encriptados (fila de mensagens e entidades da tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação via a técnica de envelope
Desencriptação via a técnica de envelope funciona da seguinte forma:  

1. A biblioteca de cliente parte do princípio de que o utilizador está a gerir a chave de encriptação de chaves (KEK) local ou no Azure Key Vault. O usuário não precisa de conhecer a chave específica que foi utilizada para encriptação. Em vez disso, um resolvedor de chave que resolve diferentes identificadores de chaves para chaves pode ser configurado e utilizado.  
2. A biblioteca de cliente transfere os dados criptografados, juntamente com qualquer material de encriptação que é armazenado no serviço.  
3. A chave de encriptação de conteúdo encapsulada (CEK) é, em seguida, não encapsulada (descriptografado) usando a criptografia de chave de chaves (KEK). Aqui, novamente, a biblioteca de cliente não tem acesso a KEK. Ele simplesmente invoca o personalizado ou o algoritmo de abertura do fornecedor do Key Vault.  
4. A chave de encriptação de conteúdo (CEK), em seguida, é utilizada para desencriptar os dados de utilizador encriptado.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados de utilizador. Especificamente, [encadeamento de bloco de cifras (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modo com AES. Cada serviço funciona de forma um pouco diferente, então, vamos abordar cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de cliente atualmente suporta a encriptação de todos blobs apenas. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **carregue*** métodos ou o **openOutputStream** método. Para downloads, ambos completos e transferências de intervalo são suportadas.  

Durante a encriptação, a biblioteca de cliente irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes e efetuar a encriptação de envelope dos dados de blob usando essas informações. O CEK encapsulada e alguns metadados de encriptação adicional, em seguida, são armazenados como metadados, juntamente com o blob criptografado no serviço de Blobs.

> [!WARNING]
> Se estiver a edição ou seus próprios metadados para o blob a carregar, precisa garantir que estes metadados são preservados. Se carregar novos metadados sem estes metadados, o CEK encapsulada, IV e outros metadados serão perdidos e o conteúdo será nunca possível obter novamente.
> 
> 

Transferir um blob encriptado envolve a obtenção dos conteúdos do blob inteiro com o **baixe**/**openInputStream** métodos de conveniência. O CEK encapsulada é não envolto e utilizado em conjunto com o IV (armazenado como metadados do blob neste caso) para retornar os dados descriptografados para os utilizadores.

Transferir um intervalo de arbitrário (**downloadRange** métodos) no blob criptografado envolve a ajustar o intervalo fornecido por utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizados com êxito desencriptar a pedido intervalo.  

Todos os tipos de BLOBs (blobs de blocos, blobs de páginas e blobs de acréscimo) podem ser encriptados/descriptografados com esse esquema.

### <a name="queues"></a>Filas
Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o Vetor de inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.  

Durante a encriptação, a biblioteca de cliente gera um IV aleatória de 16 bytes, juntamente com um CEK aleatório de 32 bytes e realiza a encriptação de envelope do texto de mensagem de fila usando essas informações. O CEK encapsulada e alguns metadados de encriptação adicional, em seguida, são adicionados à mensagem de fila encriptados. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Durante a descriptografia, é que a chave encapsulada extraída da mensagem de fila e não envolto. O IV também é extraído da mensagem de fila e utilizado em conjunto com a chave não encapsulada para descriptografar os dados de mensagem de fila. Tenha em atenção que os metadados de encriptação são pequeno (em 500 bytes), portanto, enquanto ele contam para o limite de 64KB de uma mensagem de fila, o impacto deve ser gerido.

### <a name="tables"></a>Tabelas
A biblioteca de cliente suporta a encriptação de propriedades de entidade para inserir e substitua operações.

> [!NOTE]
> Merge não é atualmente suportada. Uma vez que um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente as novas propriedades de mesclagem e atualizar os metadados resultará na perda de dados. Intercalar o requer a fazer chamadas de serviço extra para ler a entidade já existente do serviço ou usando uma nova chave por propriedade, que não são adequados por motivos de desempenho.
> 
> 

Encriptação de dados de tabela funciona da seguinte forma:  

1. Os utilizadores especificar as propriedades sejam encriptados.  
2. A biblioteca de cliente gera um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes para cada entidade e efetua a encriptação de envelope sobre as propriedades individuais sejam encriptados, derivando uma nova IV por propriedade. A propriedade de encriptação é armazenada como dados binários.  
3. O CEK encapsulada e alguns metadados de encriptação adicional, em seguida, são armazenadas como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém as informações sobre o IV, versão e chave encapsulada. A propriedade reservada em segundo lugar (_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são encriptados. As informações nesta segunda propriedade (_ClientEncryptionMetadata2) são autocriptografada.  
4. Devido a essas propriedades reservadas adicionais necessárias para a encriptação, os utilizadores agora podem ter apenas 250 propriedades personalizadas, em vez de 252. O tamanho total da entidade tem de ser inferior a 1MB.  
   
   Tenha em atenção que apenas as propriedades de cadeia de caracteres podem ser encriptadas. Se outros tipos de propriedades estão a ser encriptada, eles devem ser convertidos em cadeias de caracteres. As cadeias de caracteres encriptadas são armazenadas no serviço como propriedades binárias e elas são convertidas para cadeias de caracteres após desencriptação.
   
   Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito, qualquer um dos especificando um atributo [Encrypt] (para entidades POCO que derivam de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que assume uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não é necessário fornecer estas informações ao ler ou consultar entidades.

### <a name="batch-operations"></a>Operações de lote
Em operações de lote, o mesmo KEK irá ser utilizada em todas as linhas nessa operação em lote porque a biblioteca de cliente permite que apenas um objeto de opções (e, por conseguinte, uma política/KEK) por operação em lote. No entanto, a biblioteca de cliente internamente gerará um novo IV aleatório e CEK aleatório por linha no lote. Os utilizadores também podem optar por encriptar as propriedades diferentes para cada operação no lote definindo esse comportamento no resolvedor de encriptação.

### <a name="queries"></a>Consultas
> [!NOTE]
> Uma vez que as entidades são encriptadas, não é possível executar consultas que filtrarão numa propriedade de encriptação.  Se tentar, os resultados serão incorretos, porque o serviço estaria tentando comparar os dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, tem de especificar um resolvedor de chave que é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca de cliente irá gerar um erro. Para qualquer consulta que executa as projeções de lado do servidor, a biblioteca de cliente irá adicionar as propriedades de metadados de encriptação especial (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por predefinição para as colunas selecionadas.

## <a name="azure-key-vault"></a>Azure Key Vault
O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Ao utilizar o Azure Key Vault, os usuários podem criptografar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSMs). Para obter mais informações, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-whatis.md).

A biblioteca de cliente de armazenamento utiliza a biblioteca principal do Key Vault, a fim de fornecer uma estrutura comum em todo o Azure para o gerenciamento de chaves. Os utilizadores também obtém o benefício adicional de utilizar a biblioteca de extensões do Key Vault. A biblioteca de extensões fornece uma funcionalidade útil em torno de simple e transparente Symmetric/RSA local e os principais fornecedores de serviços cloud, bem como com agregação e a colocação em cache.

### <a name="interface-and-dependencies"></a>Interface e dependências
Existem três pacotes de Key Vault:  

* core de Cofre de chaves do Azure contém a IKey e IKeyResolver. É um pequeno pacote sem dependências. A biblioteca de cliente de armazenamento para Java define-o como uma dependência.
* Cofre de chaves do Azure contém o cliente REST do Cofre de chave.  
* extensões do Cofre de chaves do Azure contém o código de extensão que inclui implementações de algoritmos criptográficos e um RSAKey e um SymmetricKey. Ele depende dos espaços de nomes principal e o Cofre de chaves e fornece a funcionalidade para definir um resolvedor de agregação (quando os usuários querem utilizar vários fornecedores de chaves) e uma resolução de chave de colocação em cache. Embora a biblioteca de cliente de armazenamento não diretamente depende este pacote, se os utilizadores pretenderem utilizar o Azure Key Vault para armazenar as chaves ou de utilizar as extensões de Key Vault para consumir o local e na cloud provedores criptográficos, precisará este pacote.  
  
  Key Vault foi concebido para chaves mestras de alto valor, e limites de limitação por Cofre de chaves foram projetado com isso em mente. Quando efetuar a encriptação do lado do cliente com o Key Vault, o modelo preferencial é utilizar chaves simétricas mestre armazenadas como segredos no Key Vault e armazenada em cache localmente. Os utilizadores têm de fazer o seguinte:  

1. Criar um segredo offline e carregue-o ao Cofre de chaves.  
2. Utilize o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para a encriptação e estas informações localmente em cache. Utilizar CachingKeyResolver para colocar em cache; os utilizadores não são esperados para implementar suas própria lógica de colocação em cache.  
3. Utilize o Resolvedor de colocação em cache como uma entrada ao criar a política de encriptação.
   Podem encontrar mais informações sobre a utilização do Cofre de chaves nos exemplos de código de criptografia. <fix URL>  

## <a name="best-practices"></a>Melhores práticas
Suporte de encriptação está disponível apenas na biblioteca de cliente de armazenamento para Java.

> [!IMPORTANT]
> Tenha em atenção os seguintes pontos importantes ao utilizar a encriptação do lado do cliente:
> 
> * Ao ler a partir de ou escrever para um blob encriptado, utilize os comandos de carregamento de BLOBs de todo e comandos de download de blob de intervalo/todo. Evitar gravar num blob encriptado a utilizar operações de protocolo, como a colocação de blocos, colocar a lista de bloqueios, páginas de escrever, páginas clara ou acrescentar bloco; caso contrário, pode danificar o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.  
> * Se definir metadados no blob criptografado, pode substituir os metadados relacionados com a encriptação necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isto também se aplica para instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob encriptado. Se os metadados têm de ser definidos, certifique-se de que chamar o **downloadAttributes** método pela primeira vez para obter os metadados de encriptação atual e evitar escritas simultâneas, enquanto estiver a ser definido metadados.  
> * Ativar a **requireEncryption** sinalizador nas opções de pedido predefinidas para os utilizadores que devem funcionar apenas com os dados encriptados. Veja abaixo para obter mais informações.  
> 
> 

## <a name="client-api--interface"></a>API de cliente / Interface
Ao criar um objeto de EncryptionPolicy, os utilizadores podem fornecer apenas uma chave (implementar a IKey), apenas um resolvedor (implementar IKeyResolver) ou ambos. IKey é o tipo de chave básico que é identificado com um identificador de chave e que fornece a lógica de encapsulamento de aplicações/abertura. IKeyResolver é utilizado para resolver uma chave durante o processo de descriptografia. Define um método de ResolveKey que retorna uma IKey tendo em conta um identificador de chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias chaves que são geridas em várias localizações.

* Para a encriptação, a chave é utilizada sempre e a ausência de uma chave irá resultar num erro.  
* Para desencriptação:  
  
  * O Resolvedor de chave é invocado se especificada para obter a chave. Se o resolvedor for especificado, mas não tem um mapeamento para o identificador de chave, ocorrerá um erro.  
  * Se o resolvedor não for especificado, mas é especificada uma chave, a chave é utilizada se o seu identificador corresponde ao identificador de chave necessário. Se o identificador não corresponderem, ocorrerá um erro.  
    
    O [exemplos de encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>demonstrar um cenário de ponto a ponto mais detalhado para blobs, filas e tabelas, juntamente com a integração do Cofre de chaves.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os utilizadores podem, opcionalmente, ativar um modo de funcionamento em que todos os carregamentos e transferências tem de estar encriptadas. Neste modo, as tentativas para carregar dados sem uma política de encriptação ou transferir os dados que não estão encriptados no serviço irão falhar no cliente. O **requireEncryption** sinalizador do objeto de opções de pedido controla esse comportamento. Se seu aplicativo vai encriptar todos os objetos armazenados no armazenamento do Azure, em seguida, pode definir o **requireEncryption** propriedade sobre as opções de pedido predefinidas para o objeto de cliente do serviço.   

Por exemplo, usar **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** para exigir a encriptação para todas as operações executadas por meio desse objeto de cliente do blob.

### <a name="blob-service-encryption"></a>Encriptação do serviço de BLOBs
Criar uma **BlobEncryptionPolicy** objeto e defina-o nas opções de pedido (por API ou num nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será manipulado pela biblioteca cliente internamente.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Encriptação do serviço fila
Criar uma **QueueEncryptionPolicy** objeto e defina-o nas opções de pedido (por API ou num nível de cliente utilizando **DefaultRequestOptions**). Tudo o resto será manipulado pela biblioteca cliente internamente.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Encriptação do serviço de tabela
Além de criar uma política de encriptação e defini-lo sobre as opções de pedido, deve optar por especificar uma **EncryptionResolver** na **TableRequestOptions**, ou definir o atributo [Encrypt] da entidade getter e setter.

### <a name="using-the-resolver"></a>Utilizar o resolvedor

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Utilizar atributos
Conforme mencionado acima, se a entidade implementa TableEntity, em seguida, propriedades getter e setter podem ser decoradas com o atributo [Encrypt] em vez de especificar o **EncryptionResolver**.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Encriptação e o desempenho
Tenha em atenção que os resultados de dados de armazenamento em sobrecarga de desempenho adicionais a encriptar. A chave de conteúdo e o IV tem de ser gerado, o próprio conteúdo tem de estar encriptado e metadados adicionais tem de ser formatados e carregados. Essa sobrecarga irá variar consoante a quantidade de dados a ser encriptadas. Recomendamos que os clientes sempre testarem seus aplicativos para o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes
* Transferir o [biblioteca de cliente de armazenamento do Azure para o pacote Maven de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Transferir o [biblioteca de clientes de armazenamento do Azure para Java código-fonte do GitHub](https://github.com/Azure/azure-storage-java)   
* Transferir a biblioteca do Maven de Cofre de chave do Azure para os pacotes Java Maven:
  * [Principais](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) pacote
  * [Cliente](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) pacote
* Visite o [documentação do Cofre de chaves do Azure](../../key-vault/key-vault-whatis.md)
