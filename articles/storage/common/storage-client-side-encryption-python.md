---
title: Criptografia do lado do cliente com Python para Armazenamento do Microsoft Azure | Microsoft Docs
description: A biblioteca de cliente de armazenamento do Azure para Python dá suporte à criptografia do lado do cliente para segurança máxima para seus aplicativos de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cd8ba51b960703fa25371d874ed2bb50e7df2fde
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360051"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Criptografia do lado do cliente com Python para Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição geral
A [biblioteca de cliente de armazenamento do Azure para Python](https://pypi.python.org/pypi/azure-storage) dá suporte à criptografia de dados em aplicativos cliente antes do carregamento no armazenamento do Azure e à descriptografia de dados durante o download para o cliente.

> [!NOTE]
> A biblioteca do Python de armazenamento do Azure está em versão prévia.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia por meio da técnica de envelope
Os processos de criptografia e descriptografia seguem a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia por meio da técnica de envelope
A criptografia por meio da técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente de armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo), que é uma chave simétrica de uso único.
2. Os dados do usuário são criptografados usando este CEK.
3. O CEK é então encapsulado (criptografado) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, que é gerenciada localmente.
   A própria biblioteca do cliente de armazenamento nunca tem acesso ao KEK. A biblioteca invoca o algoritmo de encapsulamento de chave que é fornecido pelo KEK. Os usuários podem optar por usar provedores personalizados para encapsulamento/desencapsulamento de chave, se desejado.
