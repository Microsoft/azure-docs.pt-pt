---
title: Trabalhando com coleções confiáveis | Microsoft Docs
description: Conheça as práticas recomendadas para trabalhar com coleções confiáveis.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2019
ms.author: atsenthi
ms.openlocfilehash: 2d1284115a35881087e0ced0ee735ea38ce3f5ce
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598717"
---
# <a name="working-with-reliable-collections"></a>Trabalhar com as Reliable Collections
O Service Fabric oferece um modelo de programação com estado disponível para desenvolvedores do .NET por meio de coleções confiáveis. Especificamente, Service Fabric fornece classes confiáveis de dicionário e fila confiável. Quando você usa essas classes, seu estado é particionado (para escalabilidade), replicado (para disponibilidade) e transacionado em uma partição (para semântica ACID). Vamos examinar um uso típico de um objeto Dictionary confiável e ver o que ele está realmente fazendo.

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

Todas as operações em objetos de dicionário confiáveis (exceto para ClearAsync, que não são desnecessárias) exigem um objeto ITransaction. Este objeto está associado a ele qualquer e todas as alterações que você está tentando fazer em qualquer dicionário confiável e/ou objetos de fila confiável em uma única partição. Você adquire um objeto ITransaction chamando o método CreateTransaction de StateManager's da partição.

No código acima, o objeto ITransaction é passado para um método addasync de um dicionário confiável. Internamente, os métodos de dicionário que aceitam uma chave usam um bloqueio de leitor/gravador associado à chave. Se o método modificar o valor da chave, o método usará um bloqueio de gravação na chave e, se o método ler apenas o valor da chave, um bloqueio de leitura será obtido na chave. Como addasync modifica o valor da chave para o novo valor passado, o bloqueio de gravação da chave é obtido. Portanto, se 2 (ou mais) threads tentarem adicionar valores com a mesma chave ao mesmo tempo, um thread adquirirá o bloqueio de gravação e os outros threads serão bloqueados. Por padrão, os métodos são bloqueados por até 4 segundos para adquirir o bloqueio; após 4 segundos, os métodos geram um TimeoutException. As sobrecargas de método existem, permitindo que você passe um valor de tempo limite explícito se preferir.

Normalmente, você escreve seu código para reagir a um TimeoutException, capturando-o e repetindo a operação inteira (conforme mostrado no código acima). No meu código simples, estou apenas chamando Task. Delay passando 100 milissegundos a cada vez. Mas, na realidade, você pode ser melhor usar algum tipo de atraso de retirada exponencial em vez disso.

Depois que o bloqueio é adquirido, addasync adiciona as referências de objeto de chave e valor a um dicionário temporário interno associado ao objeto ITransaction. Isso é feito para fornecer a semântica de leitura de sua própria gravação. Ou seja, depois de chamar addasync, uma chamada posterior para TryGetValueAsync (usando o mesmo objeto ITransaction) retornará o valor mesmo que você ainda não tenha confirmado a transação. Em seguida, addasync serializa seus objetos de chave e valor para matrizes de bytes e acrescenta essas matrizes de bytes a um arquivo de log no nó local. Por fim, addasync envia as matrizes de bytes para todas as réplicas secundárias para que elas tenham as mesmas informações de chave/valor. Embora as informações de chave/valor tenham sido gravadas em um arquivo de log, as informações não são consideradas parte do dicionário até que a transação à qual elas estão associadas tenha sido confirmada.

No código acima, a chamada para CommitAsync confirma todas as operações da transação. Especificamente, ele acrescenta informações de confirmação ao arquivo de log no nó local e também envia o registro de confirmação para todas as réplicas secundárias. Depois que um quorum (maioria) das réplicas tiver respondido, todas as alterações de dados serão consideradas permanentes e todos os bloqueios associados às chaves que foram manipuladas por meio do objeto ITransaction serão liberados para que outros threads/transações possam manipular as mesmas chaves e suas os.

Se CommitAsync não for chamado (geralmente devido a uma exceção sendo gerada), o objeto ITransaction será Descartado. Ao descartar um objeto ITransaction não confirmado, Service Fabric acrescenta informações de anulação ao arquivo de log do nó local e nada precisa ser enviado a nenhuma das réplicas secundárias. E, em seguida, todos os bloqueios associados às chaves que foram manipuladas por meio da transação são liberados.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-las
Agora que você entende como as coleções confiáveis funcionam internamente, vamos dar uma olhada em algumas dessas reutilizações comuns. Consulte o código abaixo:

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

Ao trabalhar com um dicionário comum do .NET, você pode adicionar uma chave/valor ao dicionário e, em seguida, alterar o valor de uma propriedade (como LastLogin). No entanto, esse código não funcionará corretamente com um dicionário confiável. Lembre-se da discussão anterior, a chamada para addasync serializa os objetos de chave/valor para matrizes de bytes e, em seguida, salva as matrizes em um arquivo local e também as envia para as réplicas secundárias. Se você alterar uma propriedade posteriormente, isso altera o valor da propriedade somente na memória; Ele não afeta o arquivo local ou os dados enviados para as réplicas. Se o processo falhar, o que está na memória será jogado fora. Quando um novo processo é iniciado ou se outra réplica se torna primária, o valor antigo da propriedade é o que está disponível.

Não posso enfatizar o suficiente como é fácil fazer o tipo de erro mostrado acima. E você só aprenderá sobre o erro se/quando o processo falhar. A maneira correta de escrever o código é simplesmente reverter as duas linhas:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Aqui está outro exemplo que mostra um erro comum:

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

