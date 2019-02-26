---
title: Trabalhar com as Reliable Collections | Documentos da Microsoft
description: Aprenda as melhores práticas para trabalhar com as Reliable Collections.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2019
ms.author: aljo
ms.openlocfilehash: 38aef6e5ba65f67a1dd30ba2c18e180cd92624c6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805318"
---
# <a name="working-with-reliable-collections"></a>Trabalhar com as Reliable Collections
Recursos de infraestrutura do serviço oferece um modelo de programação com monitoração de estado disponível para os desenvolvedores do .NET através de coleções fiáveis. Especificamente, o Service Fabric fornece classes de fila do reliable e de dicionário fiável. Quando usar essas classes, seu estado é particionado (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma partição (para semântica ACID). Vamos examinar um uso típico de um objeto de dicionário fiável e ver o que está realmente fazendo.

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
}
```

Todas as operações em objetos de dicionário fiável (exceto para ClearAsync, que não pode ser executado), exigem um objeto de ITransaction. Este objeto tem associados, pelo que quaisquer e todas as alterações que está a tentar fazer para qualquer dicionário fiável e/ou confiável fila de espera de objetos dentro de uma única partição. Adquirir um ITransaction objeto chamando a partição do método de CreateTransaction do StateManager.

No código acima, o objeto de ITransaction é transmitido ao método de AddAsync um dicionário fiável. Internamente, os métodos de dicionário que aceitam uma chave de usar um bloqueio de Leitor/gravador associado à chave. Se o método modifica o valor da chave, o método usa um bloqueio de escrita na chave e se o método lê apenas a partir do valor da chave, um bloqueio de leitura com a, em seguida, é colocado na chave. Uma vez que AddAsync modifica o valor da chave para o valor novo, no passado, o bloqueio de escrita da chave é utilizado. Então, se tentarem threads 2 (ou mais) adicionar valores com a mesma chave ao mesmo tempo, um thread irá adquirir o bloqueio de escrita e os outros threads irão bloquear. Por predefinição, o bloco de métodos para até 4 segundos adquirir o bloqueio; Após 4 segundos, os métodos de lançar uma TimeoutException. Sobrecargas do método existem que lhe permite passar um valor de tempo limite explícito, se preferir.

Normalmente, escrever seu código para reagir a uma TimeoutException ao capturando-lo e repetir a operação de toda a operação (como mostrado no código acima). No meu código simple, estou apenas chamando Task.Delay passando 100 milissegundos cada vez. Mas, na realidade, poderá ser melhor usar algum tipo de atraso de início de término exponencial em vez disso.

Assim que o bloqueio seja adquirido, AddAsync adiciona as referências de objeto de chave e valor a um dicionário temporário interno associado ao objeto de ITransaction. Isso é feito para lhe fornecer a semântica de leitura-your-own-escritas. Ou seja, depois de chamar AddAsync, uma chamada posterior para TryGetValueAsync (usando o mesmo objeto de ITransaction) irá devolver o valor, mesmo que ainda não consolidada a transação. Em seguida, AddAsync serializa a chave e valor de objetos para matrizes de bytes e acrescenta essas matrizes de bytes para um ficheiro de registo no nó local. Por fim, AddAsync envia as matrizes de byte para todas as réplicas secundárias, para que eles tenham as mesmas informações de chave/valor. Apesar das informações de chave/valor foi escritas para um ficheiro de registo, as informações não são consideradas parte do dicionário até que a transação que estão associados foi confirmada.

No código acima, a chamada para CommitAsync consolida todas as operações da transação. Especificamente, ele acrescenta informações de consolidação para o ficheiro de registo no nó local e também envia o registo de consolidação para todas as réplicas secundárias. Depois de um quórum (maioria) das réplicas respondeu, todas as alterações de dados são consideradas permanentes e nenhum bloqueio associado às chaves que foram manipuladas por meio do objeto de ITransaction é lançado para que outros threads/transações pode manipular as mesmas chaves e suas valores.

Se não é chamado de CommitAsync (geralmente devido a uma exceção a ser lançada), o objeto de ITransaction obtém descartado. Ao descartar um objeto de ITransaction não consolidado, o Service Fabric acrescenta informações de anulação de ficheiro de registo do nó local e nada precisa ser enviada a qualquer uma das réplicas secundárias. E, em seguida, é lançado nenhum bloqueio associado às chaves que foram manipuladas por meio da transação.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-los
Agora que compreende como as coleções fiáveis funcionam internamente, vamos dar uma olhada em alguns misuses comuns delas. Consulte o código a seguir:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ao trabalhar com um dicionário regular do .NET, pode adicionar um chave/valor ao dicionário e, em seguida, altere o valor de uma propriedade (como LastLogin). No entanto, esse código não funcionará corretamente com um dicionário fiável. Lembre-se com a discussão anterior, a chamada para AddAsync serializa os objetos de chave/valor para matrizes de bytes e, em seguida, guarda as matrizes de um ficheiro local e também envia-os para as réplicas secundárias. Se posteriormente alterar uma propriedade, isto altera o valor da propriedade na memória apenas; ela não afeta o arquivo local ou os dados enviados para as réplicas. Se o processo falhar, o que está na memória é descartada. Quando um novo processo é iniciado ou se outra réplica se tornar primária, o antigo valor da propriedade é o que está disponível.

Não consigo enfatizar suficiente como é fácil fazer o tipo de erro mostrado acima. E, apenas aprenderá sobre o erro if/quando que o processo de fica inativo. A maneira correta de escrever o código é simplesmente inverter as duas linhas:


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
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Novamente, com dicionários regulares do .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para procurar um valor. Se o valor existir, o desenvolvedor altera o valor de uma propriedade. No entanto, com as reliable collections, esse código exibe o mesmo problema, como já explicado: **não deve modificar um objeto assim que tiver dado-la numa coleção fiável.**

É a maneira correta de atualizar um valor numa coleção fiável obter uma referência ao valor existente e considerar o objeto referenciado por esta referência imutável. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, pode modificar o estado do novo objeto e escrever o novo objeto à coleção para que ele obtém serializado para matrizes de bytes, anexado o ficheiro local e enviadas para as réplicas. Após efetuar a alteração ou alterações à, os objetos na memória, o arquivo local e todas as réplicas têm o mesmo Estado exato. Tudo o que é bom!

O código abaixo mostra a maneira correta de atualizar um valor numa coleção fiável:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
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

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir os tipos de dados imutáveis para impedir que o erro do Programador
Idealmente, gostaríamos do compilador para relatar erros quando acidentalmente de produzir o código que desenvolvendo o estado de um objeto que deverá para considerar um imutável. No entanto, o C# compilador não tem a capacidade de fazer isso. Portanto, para evitar potenciais erros de programador, é altamente recomendável que definir os tipos de usar com as reliable collections ser tipos imutáveis. Especificamente, isso significa que siga principais tipos de valor (por exemplo, números [Int32, UInt64, etc.], DateTime, Guid, período de tempo e assim por diante). Também pode utilizar a cadeia de caracteres. É melhor evitar as propriedades de coleção como serializando e desserializar-las com freqüência pode prejudicar desempenho. No entanto, se pretender utilizar as propriedades de coleção, recomendamos vivamente a utilização de. Biblioteca de coleções imutáveis da NET ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Essa biblioteca está disponível para download em http://nuget.org. Também recomendamos selar suas classes e fazendo campos só de leitura, sempre que possível.

O tipo de UserInfo abaixo demonstra como definir um imutável tipo tirar partido de recomendações mencionados anteriormente.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
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

O tipo de ItemId também é um tipo imutável, conforme mostrado aqui:

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

## <a name="schema-versioning-upgrades"></a>Controle de versão do esquema (atualizações)
Internamente, as coleções fiáveis serializar os objetos usando. DataContractSerializer do NET. Os objetos serializados serem mantidos no disco local da réplica primária e também são transmitidos para as réplicas secundárias. Como o seu serviço amadurece, é provável que vai querer alterar o tipo de dados (esquema), que seu serviço requer. Controlo de versões de abordagem dos seus dados com muito cuidado. Primeiro e mais importante, deve sempre ser capaz de anular a serialização de dados antigos. Especificamente, isso significa que seu código de desserialização tem de ser compatíveis infinitamente com versões anteriores: Versão 333 do seu código de serviço tem de ser capaz de operar em dados colocados numa coleção fiável, por versão 1 do seu código de serviço, há cinco anos.

Além disso, o código de serviço é atualizado um domínio de atualização de cada vez. Por isso, durante uma atualização, tem duas versões diferentes do seu código de serviço em execução em simultâneo. Deve evitar a nova versão do seu código de serviço utilizar o novo esquema conforme as versões antigas do seu código de serviço podem não ser capazes de lidar com o novo esquema. Sempre que possível, deve conceber a cada versão do seu serviço para ser compatível por uma versão. Especificamente, isso significa que a V1 do seu código de serviço deve ser capazes de ignorar quaisquer elementos de esquema não processar explicitamente. No entanto, tem de ser capaz de guardar todos os dados não explicitamente o conhece e write-lo de volta ao atualizar uma chave de dicionário ou valor.

> [!WARNING]
> Embora possa modificar o esquema de uma chave, certifique-se de que o código de hash de sua chave e algoritmos de é igual a são estáveis. Se alterar qualquer um desses algoritmos como funcionam, não será capaz de pesquisar novamente a chave no dicionário fiável.
> Cadeias de caracteres do .NET pode ser utilizadas como uma chave, utilize a cadeia em si, como a chave –, mas não utilize o resultado da String.GetHashCode como a chave.

Em alternativa, pode efetuar o que normalmente é referido como uma atualização de dois. Com uma atualização em duas fases, atualizar o serviço do V1 para o V2: V2 contém o código que sabe como lidar com a nova alteração de esquema, mas esse código não é executado. Quando o código de V2 lê dados V1, ele opera no mesmo e escreve dados V1. Em seguida, após a atualização estiver concluída em domínios de atualização de tudo, pode alguma forma Sinalizar para as instâncias de V2 em execução que a atualização foi concluída. (Uma forma de sinal é implementar uma atualização de configuração; este é o que torna isso uma atualização em duas fases.) Agora, as instâncias de V2 podem ler os dados de V1, convertê-lo aos dados da V2, operar no mesmo e escrevê-lo como dados da V2. Quando outras instâncias de ler os dados de V2, não precisa convertê-lo, eles apenas operam nele e escrever os dados da V2.

## <a name="next-steps"></a>Próximos Passos
Para saber mais sobre como criar contratos de reencaminhar dados compatíveis, consulte [contratos de dados de compatibilidade](https://msdn.microsoft.com/library/ms731083.aspx)

Para obter melhores práticas em contratos de dados de controlo de versões, veja [versão do contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx)

Para saber como implementar contratos de dados com tolerância a falhas de versão, consulte [retornos de chamada de serialização tolerância a falhas da versão](https://msdn.microsoft.com/library/ms733734.aspx)

Para saber como fornecer uma estrutura de dados que pode interoperar entre várias versões, veja [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)