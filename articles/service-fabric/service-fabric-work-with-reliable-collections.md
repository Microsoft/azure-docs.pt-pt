---
title: Trabalhar com as Reliable Collections
description: Aprenda as melhores práticas para trabalhar com Coleções Fiáveis dentro de uma aplicação de Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787880"
---
# <a name="working-with-reliable-collections"></a>Trabalhar com as Reliable Collections
A Service Fabric oferece um modelo de programação imponente disponível para os desenvolvedores .NET através de Coleções Fiáveis. Especificamente, o Service Fabric fornece aulas de fila fiáveis e de fiáveis. Quando utiliza estas classes, o seu estado é dividido (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma divisória (para semântica ACID). Vamos olhar para um uso típico de um objeto dicionário fiável e ver o que ele está realmente a fazer.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

Todas as operações em objetos dicionários fiáveis (exceto o ClearAsync, que não é incodável), requerem um objeto ITransaction. Este objeto associou-lhe todas e quaisquer alterações que esteja a tentar fazer a qualquer dicionário fiável e/ou objetos de fila fiáveis dentro de uma única divisória. Você adquire um objeto ITransaction chamando o método de CriaTransção do StateManager da partição.

No código acima, o objeto ITransaction é passado para um método addAsync de um dicionário fiável. Internamente, os métodos do dicionário que aceitam uma chave tomam uma fechadura de leitor/escritor associada à chave. Se o método modificar o valor da chave, o método faz uma fechadura de escrita na chave e se o método apenas ler a partir do valor da chave, então é feita uma fechadura de leitura na tecla. Uma vez que o AddAsync modifica o valor da chave para o novo valor passado, o bloqueio de escrita da chave é tomado. Assim, se 2 (ou mais) fios tentarem adicionar valores com a mesma tecla ao mesmo tempo, um fio adquirirá o bloqueio de escrita e os outros fios bloquearão. Por predefinição, os métodos bloqueiam até 4 segundos para adquirir o bloqueio; após 4 segundos, os métodos lançam uma TimeoutException. Existem sobrecargas de métodos que lhe permitem passar um valor explícito de tempo limite, se preferir.

Normalmente, escreve o seu código para reagir a uma TimeoutException, capturando-a e reformulando toda a operação (como mostra o código acima). Neste simples código, estamos apenas a chamar Task.Delay passando 100 milissegundos de cada vez. Mas, na realidade, talvez seja melhor usar algum tipo de atraso exponencial.

Uma vez adquirido o bloqueio, o AddAsync adiciona as referências de chave e objeto de valor a um dicionário temporário interno associado ao objeto ITransaction. Isto é feito para lhe proporcionar semântica de leitura. Isto é, depois de ligar para AddAsync, uma chamada posterior para TryGetValueAsync usando o mesmo objeto ITransaction devolverá o valor mesmo que ainda não tenha cometido a transação.

> [!NOTE]
> Ligar para o TryGetValueAsync com uma nova transação devolverá uma referência ao último valor comprometido. Não modifique diretamente essa referência, pois contorna o mecanismo de persistência e replicação das alterações. Recomendamos que os valores só leiam para que a única maneira de alterar o valor de uma chave seja através de APIs de dicionário fiável.

Em seguida, addAsync serializa a sua chave e objetos de valor para byte arrays e anexa estes conjuntos byte a um ficheiro de log no nó local. Finalmente, a AddAsync envia as matrizes byte para todas as réplicas secundárias para que tenham a mesma informação chave/valor. Apesar de a informação chave/valor ter sido escrita para um ficheiro de registo, a informação não é considerada parte do dicionário até que a transação a que estão associados tenha sido comprometida.

No código acima, a chamada para a CommitAsync compromete todas as operações da transação. Especificamente, ele anexa a informação ao ficheiro de registo no nó local e também envia o registo de compromisso para todas as réplicas secundárias. Uma vez que um quórum (maioria) das réplicas tenha respondido, todas as alterações de dados são consideradas permanentes e quaisquer bloqueios associados com chaves que foram manipuladas através do objeto ITransaction são libertados para que outros fios/transações possam manipular as mesmas teclas e seus valores.

Se o CommitAsync não for chamado (normalmente devido a uma exceção a ser lançada), então o objeto ITransaction é eliminado. Ao eliminar um objeto ITransaction não comprometido, o Service Fabric anexa a informação para o ficheiro de registo do nó local e nada precisa de ser enviado para nenhuma das réplicas secundárias. E então, quaisquer fechaduras associadas com chaves que foram manipuladas através da transação são libertadas.

## <a name="volatile-reliable-collections"></a>Coleções fiáveis voláteis 
Em algumas cargas de trabalho, como uma cache replicada, por exemplo, a perda ocasional de dados pode ser tolerada. Evitar a persistência dos dados no disco pode permitir melhores latências e produçãos ao escrever para Dicionários Fiáveis. A compensação por falta de persistência é que, se ocorrer perda de quórum, ocorrerá uma perda total de dados. Uma vez que a perda de quórum é uma ocorrência rara, o aumento do desempenho pode valer a rara possibilidade de perda de dados para essas cargas de trabalho.

Atualmente, o suporte volátil só está disponível para Dicionários Fiáveis e Filas Fiáveis, e não ReliableConcurrentQueues. Consulte a lista de [Caveats](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) para informar a sua decisão sobre se deve usar coleções voláteis.

Para permitir um suporte volátil no seu serviço, coloque a ```HasPersistedState``` bandeira na declaração do tipo de serviço ```false``` para, assim:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Os serviços existentes não podem ser tornados voláteis, e vice-versa. Se assim o desejar, terá de eliminar o serviço existente e, em seguida, implantar o serviço com a bandeira atualizada. Isto significa que deve estar disposto a incorrer em perda total de dados se quiser alterar a ```HasPersistedState``` bandeira. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-las
Agora que entende como as coleções fiáveis funcionam internamente, vamos dar uma olhada em alguns usos comuns delas. Veja o código abaixo:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ao trabalhar com um dicionário regular .NET, pode adicionar uma chave/valor ao dicionário e, em seguida, alterar o valor de uma propriedade (como LastLogin). No entanto, este código não funcionará corretamente com um dicionário fiável. Lembre-se da discussão anterior, a chamada para AddAsync serializa os objetos chave/valor para byte arrays e, em seguida, guarda as matrizes para um arquivo local e também envia-os para as réplicas secundárias. Se mais tarde alterar um imóvel, isso altera apenas o valor da propriedade na memória; não afeta o ficheiro local nem os dados enviados para as réplicas. Se o processo falhar, o que está na memória é deitado fora. Quando um novo processo começa ou se outra réplica se torna primária, então o valor antigo da propriedade é o que está disponível.

Não posso sublinhar o quão fácil é cometer o tipo de erro acima demonstrado. E só vais aprender sobre o erro se/quando o processo for encerrado. A forma correta de escrever o código é simplesmente inverter as duas linhas:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Eis outro exemplo que mostra um erro comum:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Mais uma vez, com dicionários regulares .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para procurar um valor. Se o valor existir, o desenvolvedor altera o valor de uma propriedade. No entanto, com coleções fiáveis, este código apresenta o mesmo problema que já foi discutido: **não deve modificar um objeto depois de o ter dado a uma coleção fiável.**

A forma correta de atualizar um valor numa coleção fiável, é obter uma referência ao valor existente e considerar o objeto referido por esta referência imutável. Em seguida, crie um novo objeto que seja uma cópia exata do objeto original. Agora, pode modificar o estado deste novo objeto e escrever o novo objeto na coleção para que seja serializado para matrizes byte, anexados ao arquivo local e enviados para as réplicas. Depois de cometer as alterações, os objetos na memória, o ficheiro local e todas as réplicas têm o mesmo estado exato. Tudo vai bem!

O código abaixo mostra a forma correta de atualizar um valor numa coleção fiável:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erros do programador
Idealmente, gostaríamos que o compilador relatasse erros quando acidentalmente produz código que muta o estado de um objeto que se deve considerar imutável. Mas o compilador C# não tem a capacidade de fazer isto. Assim, para evitar potenciais bugs de programadores, recomendamos vivamente que defina os tipos que utiliza com coleções fiáveis para serem tipos imutáveis. Especificamente, isto significa que você se cinge aos tipos de valor principal (tais como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan, e similares). Também pode usar String. O melhor é evitar que as propriedades de recolha, uma vez que serializá-las e deserizar podem ferir frequentemente o desempenho. No entanto, se pretender utilizar propriedades de recolha, recomendamos vivamente a utilização de . Biblioteca de coleções imutáveis da NET[(System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Esta biblioteca está disponível para download a partir de https://nuget.org . Recomendamos também selar as suas aulas e fazer com que os campos só leiam sempre que possível.

O tipo UserInfo abaixo demonstra como definir um tipo imutável aproveitando as recomendações acima mencionadas.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

O tipo ItemId é também um tipo imutável como mostrado aqui:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Versão de schema (upgrades)
Internamente, as Coleções Fiáveis serializam os seus objetos utilizando . DataContractSerializer da NET. Os objetos serializados são persistidos ao disco local da réplica primária e também são transmitidos às réplicas secundárias. À medida que o seu serviço amadurece, é provável que queira alterar o tipo de dados (esquema) que o seu serviço necessita. Abordar a versão dos seus dados com muito cuidado. Em primeiro lugar, deve ser sempre capaz de deserizar dados antigos. Especificamente, isto significa que o seu código de dessaseialização deve ser infinitamente compatível com retro-costas: a versão 333 do seu código de serviço deve ser capaz de operar em dados colocados numa recolha fiável pela versão 1 do seu código de serviço há 5 anos.

Além disso, o código de serviço é atualizado um domínio de atualização de cada vez. Assim, durante uma atualização, tem duas versões diferentes do seu código de serviço em execução simultaneamente. Deve evitar que a nova versão do seu código de serviço utilize o novo esquema, uma vez que as versões antigas do seu código de serviço podem não conseguir lidar com o novo esquema. Quando possível, deve conceber cada versão do seu serviço para ser compatível com uma versão. Especificamente, isto significa que o V1 do seu código de serviço deve ser capaz de ignorar quaisquer elementos de esquema que não manuseie explicitamente. No entanto, deve ser capaz de guardar quaisquer dados que não saiba explicitamente e escrevê-lo de volta ao atualizar uma chave ou valor do dicionário.

> [!WARNING]
> Enquanto pode modificar o esquema de uma chave, deve garantir que o código de haxixe da sua chave e algoritmos iguais são estáveis. Se alterar o funcionamento de qualquer um destes algoritmos, não poderá procurar novamente a chave dentro do dicionário fiável.
> .NET Strings pode ser usado como uma chave, mas usar a própria corda como chave -- não use o resultado de String.GetHashCode como a chave.

Em alternativa, pode executar o que normalmente é referido como uma atualização de dois. Com uma atualização em duas fases, atualiza o seu serviço de V1 para V2: O V2 contém o código que sabe lidar com a nova alteração de esquema, mas este código não executa. Quando o código V2 lê os dados V1, opera neles e escreve dados V1. Em seguida, depois de a atualização estar completa em todos os domínios de upgrade, pode de alguma forma sinalizar para as instâncias V2 em execução que a atualização está completa. (Uma maneira de sinalizar isto é lançar uma atualização de configuração; isto é o que faz com que isto seja uma atualização em duas fases.) Agora, as instâncias V2 podem ler dados V1, convertê-lo em dados V2, operar neles, e escrevê-lo como dados V2. Quando outros casos lêem dados V2, não precisam de os converter, apenas operam neles e escrevem dados V2.

## <a name="next-steps"></a>Próximos passos
Para conhecer a criação de contratos de dados compatíveis a prazo, consulte [Contratos de Dados Compatíveis a Prazo](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Para conhecer as melhores práticas sobre a versão dos contratos de dados, consulte [a versão do contrato de dados](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Para aprender a implementar contratos de dados tolerantes de versão, consulte [as callbacks de serialização tolerantes à versão](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Para aprender a fornecer uma estrutura de dados que possa interoperar em várias versões, consulte [iExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject)