4. Os dados criptografados são então carregados no serviço de armazenamento do Azure. A chave encapsulada junto com alguns metadados de criptografia adicionais é armazenada como metadados (em um blob) ou interpolada com os dados criptografados (mensagens de fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia por meio da técnica de envelope
A descriptografia por meio da técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente pressupõe que o usuário está gerenciando a chave de criptografia de chave (KEK) localmente. O usuário não precisa saber a chave específica que foi usada para criptografia. Em vez disso, um resolvedor de chave, que resolve diferentes identificadores de chave para chaves, pode ser configurado e usado.
2. A biblioteca de cliente baixa os dados criptografados junto com qualquer material de criptografia armazenado no serviço.
3. A chave de criptografia de conteúdo encapsulado (CEK) é desencapsulada (descriptografada) usando a chave de criptografia de chave (KEK). Novamente, a biblioteca de cliente não tem acesso ao KEK. Ele simplesmente invoca o algoritmo de desempacotamento do provedor personalizado.
4. A CEK (chave de criptografia de conteúdo) é usada para descriptografar os dados de usuário criptografados.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa o [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar os dados do usuário. Especificamente, o modo [CBC (encadeamento de bloco de codificação)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de maneira um pouco diferente, então discutiremos cada um deles aqui.

### <a name="blobs"></a>Blobs
Atualmente, a biblioteca de cliente dá suporte apenas à criptografia de BLOBs inteiros. Especificamente, há suporte para a criptografia quando os usuários usam os métodos **Create***. Para downloads, há suporte para downloads completos e de intervalo e a paralelização de upload e download está disponível.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatória (IV) de 16 bytes, junto com uma chave de criptografia de conteúdo aleatória (CEK) de 32 bytes e executará a criptografia de envelope dos dados de BLOB usando essas informações. O CEK encapsulado e alguns metadados de criptografia adicionais são armazenados como metadados de blob junto com o blob criptografado no serviço.

> [!WARNING]
> Se você estiver editando ou carregando seus próprios metadados para o blob, será necessário garantir que esses metadados sejam preservados. Se você carregar novos metadados sem esses metadados, o CEK encapsulado, o IV e outros metadados serão perdidos e o conteúdo do blob nunca será recuperável novamente.
> 
> 

O download de um blob criptografado envolve a recuperação do conteúdo do blob inteiro usando os métodos de conveniência **Get** *. O CEK encapsulado é desencapsulado e usado junto com o IV (armazenado como metadados de blob nesse caso) para retornar os dados descriptografados aos usuários.

O download de um intervalo arbitrário ( **Get** * métodos com parâmetros de intervalo passados) no blob criptografado envolve o ajuste do intervalo fornecido pelos usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado .

Blobs de blocos e blobs de páginas só podem ser criptografados/descriptografados usando este esquema. Atualmente, não há suporte para a criptografia de blobs de acréscimo.

### <a name="queues"></a>Filas
Como as mensagens da fila podem ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia de conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa a criptografia de envelope do texto da mensagem da fila usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são adicionados à mensagem da fila criptografada. Essa mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. O CEK encapsulado e alguns metadados de criptografia adicionais são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada\_(ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém as informações sobre o IV, a versão e a chave encapsulada. A segunda propriedade reservada\_(ClientEncryptionMetadata2) é uma propriedade binária que contém as informações sobre as propriedades que são criptografadas. As informações nessa segunda Propriedade (\_ClientEncryptionMetadata2) são criptografadas em si.
4. Devido a essas propriedades reservadas adicionais necessárias para a criptografia, os usuários agora podem ter apenas 250 Propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser menor que 1 MB.

   Observe que apenas as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades forem criptografados, eles deverão ser convertidos em cadeias de caracteres. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias e são convertidas de volta em cadeias de caracteres (cadeias de caracteres brutas, não Entityproperties com o tipo EdmType. STRING) após a descriptografia.

   Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito armazenando-se essas propriedades em objetos TableEntity com o tipo definido como EdmType. STRING e Encrypt definido como true ou definindo o encryption_resolver_function no objeto tableservice. Um resolvedor de criptografia é uma função que usa uma chave de partição, uma chave de linha e um nome de propriedade e retorna um booliano que indica se essa propriedade deve ser criptografada. Durante a encriptação, a biblioteca de cliente utilizará estas informações para decidir se uma propriedade deve ser encriptada ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Observe que não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

### <a name="batch-operations"></a>Operações em lote
Uma política de criptografia se aplica a todas as linhas no lote. A biblioteca de cliente gerará internamente um novo IV aleatório e CEK aleatórios por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação no lote definindo esse comportamento no resolvedor de criptografia.
Se um lote for criado como um Gerenciador de contexto por meio do método tableservice batch (), a política de criptografia do tableservice será aplicada automaticamente ao lote. Se um lote for criado explicitamente chamando o construtor, a política de criptografia deverá ser passada como um parâmetro e deixada sem modificações durante o tempo de vida do lote.
Observe que as entidades são criptografadas à medida que são inseridas no lote usando a política de criptografia do lote (as entidades não são criptografadas no momento da confirmação do lote usando a política de criptografia do tableservice).

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades são criptografadas, você não pode executar consultas que filtram uma propriedade criptografada.  Se você tentar, os resultados estarão incorretos, pois o serviço estaria tentando comparar dados criptografados com dados não criptografados.
> 
> 
> Para executar operações de consulta, você deve especificar um resolvedor de chave capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida para um provedor, a biblioteca do cliente gerará um erro. Para qualquer consulta que executa projeções do lado do servidor, a biblioteca de cliente adicionará as propriedades especiais\_de metadados \_de criptografia (ClientEncryptionMetadata1 e ClientEncryptionMetadata2) por padrão às colunas selecionadas.
> 
> [!IMPORTANT]
> Esteja atento a esses pontos importantes ao usar a criptografia do lado do cliente:
> 
> * Ao ler ou gravar em um blob criptografado, use comandos de carregamento de blob inteiros e comandos de download de blob completo e de intervalo. Evite gravar em um blob criptografado usando operações de protocolo como colocar bloco, colocar lista de blocos, gravar páginas ou limpar páginas; caso contrário, você pode corromper o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar Propriedades criptografadas sem Atualizar os metadados de criptografia.
> * Se você definir metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, pois a definição de metadados não é aditiva. Isso também é verdadeiro para instantâneos; Evite especificar metadados ao criar um instantâneo de um blob criptografado. Se os metadados devem ser definidos, certifique-se de chamar o método **get_blob_metadata** primeiro para obter os metadados de criptografia atuais e evitar gravações simultâneas enquanto os metadados estão sendo definidos.
> * Habilite o sinalizador **require_encryption** no objeto de serviço para usuários que devem funcionar apenas com dados criptografados. Consulte abaixo para obter mais informações.

A biblioteca de cliente de armazenamento espera o KEK e o resolvedor de chave fornecidos para implementar a interface a seguir. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) suporte para o gerenciamento Kek do Python está pendente e será integrado a essa biblioteca quando for concluído.

## <a name="client-api--interface"></a>API/interface do cliente
Depois que um objeto de serviço de armazenamento (ou seja, blockblobservice) tiver sido criado, o usuário poderá atribuir valores aos campos que constituem uma política de criptografia: key_encryption_key, key_resolver_function e require_encryption. Os usuários podem fornecer apenas um KEK, apenas um resolvedor ou ambos. key_encryption_key é o tipo de chave básica que é identificado usando um identificador de chave e que fornece a lógica para encapsular/desencapsular. key_resolver_function é usado para resolver uma chave durante o processo de descriptografia. Ele retorna um KEK válido dado um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias chaves que são gerenciadas em vários locais.

O KEK deve implementar os seguintes métodos para criptografar os dados com êxito:

* wrap_key(cek): Encapsula o CEK especificado (bytes) usando um algoritmo da opção do usuário. Retorna a chave encapsulada.
* get_key_wrap_algorithm(): Retorna o algoritmo usado para encapsular as chaves.
* get_kid(): Retorna a ID da chave de cadeia de caracteres para este KEK.
  O KEK deve implementar os seguintes métodos para descriptografar dados com êxito:
* unwrap_key(cek, algorithm): Retorna a forma desencapsulada do CEK especificado usando o algoritmo de cadeia de caracteres especificado.
* get_kid(): Retorna uma ID de chave de cadeia de caracteres para este KEK.

O resolvedor de chave deve, pelo menos, implementar um método que, dado uma ID de chave, retorne o KEK correspondente que implementa a interface acima. Somente esse método deve ser atribuído à propriedade key_resolver_function no objeto de serviço.

* Para criptografia, a chave é usada sempre e a ausência de uma chave resultará em um erro.
* Para descriptografia:

  * O resolvedor de chave é invocado se especificado para obter a chave. Se o resolvedor for especificado, mas não tiver um mapeamento para o identificador de chave, um erro será gerado.
  * Se o resolvedor não for especificado, mas uma chave for especificada, a chave será usada se seu identificador corresponder ao identificador de chave necessário. Se o identificador não corresponder, um erro será gerado.

    Os exemplos de criptografia no Azure. Storage. Samples demonstram um cenário de ponta a ponta mais detalhado para BLOBs, filas e tabelas.
      As implementações de exemplo do KEK e do resolvedor de chave são fornecidas nos arquivos de exemplo como keywrapper e keyresolvetor, respectivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Opcionalmente, os usuários podem habilitar um modo de operação em que todos os uploads e downloads devem ser criptografados. Nesse modo, tentativas de carregar dados sem uma política de criptografia ou baixar dados que não estão criptografados no serviço falharão no cliente. O sinalizador **require_encryption** no objeto de serviço controla esse comportamento.

### <a name="blob-service-encryption"></a>Criptografia do serviço blob
Defina os campos de política de criptografia no objeto blockblobservice. Todo o resto será manipulado pela biblioteca de cliente internamente.

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

### <a name="queue-service-encryption"></a>serviço Fila criptografia
Defina os campos de política de criptografia no objeto queueservice. Todo o resto será manipulado pela biblioteca de cliente internamente.

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

### <a name="table-service-encryption"></a>Criptografia do serviço tabela
Além de criar uma política de criptografia e defini-la nas opções de solicitação, você deve especificar um **encryption_resolver_function** no **tableservice**ou definir o atributo Encrypt em entityproperty.

### <a name="using-the-resolver"></a>Usando o resolvedor

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
Conforme mencionado acima, uma propriedade pode ser marcada para criptografia armazenando-a em um objeto Entityproperty e definindo o campo Encrypt.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Criptografia e desempenho
Observe que criptografar os dados de armazenamento resulta em sobrecarga de desempenho adicional. A chave de conteúdo e a IV devem ser geradas, o conteúdo em si deve ser criptografado e os metadados adicionais devem ser formatados e carregados. Essa sobrecarga variará dependendo da quantidade de dados que estão sendo criptografados. Recomendamos que os clientes sempre testem seus aplicativos quanto ao desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes
* Baixar a [biblioteca de cliente do armazenamento do Azure para o pacote PyPi Java](https://pypi.python.org/pypi/azure-storage)
* Baixar a [biblioteca de cliente do armazenamento do Azure para o código-fonte do Python do GitHub](https://github.com/Azure/azure-storage-python)
