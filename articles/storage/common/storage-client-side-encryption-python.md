---
title: Encriptação do lado do cliente com Python
titleSuffix: Azure Storage
description: A Biblioteca de Clientes de Armazenamento Azure para Python suporta encriptação do lado do cliente para segurança máxima para as suas aplicações de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895375"
---
# <a name="client-side-encryption-with-python"></a>Encriptação do lado do cliente com Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
A Biblioteca de Clientes de [Armazenamento Azure para Python](https://pypi.python.org/pypi/azure-storage) suporta encriptar dados dentro das aplicações do cliente antes de enviar para o Armazenamento Azure e desencriptar dados enquanto descarrega para o cliente.

> [!NOTE]
> A biblioteca Azure Storage Python está em pré-estreia.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através da técnica do envelope
Os processos de encriptação e desencriptação seguem a técnica do envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica do envelope
A encriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca de clientes de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.
2. Os dados do utilizador são encriptados utilizando este CEK.
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação da chave (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétricaou uma chave simétrica, que é gerida localmente.
   A biblioteca de clientes de armazenamento em si nunca tem acesso à KEK. A biblioteca invoca o algoritmo de embrulho chave que é fornecido pelo KEK. Os utilizadores podem optar por utilizar fornecedores personalizados para embrulhar/desembrulhar chaves, se desejar.
4. Os dados encriptados são depois enviados para o serviço de armazenamento Azure. A chave embrulhada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (numa bolha) ou interpolada com os dados encriptados (mensagens de fila e entidades de mesa).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através da técnica do envelope
A desencriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação (KEK) localmente. O utilizador não precisa de saber a chave específica que foi usada para encriptação. Em vez disso, um resolver-chave, que resolve diferentes identificadores chave para as teclas, pode ser configurado e utilizado.
2. A biblioteca do cliente descarrega os dados encriptados juntamente com qualquer material de encriptação que seja armazenado no serviço.
3. A chave de encriptação de conteúdo embrulhado (CEK) é então desembrulhada (desencriptada) utilizando a chave de encriptação da chave (KEK). Aqui novamente, a biblioteca de clientes não tem acesso à KEK. Simplesmente invoca o algoritmo de desembrulho do fornecedor personalizado.
4. A chave de encriptação de conteúdo (CEK) é então usada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca de clientes de armazenamento utiliza [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados dos utilizadores. Especificamente, modo [de acorrentação de blocos de cifra (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de forma um pouco diferente, por isso vamos discutir cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca de clientes atualmente suporta encriptação de bolhas inteiras. Especificamente, a encriptação é suportada quando os utilizadores usam os métodos **de criação***. Para downloads, tanto os downloads completos como os downloads de gama são suportados, e a paralelização tanto do upload como do download está disponível.

Durante a encriptação, a biblioteca do cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes, e executará encriptação de envelopes dos dados blob usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como metadados blob juntamente com a bolha encriptada no serviço.

> [!WARNING]
> Se estiver a editar ou a carregar os seus próprios metadados para a bolha, tem de garantir que estes metadados estão preservados. Se fizer o upload de novos metadados sem estes metadados, perder-se-ão os metadados CEK, IV e outros metadados embrulhados e o conteúdo blob nunca mais será recuperável.
> 
> 

O download de uma bolha encriptada envolve recuperar o conteúdo de toda a bolha utilizando os métodos de conveniência **get*** . O CEK embrulhado é desembrulhado e utilizado juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

O descarregamento de uma gama arbitrária **(obter**métodos * com parâmetros de alcance passados) na bolha encriptada envolve ajustar a gama fornecida pelos utilizadores de forma a obter uma pequena quantidade de dados adicionais que podem ser usados para desencriptar com sucesso a gama solicitada.

As bolhas de bloco e as bolhas de página só podem ser encriptadas/desencriptadas utilizando este esquema. Atualmente não existe suporte para encriptar bolhas de apêndice.

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
3. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como duas propriedades reservadas adicionais. A primeira propriedade\_reservada (ClientEncryptionMetadata1) é uma propriedade de cadeia que detém a informação sobre IV, versão e chave embrulhada. A segunda propriedade\_reservada (ClientEncryptionMetadata2) é uma propriedade binária que detém a informação sobre as propriedades que estão encriptadas. A informação nesta\_segunda propriedade (ClientEncryptionMetadata2) é encriptada.
4. Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores podem agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser inferior a 1MB.

   Note que apenas as propriedades de cordas podem ser encriptadas. Para que outros tipos de propriedades sejam encriptados, devem ser convertidos em cordas. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta para cordas (cordas cruas, não EntityProperties com tipo EdmType.STRING) após desencriptação.

   Para as tabelas, para além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito armazenando estas propriedades em objetos TableEntity com o tipo definido para EdmType.STRING e encriptar definido para verdade ou configurando o encryption_resolver_function no objeto de serviço de mesa. Um resolver de encriptação é uma função que pega uma chave de partição, chave de linha e nome de propriedade e devolve uma booleanque que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usará esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve ao fio. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar propriedade A; de outra forma encriptar propriedades A e B.) Note que não é necessário fornecer esta informação enquanto lê ou consulta entidades.

### <a name="batch-operations"></a>Operações de Lote
Uma política de encriptação aplica-se a todas as linhas do lote. A biblioteca do cliente irá criar internamente um novo CEK aleatório e aleatório por linha no lote. Os utilizadores também podem optar por encriptar diferentes propriedades para cada operação no lote, definindo este comportamento na resolução de encriptação.
Se um lote for criado como gestor de contexto através do método do lote de serviço de mesas, a política de encriptação do serviço de mesas será automaticamente aplicada ao lote. Se um lote for criado explicitamente chamando o construtor, a política de encriptação deve ser aprovada como parâmetro e deixada inalterada durante a vida útil do lote.
Note que as entidades são encriptadas à medida que são inseridas no lote utilizando a política de encriptação do lote (as entidades NÃO são encriptadas no momento de cometer o lote usando a política de encriptação do serviço de mesa).

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades estão encriptadas, não é possível executar consultas que filtram uma propriedade encriptada.  Se tentar, os resultados estarão incorretos, porque o serviço estaria a tentar comparar dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, deve especificar um resolver chave que seja capaz de resolver todas as teclas no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida a um fornecedor, a biblioteca do cliente irá lançar um erro. Para qualquer consulta que execute as projeções laterais do servidor, a\_biblioteca do cliente \_adicionará as propriedades especiais de metadados de encriptação (ClientEncryptionMetadata1 e ClientEncryptionMetadata2) por padrão às colunas selecionadas.
> 
> [!IMPORTANT]
> Esteja ciente destes pontos importantes ao utilizar encriptação do lado do cliente:
> 
> * Ao ler ou escrever para uma bolha encriptada, utilize comandos de upload de blob inteiros e comandos de descarregamento de gama/bolha completas. Evite escrever a uma bolha encriptada utilizando operações protocolares como Bloquear, Colocar Lista de Blocos, Escrever Páginas ou Páginas Claras; caso contrário, poderá corromper a bolha encriptada e torná-la ilegível.
> * Para as mesas, existe um constrangimento semelhante. Tenha cuidado para não atualizar propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir metadados na bolha encriptada, poderá substituir os metadados relacionados com encriptação necessários para a desencriptação, uma vez que a definição de metadados não é aditivo. Isto também é verdade para instantâneos; evite especificar metadados enquanto cria uma imagem instantânea de uma bolha encriptada. Se os metadados forem definidos, certifique-se de que chama primeiro o método **get_blob_metadata** para obter os metadados de encriptação atuais e evite escritas simultâneas enquanto os metadados estão a ser definidos.
> * Ative a bandeira **require_encryption** no objeto de serviço para utilizadores que devem funcionar apenas com dados encriptados. Veja abaixo mais informações.

A biblioteca de clientes de armazenamento espera que o KEK fornecido e a chave resolver implementem a seguinte interface. O suporte [azure Key Vault](https://azure.microsoft.com/services/key-vault/) para a gestão da Python KEK está pendente e será integrado nesta biblioteca quando concluído.

## <a name="client-api--interface"></a>Cliente API / Interface
Após a criação de um objeto de serviço de armazenamento (isto é, blockblobservice), o utilizador pode atribuir valores aos campos que constituem uma política de encriptação: key_encryption_key, key_resolver_function e require_encryption. Os utilizadores podem fornecer apenas um KEK, apenas um resolver, ou ambos. key_encryption_key é o tipo-chave básico que é identificado usando um identificador chave e que fornece a lógica para embrulhar/desembrulhar. key_resolver_function é usado para resolver uma chave durante o processo de desencriptação. Devolve um KEK válido dado um identificador chave. Isto fornece aos utilizadores a capacidade de escolher entre várias teclas que são geridas em vários locais.

O KEK deve implementar os seguintes métodos para encriptar com sucesso dados:

* wrap_key(cek): Envolve o CEK especificado (bytes) utilizando um algoritmo à escolha do utilizador. Devolve a chave embrulhada.
* get_key_wrap_algorithm(): Devolve o algoritmo utilizado para embrulhar chaves.
* get_kid(): Devolve o id da chave de cordas para este KEK.
  O KEK deve implementar os seguintes métodos para desencriptar com sucesso os dados:
* unwrap_key (cek, algoritmo): Devolve a forma desembrulhada do CEK especificado utilizando o algoritmo especificado para a cadeia.
* get_kid(): Devolve um id de chave de corda para este KEK.

O resolver chave deve, pelo menos, implementar um método que, dado um id chave, devolve o KEK correspondente implementando a interface acima. Apenas este método deve ser atribuído à propriedade key_resolver_function no objeto de serviço.

* Para encriptação, a chave é sempre usada e a ausência de uma chave resultará num erro.
* Para desencriptação:

  * O resolver chave é invocado se especificado para obter a chave. Se o resolver for especificado mas não tiver um mapeamento para o identificador chave, é lançado um erro.
  * Se o resolver não for especificado, mas uma chave for especificada, a chave é utilizada se o seu identificador corresponder ao identificador de chave necessário. Se o identificador não corresponder, é lançado um erro.

    As amostras de encriptação em azure.storage.samples demonstram um cenário mais detalhado de ponta a ponta para bolhas, filas e mesas.
      As implementações da amostra do KEK e da chave resolver são fornecidas nos ficheiros da amostra como KeyWrapper e KeyResolver, respectivamente.

### <a name="requireencryption-mode"></a>Modo De Encriptação Requerer
Os utilizadores podem ativar opcionalmente um modo de funcionamento onde todos os uploads e downloads devem ser encriptados. Neste modo, as tentativas de fazer o upload de dados sem uma política de encriptação ou descarregamento de dados que não sejam encriptados no serviço falharão no cliente. A **bandeira require_encryption** no objeto de serviço controla este comportamento.

### <a name="blob-service-encryption"></a>Encriptação do serviço Blob
Desbloqueie os campos de política de encriptação no objeto blockblobservice. Todo o resto será tratado internamente pela biblioteca de clientes.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila
Desloque os campos de política de encriptação no objeto de serviço de fila. Todo o resto será tratado internamente pela biblioteca de clientes.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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

### <a name="table-service-encryption"></a>Encriptação do serviço de mesa
Além de criar uma política de encriptação e defini-la nas opções de pedido, deve especificar um **encryption_resolver_function** no serviço de **mesa,** ou definir o atributo encriptado na EntidadePropriedade.

### <a name="using-the-resolver"></a>Usando a resolver

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Usando atributos
Como mencionado acima, uma propriedade pode ser marcada para encriptação armazenando-a em um objeto EntityProperty e definindo o campo de encriptação.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Encriptação e desempenho
Note que encriptar os dados de armazenamento resulta em despesas adicionais de desempenho. A chave de conteúdo e IV devem ser geradas, o conteúdo em si deve ser encriptado e metadados adicionais devem ser formatados e carregados. Esta sobrecarga variará dependendo da quantidade de dados que estão a ser encriptados. Recomendamos que os clientes testem sempre as suas aplicações para desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos seguintes
* Descarregue a Biblioteca de Clientes de [Armazenamento Azure para o pacote Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Descarregue a Biblioteca do Cliente de [Armazenamento Azure para o Código Fonte Python do GitHub](https://github.com/Azure/azure-storage-python)
