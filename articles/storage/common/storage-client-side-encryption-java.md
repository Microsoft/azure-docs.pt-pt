---
title: Encriptação do lado do cliente com Java para armazenamento Microsoft Azure / Microsoft Docs
description: A Biblioteca de Clientes de Armazenamento Azure para Java suporta encriptação e integração do lado do cliente com o Cofre chave Azure para máxima segurança para as suas aplicações de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8fe95a471df6ea86aad90f387088824c3c92bd3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460441"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Encriptação do lado do cliente e cofre de chaves Azure com Java para armazenamento Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
A Biblioteca de Clientes de [Armazenamento Azure para Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) suporta encriptar dados dentro das aplicações dos clientes antes de enviar para o Armazenamento Azure e desencriptar dados enquanto descarrega para o cliente. A biblioteca também apoia a integração com [o Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gestão de chaves de conta de armazenamento.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através da técnica do envelope
Os processos de encriptação e desencriptação seguem a técnica do envelope.  

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica do envelope
A encriptação através da técnica do envelope funciona da seguinte forma:  

1. A biblioteca de clientes de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.  
2. Os dados do utilizador são encriptados utilizando este CEK.   
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação da chave (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em Cofres chave Azure.  
   A biblioteca de clientes de armazenamento em si nunca tem acesso à KEK. A biblioteca invoca o algoritmo de embrulho chave que é fornecido pela Key Vault. Os utilizadores podem optar por utilizar fornecedores personalizados para embrulhar/desembrulhar chaves, se desejar.  
4. Os dados encriptados são depois enviados para o serviço de armazenamento Azure. A chave embrulhada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (numa bolha) ou interpolada com os dados encriptados (mensagens de fila e entidades de mesa).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através da técnica do envelope
A desencriptação através da técnica do envelope funciona da seguinte forma:  

1. A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação (KEK) localmente ou em Cofres chave Azure. O utilizador não precisa de saber a chave específica que foi usada para encriptação. Em vez disso, pode ser configurado e utilizado um resolver-chave que resolva diferentes identificadores chave das teclas.  
2. A biblioteca do cliente descarrega os dados encriptados juntamente com qualquer material de encriptação que seja armazenado no serviço.  
3. A chave de encriptação de conteúdo embrulhado (CEK) é então desembrulhada (desencriptada) utilizando a chave de encriptação da chave (KEK). Aqui novamente, a biblioteca de clientes não tem acesso à KEK. Simplesmente invoca o algoritmo de desembrulho personalizado ou key vault.  
4. A chave de encriptação de conteúdo (CEK) é então usada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca de clientes de armazenamento utiliza [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados dos utilizadores. Especificamente, modo [de acorrentação de blocos de cifra (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de forma um pouco diferente, por isso vamos discutir cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de clientes atualmente suporta encriptação de bolhas inteiras. Especificamente, a encriptação é suportada quando os utilizadores usam os métodos **de upload*** ou o método **openOutputStream.** Para downloads, tanto os downloads completos como os downloads de gama são suportados.  

Durante a encriptação, a biblioteca do cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes, e executará encriptação de envelopes dos dados blob usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como metadados blob juntamente com a bolha encriptada no serviço.

> [!WARNING]
> Se estiver a editar ou a carregar os seus próprios metadados para a bolha, tem de garantir que estes metadados estão preservados. Se fizer o upload de novos metadados sem estes metadados, perder-se-ão os metadados CEK, IV e outros metadados embrulhados e o conteúdo blob nunca mais será recuperável.
> 
> 

O download de uma bolha encriptada envolve recuperar o conteúdo de toda a bolha utilizando **os**/métodos de conveniência**openInputStream.** O CEK embrulhado é desembrulhado e utilizado juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

O descarregamento de uma gama arbitrária (métodos**downloadRange)** na bolha encriptada envolve ajustar a gama fornecida pelos utilizadores de forma a obter uma pequena quantidade de dados adicionais que podem ser usados para desencriptar com sucesso a gama solicitada.  

Todos os tipos de bolhas (bolhas de bloco, bolhas de página e bolhas de apêndice) podem ser encriptados/desencriptados utilizando este esquema.

### <a name="queues"></a>Filas
Uma vez que as mensagens de fila podem ser de qualquer formato, a biblioteca do cliente define um formato personalizado que inclui o Vetor de Inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.  

Durante a encriptação, a biblioteca do cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa encriptação de envelope do texto de mensagem de fila usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são adicionados à mensagem de fila encriptada. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Durante a desencriptação, a chave embrulhada é extraída da mensagem da fila e desembrulhada. O IV também é extraído da mensagem de fila e utilizado juntamente com a chave desembrulhada para desencriptar os dados da mensagem de fila. Note que os metadados de encriptação são pequenos (abaixo de 500 bytes), pelo que, embora conte para o limite de 64KB para uma mensagem de fila, o impacto deve ser controlável.

### <a name="tables"></a>Tabelas
A biblioteca do cliente suporta a encriptação de propriedades da entidade para inserir e substituir operações.

> [!NOTE]
> A fusão não é atualmente apoiada. Uma vez que um subconjunto de propriedades pode ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resultará na perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente do serviço, ou usar uma nova chave por imóvel, ambas inadequadas por razões de desempenho.
> 
> 

A encriptação de dados de tabela funciona da seguinte forma:  

1. Os utilizadores especificam as propriedades a encriptar.  
2. A biblioteca do cliente gera um vetor de inicialização aleatório (IV) de 16 bytes juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes para cada entidade, e executa encriptação de envelopes nas propriedades individuais a serem encriptadas derivando um novo IV por propriedade. A propriedade encriptada é armazenada como dados binários.  
3. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cordas que detém a informação sobre IV, versão e chave embrulhada. A segunda propriedade reservada (_ClientEncryptionMetadata2) é uma propriedade binária que detém a informação sobre as propriedades que estão encriptadas. A informação nesta segunda propriedade (_ClientEncryptionMetadata2) é encriptada.  
4. Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores podem agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser inferior a 1MB.  
   
   Note que apenas as propriedades de cordas podem ser encriptadas. Para que outros tipos de propriedades sejam encriptados, devem ser convertidos em cordas. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta às cordas após a desencriptação.
   
   Para as tabelas, para além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito especificando um atributo [Encrypt] (para entidades POCO que derivam da TableEntity) ou uma resolver de encriptação nas opções de pedido. Um resolver de encriptação é um delegado que pega uma chave de partição, chave de linha e nome de propriedade e devolve uma booleanque que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usará esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve ao fio. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar propriedade A; de outra forma encriptar propriedades A e B.) Note que não é necessário fornecer esta informação enquanto lê ou consulta entidades.

### <a name="batch-operations"></a>Operações de Lote
Nas operações de lote, o mesmo KEK será utilizado em todas as linhas dessa operação de lote porque a biblioteca do cliente só permite um objeto de opções (e, portanto, uma política/KEK) por operação de lote. No entanto, a biblioteca do cliente irá gerar internamente um novo CEK aleatório e aleatório por linha no lote. Os utilizadores também podem optar por encriptar diferentes propriedades para cada operação no lote, definindo este comportamento na resolução de encriptação.

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades estão encriptadas, não é possível executar consultas que filtram uma propriedade encriptada.  Se tentar, os resultados estarão incorretos, porque o serviço estaria a tentar comparar dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, deve especificar um resolver chave que seja capaz de resolver todas as teclas no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida a um fornecedor, a biblioteca do cliente irá lançar um erro. Para qualquer consulta que execute as projeções laterais do servidor, a biblioteca do cliente adicionará as propriedades especiais de metadados de encriptação (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por padrão às colunas selecionadas.

## <a name="azure-key-vault"></a>Azure Key Vault
O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Ao utilizar o Cofre de Chaves Azure, os utilizadores podem encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para obter mais informações, veja [O que é o Azure Key Vault?](../../key-vault/key-vault-overview.md)

A biblioteca de clientes de armazenamento utiliza a biblioteca principal key vault para fornecer um quadro comum em Azure para gerir chaves. Os utilizadores também obtêm o benefício adicional de usar a biblioteca de extensões Key Vault. A biblioteca de extensões fornece funcionalidade útil em torno de fornecedores de chaves locais e cloud simétricos simples e sem emenda, bem como com agregação e cache.

### <a name="interface-and-dependencies"></a>Interface e dependências
Há três pacotes key vault:  

* o núcleo azure-keyvault contém o IKey e o IKeyResolver. É um pequeno pacote sem dependências. A biblioteca de clientes de armazenamento para Java define-a como uma dependência.
* O cofre azure-keycontém o cliente KEY Vault REST.  
* As extensões azure-keyvault contém um código de extensão que inclui implementações de algoritmos criptográficos e um RSAKey e um SymmetricKey. Depende dos espaços de nome Core e KeyVault e fornece funcionalidade para definir um resolver agregado (quando os utilizadores querem usar vários fornecedores chave) e uma chave de cache resolver. Embora a biblioteca de clientes de armazenamento não dependa diretamente deste pacote, se os utilizadores quiserem usar o Cofre chave Azure para armazenar as suas chaves ou usar as extensões key vault para consumir os fornecedores criptográficos locais e em nuvem, eles vão precisar deste pacote.  
  
  Key Vault é projetado para chaves master de alto valor, e os limites de estrangulamento por Key Vault são projetados com isso em mente. Ao executar encriptação do lado do cliente com key vault, o modelo preferido é usar chaves master simétricas armazenadas como segredos no Key Vault e em cache local. Os utilizadores devem fazer o seguinte:  

1. Crie um segredo offline e carregue-o para o Cofre chave.  
2. Use o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para encriptação e cache esta informação localmente. Utilize cachingKeyResolver para o cache; não se espera que os utilizadores implementem a sua própria lógica de cache.  
3. Use o resolver do cache como entrada enquanto cria a política de encriptação.
   Mais informações sobre o uso do Cofre chave podem ser encontradas nas amostras de código de encriptação.

## <a name="best-practices"></a>Melhores práticas
O suporte de encriptação está disponível apenas na biblioteca de clientes de armazenamento para Java.

> [!IMPORTANT]
> Esteja ciente destes pontos importantes ao utilizar encriptação do lado do cliente:
> 
> * Ao ler ou escrever para uma bolha encriptada, utilize comandos de upload de blob inteiros e comandos de descarregamento de gama/bolha completas. Evite escrever a uma bolha encriptada utilizando operações protocolares como Bloquear, Colocar Block List, Escrever Páginas, Páginas Claras ou Bloco de Apêndice; caso contrário, poderá corromper a bolha encriptada e torná-la ilegível.
> * Para as mesas, existe um constrangimento semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.  
> * Se definir metadados na bolha encriptada, poderá substituir os metadados relacionados com encriptação necessários para a desencriptação, uma vez que a definição de metadados não é aditivo. Isto também é verdade para instantâneos; evite especificar metadados enquanto cria uma imagem instantânea de uma bolha encriptada. Se os metadados forem definidos, certifique-se de ligar primeiro para o método **downloadAtributos** para obter os metadados de encriptação atuais, e evitar escritas simultâneas enquanto os metadados estão sendo definidos.  
> * Ative a bandeira **de encriptação necessária** nas opções de pedido predefinidos para os utilizadores que devem funcionar apenas com dados encriptados. Veja abaixo mais informações.  
> 
> 

## <a name="client-api--interface"></a>Cliente API / Interface
Ao criar um objeto EncryptionPolicy, os utilizadores podem fornecer apenas uma Chave (implementação iKey), apenas um resolver (implementação iKeyResolver), ou ambos. IKey é o tipo de chave básico que é identificado usando um identificador chave e que fornece a lógica para embrulhar/desembrulhar. O IKeyResolver é utilizado para resolver uma chave durante o processo de desencriptação. Define um método ResolveKey que devolve um IKey dado um identificador chave. Isto fornece aos utilizadores a capacidade de escolher entre várias teclas que são geridas em vários locais.

* Para encriptação, a chave é sempre usada e a ausência de uma chave resultará num erro.  
* Para desencriptação:  
  
  * O resolver chave é invocado se especificado para obter a chave. Se o resolver for especificado mas não tiver um mapeamento para o identificador chave, é lançado um erro.  
  * Se o resolver não for especificado, mas uma chave for especificada, a chave é utilizada se o seu identificador corresponder ao identificador de chave necessário. Se o identificador não corresponder, é lançado um erro.  
    
    As amostras de [encriptação](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) demonstram um cenário mais detalhado de ponta a ponta para bolhas, filas e mesas, juntamente com a integração do Key Vault.

### <a name="requireencryption-mode"></a>Modo De Encriptação Requerer
Os utilizadores podem ativar opcionalmente um modo de funcionamento onde todos os uploads e downloads devem ser encriptados. Neste modo, as tentativas de fazer o upload de dados sem uma política de encriptação ou descarregamento de dados que não sejam encriptados no serviço falharão no cliente. A bandeira **de encriptação necessária** das opções de pedido controla este comportamento. Se a sua aplicação encriptar todos os objetos armazenados no Armazenamento Azure, então pode definir a propriedade **de Encriptação necessária** nas opções de pedido padrão para o objeto cliente do serviço.   

Por exemplo, utilize **cloudBlobClient.getDefaultRequestOptions().setRequireEncryption (verdadeiro)** para exigir encriptação para todas as operações blob realizadas através desse objeto de cliente.

### <a name="blob-service-encryption"></a>Encriptação do serviço Blob
Crie um objeto **BlobEncryptionPolicy** e descoloque-o nas opções de pedido (por API ou a nível de cliente utilizando **O PredevidoSolicitOptions**). Todo o resto será tratado internamente pela biblioteca de clientes.

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

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila
Crie um objeto **QueueEncryptionPolicy** e desloque-o nas opções de pedido (por API ou a nível de cliente utilizando **O PredevidoSolicitOptions**). Todo o resto será tratado internamente pela biblioteca de clientes.

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

### <a name="table-service-encryption"></a>Encriptação do serviço de mesa
Além de criar uma política de encriptação e defini-la em opções de pedido, deve especificar um **CryptonResolver** em **TableRequestOptions,** ou definir o atributo [Encrypt] no getter e setter da entidade.

### <a name="using-the-resolver"></a>Usando a resolver

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

### <a name="using-attributes"></a>Usando atributos
Como mencionado acima, se a entidade implementar tableEntity, então o getter e setter de propriedades podem ser decorados com o atributo [Encrypt] em vez de especificar o **CryptonResolver**.

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

## <a name="encryption-and-performance"></a>Encriptação e desempenho

Note que encriptar os dados de armazenamento resulta em despesas adicionais de desempenho. A chave de conteúdo e IV devem ser geradas, o conteúdo em si deve ser encriptado e metadados adicionais devem ser formatados e carregados. Esta sobrecarga variará dependendo da quantidade de dados que estão a ser encriptados. Recomendamos que os clientes testem sempre as suas aplicações para desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos seguintes

* Descarregue a Biblioteca de Clientes de [Armazenamento Azure para o pacote Java Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Descarregue a Biblioteca de Clientes de [Armazenamento Azure para java código fonte do GitHub](https://github.com/Azure/azure-storage-java)
* Descarregue a Biblioteca Azure Key Vault Maven para os pacotes Java Maven:
  * [Pacote core](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
  * Pacote [de cliente](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Visite a documentação do cofre da [chave azure](../../key-vault/key-vault-overview.md)
