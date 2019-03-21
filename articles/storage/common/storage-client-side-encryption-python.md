---
title: Encriptação do lado do cliente com o Python para o armazenamento do Microsoft Azure | Documentos da Microsoft
description: Biblioteca de cliente de armazenamento do Azure para Python suporta a encriptação do lado do cliente para segurança máxima para as suas aplicações de armazenamento do Azure.
services: storage
author: lakasa
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: ecfd86a7e4a8ef97663cc930906fd909b6f0fae8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011116"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Encriptação do lado do cliente com o Python para o armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
O [biblioteca de clientes de armazenamento do Azure para Python](https://pypi.python.org/pypi/azure-storage) suporta a encriptação de dados dentro de aplicativos de cliente antes de carregar para o armazenamento do Azure e a desencriptação de dados durante a transferência para o cliente.

> [!NOTE]
> A biblioteca de Python de armazenamento do Azure está em pré-visualização.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação via a técnica de envelope
Os processos de criptografia e descriptografia siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica de envelope
Encriptação através da técnica de envelope funciona da seguinte forma:

1. A biblioteca de cliente de armazenamento do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uma único uso.
2. Dados de utilizador são encriptados utilizando este CEK.
3. O CEK, em seguida, é encapsulado (encriptada) com a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, que é gerenciada localmente.
   A biblioteca de cliente de armazenamento em si nunca tem acesso a KEK. A biblioteca invoca o algoritmo de chave de encapsulamento de aplicações fornecido pela KEK. Os utilizadores podem optar por utilizar fornecedores personalizados para o encapsulamento de chave/abertura se assim o desejar.
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. A chave encapsulada, juntamente com alguns metadados de encriptação adicional é armazenada como metadados (num blob) ou interpolada com os dados encriptados (fila de mensagens e entidades da tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação via a técnica de envelope
Desencriptação via a técnica de envelope funciona da seguinte forma:

1. A biblioteca de cliente parte do princípio de que o utilizador está a gerir a chave de encriptação de chaves (KEK) localmente. O usuário não precisa de conhecer a chave específica que foi utilizada para encriptação. Em vez disso, um resolvedor de chave, que resolve diferentes identificadores de chaves para chaves, pode ser configurado e utilizado.
2. A biblioteca de cliente transfere os dados criptografados, juntamente com qualquer material de encriptação que é armazenado no serviço.
3. A chave de encriptação de conteúdo encapsulada (CEK) é, em seguida, não encapsulada (descriptografado) usando a criptografia de chave de chaves (KEK). Aqui, novamente, a biblioteca de cliente não tem acesso a KEK. Ele simplesmente invoca o algoritmo de abertura do fornecedor personalizado.
4. A chave de encriptação de conteúdo (CEK), em seguida, é utilizada para desencriptar os dados de utilizador encriptado.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados de utilizador. Especificamente, [encadeamento de bloco de cifras (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) modo com AES. Cada serviço funciona de forma um pouco diferente, então, vamos abordar cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de cliente atualmente suporta a encriptação de todos blobs apenas. Especificamente, a encriptação é suportada quando os utilizadores utilizam o **criar*** métodos. Para downloads, ambos completos e transferências de intervalo são suportadas e, a paralelização de carregamento e transferência está disponível.

Durante a encriptação, a biblioteca de cliente irá gerar um Vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes e efetuar a encriptação de envelope dos dados de blob usando essas informações. O CEK encapsulada e alguns metadados de encriptação adicional, em seguida, são armazenados como metadados, juntamente com o blob criptografado no serviço de Blobs.

> [!WARNING]
> Se estiver a edição ou seus próprios metadados para o blob a carregar, precisa garantir que estes metadados são preservados. Se carregar novos metadados sem estes metadados, o CEK encapsulada, IV e outros metadados serão perdidos e o conteúdo será nunca possível obter novamente.
> 
> 

Transferir um blob encriptado envolve a obtenção dos conteúdos do blob inteiro com o **obter*** métodos de conveniência. O CEK encapsulada é não envolto e utilizado em conjunto com o IV (armazenado como metadados do blob neste caso) para retornar os dados descriptografados para os utilizadores.

Transferir um intervalo de arbitrário (**obter*** métodos com parâmetros de intervalo passado) no blob criptografado envolve a ajustar o intervalo fornecido por utilizadores para obter uma pequena quantidade de dados adicionais que podem ser utilizadas para com êxito descriptografar o intervalo pedido.

Blobs de blocos e blobs de página só podem ser encriptados/desencriptar a utilização deste esquema. Atualmente não há suporte para encriptação de blobs de acréscimo.

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
3. O CEK encapsulada e alguns metadados de encriptação adicional, em seguida, são armazenadas como duas propriedades reservadas adicionais. A primeira propriedade de reservada (\_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém as informações sobre o IV, versão e chave encapsulada. O segundo de propriedade reservada (\_ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são encriptados. As informações nesta segunda propriedade (\_ClientEncryptionMetadata2) é autocriptografada.
4. Devido a essas propriedades reservadas adicionais necessárias para a encriptação, os utilizadores agora podem ter apenas 250 propriedades personalizadas, em vez de 252. O tamanho total da entidade tem de ser inferior a 1MB.

   Tenha em atenção que apenas as propriedades de cadeia de caracteres podem ser encriptadas. Se outros tipos de propriedades estão a ser encriptada, eles devem ser convertidos em cadeias de caracteres. As cadeias de caracteres encriptadas são armazenadas no serviço como propriedades binárias e elas são convertidas para cadeias de caracteres (não processadas cadeias de caracteres, não EntityProperties com tipo EdmType.STRING) após desencriptação.

   Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito por qualquer um dos armazenar estas propriedades no TableEntity objetos com o conjunto de tipo para EdmType.STRING e encriptar definido como VERDADEIRO ou definir o encryption_resolver_function no objeto tableservice. Um resolvedor de encriptação é uma função que obtém uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Tenha em atenção que não é necessário fornecer estas informações ao ler ou consultar entidades.

### <a name="batch-operations"></a>Operações de lote
Uma política de encriptação aplica-se a todas as linhas no lote. A biblioteca de cliente internamente irá gerar um novo IV aleatório e CEK aleatório por linha no lote. Os utilizadores também podem optar por encriptar as propriedades diferentes para cada operação no lote definindo esse comportamento no resolvedor de encriptação.
Se um lote é criado como um Gestor de contexto através do método batch() tableservice, política de encriptação do tableservice será aplicada automaticamente para o batch. Se um lote é criado explicitamente ao chamar o construtor, a política de encriptação tem de ser transmitida como um parâmetro e à esquerda sem modificações para o tempo de vida do batch.
Tenha em atenção que as entidades são encriptadas conforme eles são inseridos no batch através da política de encriptação do batch (entidades não são encriptadas no momento da consolidar o lote através da política de encriptação do tableservice).

### <a name="queries"></a>Consultas
> [!NOTE]
> Uma vez que as entidades são encriptadas, não é possível executar consultas que filtrarão numa propriedade de encriptação.  Se tentar, os resultados serão incorretos, porque o serviço estaria tentando comparar os dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, tem de especificar um resolvedor de chave que é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um fornecedor, a biblioteca de cliente irá gerar um erro. Para qualquer consulta que executa as projeções de lado do servidor, a biblioteca de cliente irá adicionar as propriedades de metadados de encriptação especial (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) por predefinição, para as colunas selecionadas.
> 
> [!IMPORTANT]
> Tenha em atenção os seguintes pontos importantes ao utilizar a encriptação do lado do cliente:
> 
> * Ao ler a partir de ou escrever para um blob encriptado, utilize os comandos de carregamento de BLOBs de todo e comandos de download de blob de intervalo/todo. Evitar gravar num blob encriptado a utilizar operações de protocolo, como a colocação de blocos, colocar a lista de bloqueios, escrever páginas ou páginas clara; caso contrário, pode danificar o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir metadados no blob criptografado, pode substituir os metadados relacionados com a encriptação necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isto também se aplica para instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob encriptado. Se os metadados têm de ser definidos, certifique-se de que chamar o **get_blob_metadata** método pela primeira vez para obter os metadados de encriptação atual e evitar escritas simultâneas, enquanto estiver a ser definido metadados.
> * Ativar a **require_encryption** sinalizador do objeto de serviço para os utilizadores que devem funcionar apenas com os dados encriptados. Veja abaixo para obter mais informações.

A biblioteca de cliente de armazenamento espera que a KEK fornecido e o Resolvedor de chave para implementar a interface seguinte. [O Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o suporte para gestão de Python KEK está pendente e será integrado para esta biblioteca quando concluída.

## <a name="client-api--interface"></a>API de cliente / Interface
Depois de ter sido criado um objeto de serviço de armazenamento (ou seja, blockblobservice), o utilizador pode atribuir valores aos campos que constituem uma política de encriptação: key_encryption_key, key_resolver_function e require_encryption. Os utilizadores podem fornecer apenas KEK, apenas um resolvedor, ou ambos. key_encryption_key é o tipo de chave básico que é identificado com um identificador de chave e que fornece a lógica de encapsulamento de aplicações/abertura. key_resolver_function é utilizado para resolver uma chave durante o processo de descriptografia. Ele retorna um KEK válido devido um identificador de chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias chaves que são geridas em várias localizações.

A KEK deve implementar os métodos seguintes para encriptar os dados com êxito:

* wrap_key(cek): Encapsula CEK especificado com um algoritmo de preferência do utilizador (bytes). Devolve a chave encapsulada.
* get_key_wrap_algorithm(): Devolve o algoritmo utilizado para encapsular as chaves.
* get_kid(): Devolve o id de chave de cadeia de caracteres para este KEK.
  A KEK deve implementar os seguintes métodos para desencriptar os dados com êxito:
* unwrap_key(cek, algorithm): Devolve o formulário não encapsulado do CEK especificado utilizando o algoritmo de cadeia especificada.
* get_kid(): Devolve um id de chave de cadeia de caracteres para este KEK.

O Resolvedor de chave tem de implementar, pelo menos, um método que, tendo em conta um id de chave, retorna a KEK correspondente, a implementação da interface acima. Apenas esse método é atribuído à propriedade key_resolver_function no objeto de serviço.

* Para a encriptação, a chave é utilizada sempre e a ausência de uma chave irá resultar num erro.
* Para desencriptação:

  * O Resolvedor de chave é invocado se especificada para obter a chave. Se o resolvedor for especificado, mas não tem um mapeamento para o identificador de chave, ocorrerá um erro.
  * Se o resolvedor não for especificado, mas é especificada uma chave, a chave é utilizada se o seu identificador corresponde ao identificador de chave necessário. Se o identificador não corresponderem, ocorrerá um erro.

    Os exemplos de encriptação no azure.storage.samples <fix URL>demonstrar um cenário de ponto a ponto mais detalhado para blobs, filas e tabelas.
      Implementações de exemplo da KEK e resolução de chave são fornecidas nos ficheiros de exemplo como KeyWrapper e KeyResolver, respetivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os utilizadores podem, opcionalmente, ativar um modo de funcionamento em que todos os carregamentos e transferências tem de estar encriptadas. Neste modo, as tentativas para carregar dados sem uma política de encriptação ou transferir os dados que não estão encriptados no serviço irão falhar no cliente. O **require_encryption** sinalizador no objeto de serviço controla esse comportamento.

### <a name="blob-service-encryption"></a>Encriptação do serviço de BLOBs
Campos de política, a criptografia deve ser definido no objeto blockblobservice. Tudo o resto será manipulado pela biblioteca cliente internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Encriptação do serviço fila
Campos de política, a criptografia deve ser definido no objeto queueservice. Tudo o resto será manipulado pela biblioteca cliente internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Encriptação do serviço de tabela
Além de criar uma política de encriptação e defini-lo sobre as opções de pedido, deve optar por especificar uma **encryption_resolver_function** no **tableservice**, ou definir o atributo de criptografar no EntityProperty.

### <a name="using-the-resolver"></a>Utilizar o resolvedor

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.
def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Utilizar atributos
Conforme mencionado acima, uma propriedade pode ser marcada para a encriptação de armazená-los num objeto de EntityProperty e definindo o campo de criptografar.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Encriptação e o desempenho
Tenha em atenção que os resultados de dados de armazenamento em sobrecarga de desempenho adicionais a encriptar. A chave de conteúdo e o IV tem de ser gerado, o próprio conteúdo tem de estar encriptado e metadados adicionais tem de ser formatado e carregados. Essa sobrecarga irá variar consoante a quantidade de dados a ser encriptadas. Recomendamos que os clientes sempre testarem seus aplicativos para o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes
* Transferir o [biblioteca de cliente de armazenamento do Azure para Java PyPi pacote](https://pypi.python.org/pypi/azure-storage)
* Transferir o [código a partir do GitHub de origem da biblioteca de clientes de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