Novamente, com dicionários regulares do .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para pesquisar um valor. Se o valor existir, o desenvolvedor alterará o valor de uma propriedade. No entanto, com coleções confiáveis, esse código exibe o mesmo problema que já foi discutido: **você não deve modificar um objeto depois de tê-lo atribuído a uma coleção confiável.**

A maneira correta de atualizar um valor em uma coleção confiável é obter uma referência ao valor existente e considerar o objeto referido por essa referência imutável. Em seguida, crie um novo objeto que seja uma cópia exata do objeto original. Agora, você pode modificar o estado desse novo objeto e gravar o novo objeto na coleção para que ele seja serializado em matrizes de bytes, anexado ao arquivo local e enviado para as réplicas. Depois de confirmar a (s) alteração (ões), os objetos na memória, o arquivo local e todas as réplicas têm o mesmo estado exato. Tudo está bom!

O código a seguir mostra a maneira correta de atualizar um valor em uma coleção confiável:

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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erros de programador
Idealmente, gostaríamos que o compilador Relate erros quando você acidentalmente produza código que modificava o estado de um objeto que você deve considerar imutável. Mas, o C# compilador não tem a capacidade de fazer isso. Portanto, para evitar possíveis bugs de programador, é altamente recomendável que você defina os tipos usados com coleções confiáveis para serem tipos imutáveis. Especificamente, isso significa que você se fixa a tipos de valor de núcleo (como números [Int32, UInt64, etc.], DateTime, GUID, TimeSpan e like). Você também pode usar a cadeia de caracteres. É melhor evitar as propriedades de coleção, pois a serialização e a desserialização podem afetar o desempenho com frequência. No entanto, se você quiser usar propriedades de coleção, é altamente recomendável o uso de. Biblioteca de coleções imutáveis da rede ([System. Collections. imutável](https://www.nuget.org/packages/System.Collections.Immutable/)). Esta biblioteca está disponível para download no https://nuget.org. Também recomendamos lacrar suas classes e fazer com que os campos sejam somente leitura sempre que possível.

O tipo de UserInfo a seguir demonstra como definir um tipo imutável aproveitando as recomendações mencionadas anteriormente.

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

O tipo ItemId também é um tipo imutável, como mostrado aqui:

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

## <a name="schema-versioning-upgrades"></a>Controle de versão de esquema (atualizações)
Internamente, as coleções confiáveis serializam seus objetos usando o. DataContractSerializer da NET. Os objetos serializados são persistidos no disco local da réplica primária e também são transmitidos para as réplicas secundárias. À medida que o serviço amadurece, é provável que você queira alterar o tipo de dados (esquema) exigido pelo seu serviço. Aborde o controle de versão de seus dados com muito cuidado. Primeiro, você sempre deve ser capaz de desserializar dados antigos. Especificamente, isso significa que o código de desserialização deve ser infinitamente compatível com versões anteriores: A versão 333 do seu código de serviço deve ser capaz de operar em dados colocados em uma coleção confiável pela versão 1 do seu código de serviço há cinco anos.

Além disso, o código de serviço é atualizado um domínio de atualização por vez. Portanto, durante uma atualização, você tem duas versões diferentes do código de serviço em execução simultaneamente. Você deve evitar que a nova versão do seu código de serviço use o novo esquema, já que as versões antigas do seu código de serviço podem não ser capazes de lidar com o novo esquema. Quando possível, você deve criar cada versão do serviço para ser compatível com uma versão. Especificamente, isso significa que v1 do seu código de serviço deve ser capaz de ignorar qualquer elemento de esquema que ele não manipula explicitamente. No entanto, ele deve ser capaz de salvar quaisquer dados que não saiba explicitamente e escrevê-los novamente ao atualizar uma chave ou um valor de dicionário.

> [!WARNING]
> Embora seja possível modificar o esquema de uma chave, você deve garantir que o código de hash da chave e os algoritmos de Equals sejam estáveis. Se você alterar a forma como um desses algoritmos Opera, não será possível pesquisar a chave no dicionário confiável novamente.
> As cadeias de caracteres do .NET podem ser usadas como uma chave, mas usam a própria cadeia de caracteres como a chave – não usam o resultado de String. GetHashCode como a chave.

Como alternativa, você pode executar o que normalmente é conhecido como duas atualizações. Com uma atualização de duas fases, você atualiza seu serviço de v1 para v2: O v2 contém o código que sabe como lidar com a nova alteração de esquema, mas esse código não é executado. Quando o código v2 lê dados v1, ele opera e grava dados v1. Em seguida, depois que a atualização for concluída em todos os domínios de atualização, você poderá, de alguma forma, sinalizar para as instâncias V2 em execução que a atualização foi concluída. (Uma maneira de sinalizar isso é distribuir uma atualização de configuração; isso é o que faz isso uma atualização de duas fases.) Agora, as instâncias v2 podem ler dados v1, convertê-los em dados v2, operar nele e escrevê-los como dados v2. Quando outras instâncias lêem dados v2, elas não precisam convertê-los, simplesmente operam nele e gravam dados v2.

## <a name="next-steps"></a>Próximos Passos
Para saber mais sobre como criar contratos de dados compatíveis com o encaminhamento, consulte [contratos de dados compatíveis](https://msdn.microsoft.com/library/ms731083.aspx) com o encaminhamento

Para aprender as práticas recomendadas sobre o controle de versão de contratos de dados, consulte [controle de versão de contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx)

Para saber como implementar contratos de dados tolerantes a versões, consulte [retornos de chamada de serialização tolerantes à versão](https://msdn.microsoft.com/library/ms733734.aspx)

Para saber como fornecer uma estrutura de dados que possa interoperar em várias versões, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)