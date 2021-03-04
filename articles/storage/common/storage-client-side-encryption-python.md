---
title: Encriptação do lado do cliente com Python
titleSuffix: Azure Storage
description: A Biblioteca do Cliente de Armazenamento Azure para Python suporta encriptação do lado do cliente para máxima segurança para as suas aplicações de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ffdfd4dc8a81587d757e3f9853f1bb34e0b93c0d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043750"
---
# <a name="client-side-encryption-with-python"></a>Encriptação do lado do cliente com Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Descrição Geral
A [Biblioteca do Cliente de Armazenamento Azure para Python](https://pypi.python.org/pypi/azure-storage) suporta encriptar dados dentro das aplicações do cliente antes de fazer o upload para o Azure Storage e desencriptar dados enquanto descarrega para o cliente.

> [!NOTE]
> A biblioteca Azure Storage Python está em pré-visualização.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Encriptação e desencriptação através da técnica do envelope
Os processos de encriptação e desencriptação seguem a técnica do envelope.

### <a name="encryption-via-the-envelope-technique"></a>Encriptação através da técnica do envelope
A encriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca de clientes de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.
2. Os dados do utilizador são encriptados utilizando este CEK.
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica, que é gerida localmente.
   A biblioteca do cliente de armazenamento nunca tem acesso à KEK. A biblioteca invoca o algoritmo de embrulho chave que é fornecido pelo KEK. Os utilizadores podem optar por utilizar fornecedores personalizados para o embrulho/desembrulhar se desejar.
4. Os dados encriptados são então enviados para o serviço de Armazenamento Azure. A chave embrulhada juntamente com alguns metadados de encriptação adicionais é armazenada como metadados (numa bolha) ou interpolada com os dados encriptados (mensagens de fila e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Desencriptação através da técnica do envelope
A desencriptação através da técnica do envelope funciona da seguinte forma:

1. A biblioteca do cliente assume que o utilizador está a gerir a chave de encriptação (KEK) localmente. O utilizador não precisa de saber a chave específica que foi usada para encriptação. Em vez disso, uma chave de resolução, que resolve diferentes identificadores chave para as teclas, pode ser configurada e usada.
2. A biblioteca do cliente descarrega os dados encriptados juntamente com qualquer material de encriptação que seja armazenado no serviço.
3. A chave de encriptação de conteúdo embrulhado (CEK) é então desembrulhada (desencriptado) utilizando a chave de encriptação (KEK). Aqui novamente, a biblioteca de clientes não tem acesso à KEK. Simplesmente invoca o algoritmo de desembrulho do fornecedor personalizado.
4. A chave de encriptação de conteúdo (CEK) é então usada para desencriptar os dados encriptados do utilizador.

## <a name="encryption-mechanism"></a>Mecanismo de encriptação
A biblioteca do cliente de armazenamento utiliza [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para encriptar os dados do utilizador. Especificamente, o modo [de cadeia de blocos cifra (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona de forma um pouco diferente, por isso vamos discutir cada um deles aqui.

### <a name="blobs"></a>Blobs
A biblioteca do cliente suporta atualmente a encriptação de bolhas inteiras apenas. Especificamente, a encriptação é suportada quando os utilizadores usam os métodos **de criação*** . Para downloads, tanto os downloads completos como os de gama são suportados, e a paralelização tanto do upload como do download está disponível.

Durante a encriptação, a biblioteca do cliente gerará um Vetor de Inicialização aleatória (IV) de 16 bytes, juntamente com uma chave de encriptação de conteúdo aleatório (CEK) de 32 bytes, e executará encriptação do envelope dos dados blob usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como metadados blob juntamente com a bolha encriptada no serviço.

> [!WARNING]
> Se estiver a editar ou a carregar os seus próprios metadados para a bolha, tem de se certificar de que estes metadados são preservados. Se carregar novos metadados sem estes metadados, perder-se-ão os CEK, IV e outros metadados embrulhados e o conteúdo blob nunca mais será recuperado.
> 
> 

O download de uma bolha encriptada envolve a recuperação do conteúdo de toda a bolha utilizando os métodos de conveniência **get*** . O CEK embrulhado é desembrulhado e utilizado juntamente com o IV (armazenado como metadados blob neste caso) para devolver os dados desencriptados aos utilizadores.

O download de uma gama arbitrária **(obter*** métodos com parâmetros de alcance passados) na bolha encriptada envolve ajustar a gama fornecida pelos utilizadores de forma a obter uma pequena quantidade de dados adicionais que podem ser usados para desencriptar com sucesso a gama solicitada.

As bolhas de blocos e as bolhas de página só podem ser encriptadas/desencriptadas utilizando este esquema. Atualmente, não existe suporte para encriptar as bolhas dos apêndices.

### <a name="queues"></a>Filas
Uma vez que as mensagens de fila podem ser de qualquer formato, a biblioteca do cliente define um formato personalizado que inclui o Vetor de Inicialização (IV) e a chave de encriptação de conteúdo encriptado (CEK) no texto da mensagem.

Durante a encriptação, a biblioteca do cliente gera um IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa encriptação de envelope do texto de mensagem de fila usando esta informação. O CEK embrulhado e alguns metadados de encriptação adicionais são adicionados à mensagem de fila encriptada. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
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
3. O CEK embrulhado e alguns metadados de encriptação adicionais são então armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada \_ (ClientEncryptionMetadata1) é uma propriedade de cordas que detém a informação sobre IV, versão e chave embrulhada. A segunda propriedade reservada \_ (ClientEncryptionMetadata2) é uma propriedade binária que detém a informação sobre as propriedades encriptadas. A informação neste segundo imóvel \_ (ClientEncryptionMetadata2) está por si criptografada.
4. Devido a estas propriedades reservadas adicionais necessárias para encriptação, os utilizadores podem agora ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser inferior a 1MB.

   Note que apenas as propriedades de cordas podem ser encriptadas. Para que outros tipos de propriedades sejam encriptados, devem ser convertidos em cordas. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta para cordas (cordas cruas, não EntityProperties com tipo EdmType.STRING) após desencriptação.

   Para as tabelas, além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito armazenando estas propriedades em objetos TableEntity com o tipo definido para EdmType.STRING e encriptar definido para verdadeiro ou definir o encryption_resolver_function no objeto de serviço de mesa. Um resolver de encriptação é uma função que pega numa chave de partição, chave de linha e nome de propriedade e devolve um booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usará esta informação para decidir se uma propriedade deve ser encriptada enquanto escreve para o fio. O delegado também prevê a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar a propriedade A; caso contrário, encriptar propriedades A e B.) Note que não é necessário fornecer esta informação durante a leitura ou consulta de entidades.

### <a name="batch-operations"></a>Operações de Lote
Uma política de encriptação aplica-se a todas as linhas do lote. A biblioteca do cliente gerará internamente um novo IV aleatório e um CEK aleatório por linha no lote. Os utilizadores também podem optar por encriptar diferentes propriedades para cada operação no lote, definindo este comportamento na encriptação.
Se um lote for criado como gestor de contexto através do método do lote de tableservice() () método, a política de encriptação do serviço de mesa será automaticamente aplicada ao lote. Se um lote for criado explicitamente chamando o construtor, a política de encriptação deve ser aprovada como um parâmetro e deixada sem modificação durante toda a vida do lote.
Note que as entidades são encriptadas à medida que são inseridas no lote usando a política de encriptação do lote (as entidades NÃO são encriptadas no momento de cometer o lote usando a política de encriptação do serviço de mesa).

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades estão encriptadas, não é possível executar consultas que filtram uma propriedade encriptada.  Se tentar, os resultados serão incorretos, porque o serviço estaria a tentar comparar dados encriptados com dados não encriptados.
> 
> 
> Para efetuar operações de consulta, tem de especificar uma chave que seja capaz de resolver todas as teclas no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida a um fornecedor, a biblioteca do cliente lançará um erro. Para qualquer consulta que execute projeções laterais do servidor, a biblioteca do cliente adicionará as propriedades especiais de metadados de encriptação \_ (ClientEncryptionMetadata1 e \_ ClientEncryptionMetadata2) por padrão às colunas selecionadas.
> 
> [!IMPORTANT]
> Esteja atento a estes pontos importantes ao utilizar a encriptação do lado do cliente:
> 
> * Ao ler ou escrever para uma bolha encriptada, utilize comandos inteiros de upload de blob e comandos de descarregamento de gama/blob inteiro. Evite escrever para uma bolha encriptada utilizando operações de protocolo como Put Block, Put Block List, Write Pages ou Clear Pages; caso contrário, pode corromper a bolha encriptada e torná-la ilegível.
> * Para as tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar as propriedades encriptadas sem atualizar os metadados de encriptação.
> * Se definir metadados na bolha encriptada, poderá substituir os metadados relacionados com encriptação necessários para a desencriptação, uma vez que a definição de metadados não é aditivo. Isto também é verdade para instantâneos; evite especificar metadados enquanto cria uma imagem instantânea de uma bolha encriptada. Se os metadados embora tão devem ser definidos, certifique-se de ligar primeiro para o método **get_blob_metadata** para obter os metadados de encriptação atuais e evitar gravações simultâneas enquanto os metadados estão a ser definidos.
> * Ativar a bandeira **require_encryption** no objeto de serviço para utilizadores que devem funcionar apenas com dados encriptados. Veja abaixo mais informações.

A biblioteca do cliente de armazenamento espera que o KEK fornecido e a chave resolver para implementar a interface seguinte. O suporte [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para a gestão python KEK está pendente e será integrado nesta biblioteca quando estiver concluído.

## <a name="client-api--interface"></a>API / Interface do Cliente
Após a criação de um objeto de serviço de armazenamento (isto é, blockblobservice), o utilizador pode atribuir valores aos campos que constituem uma política de encriptação: key_encryption_key, key_resolver_function e require_encryption. Os utilizadores podem fornecer apenas um KEK, apenas um resolver, ou ambos. key_encryption_key é o tipo de chave básica que é identificado usando um identificador chave e que fornece a lógica para embrulhar/desembrulhar. key_resolver_function é usado para resolver uma chave durante o processo de desencriptação. Devolve um KEK válido dado um identificador de chave. Isto proporciona aos utilizadores a capacidade de escolher entre várias teclas que são geridas em vários locais.

O KEK deve implementar os seguintes métodos para encriptar com sucesso dados:

* wrap_key(cek): Enrola o CEK especificado (bytes) utilizando um algoritmo da escolha do utilizador. Devolve a chave embrulhada.
* get_key_wrap_algorithm:): Devolve o algoritmo usado para embrulhar as teclas.
* get_kid:): Devolve o id da chave de cordas para este KEK.
  O KEK deve implementar os seguintes métodos para desencriptar com sucesso os dados:
* unwrap_key(cek, algoritmo): Devolve a forma desembrulhada do CEK especificado utilizando o algoritmo especificado em cadeia.
* get_kid:): Devolve uma chave de corda para este KEK.

A chave resolver deve, pelo menos, implementar um método que, dado um id chave, devolve a correspondente KEK implementando a interface acima. Apenas este método deve ser atribuído à propriedade key_resolver_function no objeto de serviço.

* Para encriptação, a chave é sempre utilizada e a ausência de uma chave resultará num erro.
* Para desencriptação:

  * A chave é invocada se especificada para obter a chave. Se o resolver for especificado mas não tiver um mapeamento para o identificador de chave, é lançado um erro.
  * Se o resolver não for especificado, mas uma chave for especificada, a chave é utilizada se o seu identificador corresponder ao identificador de teclas necessário. Se o identificador não corresponder, é lançado um erro.

    As amostras de encriptação em azure.storage.samples demonstram um cenário mais detalhado de ponta a ponta para bolhas, filas e tabelas.
      As implementações da amostra do KEK e do key resolver são fornecidas nos ficheiros de amostra como KeyWrapper e KeyResolver, respectivamente.

### <a name="requireencryption-mode"></a>Requereno modo desencriptação
Os utilizadores podem opcionalmente ativar um modo de funcionamento onde todos os uploads e downloads devem ser encriptados. Neste modo, as tentativas de carregar dados sem uma política de encriptação ou de descarregar dados que não sejam encriptados no serviço falharão no cliente. A bandeira **require_encryption** no objeto de serviço controla este comportamento.

### <a name="blob-service-encryption"></a>Encriptação do serviço blob
Descreva os campos de política de encriptação no objeto blockblobservice. Todo o resto será tratado pela biblioteca do cliente internamente.

# <a name="python-v12"></a>[Python v12](#tab/python)

Estamos neste momento a trabalhar para criar códigos que reflitam a versão 12.x das bibliotecas de clientes do Azure Storage. Para mais informações, consulte [anunciando o Azure Storage v12 Client Libraries](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

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
---

### <a name="queue-service-encryption"></a>Encriptação do serviço de fila
Desajei os campos de política de encriptação no objeto do serviço de fila. Todo o resto será tratado pela biblioteca do cliente internamente.

# <a name="python-v12"></a>[Python v12](#tab/python)

Estamos neste momento a trabalhar para criar códigos que reflitam a versão 12.x das bibliotecas de clientes do Azure Storage. Para mais informações, consulte [anunciando o Azure Storage v12 Client Libraries](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

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
---

### <a name="table-service-encryption"></a>Encriptação do serviço de mesa
Além de criar uma política de encriptação e defini-la nas opções de pedido, deve especificar uma **encryption_resolver_function** no **serviço de tabelas**, ou definir o atributo encriptado na EntityProperty.

### <a name="using-the-resolver"></a>Usando o resolver

# <a name="python-v12"></a>[Python v12](#tab/python)

Estamos neste momento a trabalhar para criar códigos que reflitam a versão 12.x das bibliotecas de clientes do Azure Storage. Para mais informações, consulte [anunciando o Azure Storage v12 Client Libraries](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

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
---

### <a name="using-attributes"></a>Utilização de atributos
Como mencionado acima, uma propriedade pode ser marcada para encriptação armazenando-a num objeto EntityProperty e definindo o campo de encriptação.

# <a name="python-v12"></a>[Python v12](#tab/python)

Estamos neste momento a trabalhar para criar códigos que reflitam a versão 12.x das bibliotecas de clientes do Azure Storage. Para mais informações, consulte [anunciando o Azure Storage v12 Client Libraries](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```
---

## <a name="encryption-and-performance"></a>Encriptação e desempenho
Note que encriptar os seus dados de armazenamento resulta em despesas adicionais de desempenho. A chave de conteúdo e IV devem ser gerados, o conteúdo em si deve ser encriptado, e os metadados adicionais devem ser formatados e carregados. Esta sobrecarga variará dependendo da quantidade de dados que estão a ser encriptados. Recomendamos que os clientes testem sempre as suas aplicações para desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Passos seguintes
* Descarregue a Biblioteca do [Cliente de Armazenamento Azure para o pacote Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Descarregue a Biblioteca do [Cliente de Armazenamento Azure para o Código Fonte python do GitHub](https://github.com/Azure/azure-storage-python)
