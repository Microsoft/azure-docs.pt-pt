---
title: Trabalhar com as Reliable Collections
description: Aprenda as melhores práticas para trabalhar com Coleções Fiáveis dentro de uma aplicação Azure Service Fabric.
ms.topic: conceptual
ms.date: 02/22/2019
ms.openlocfilehash: 4a1f48d9523e5d753c222f0526e210a30e1927e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645978"
---
# <a name="working-with-reliable-collections"></a>Trabalhar com as Reliable Collections
O Service Fabric oferece um modelo de programação imponente disponível para os desenvolvedores .NET através de Coleções Fiáveis. Especificamente, o Service Fabric fornece dicionário fiável e aulas de fila fiáveis. Quando utiliza estas aulas, o seu estado é dividido (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma partição (para semântica acid). Vamos ver um uso típico de um objeto de dicionário fiável e ver o que está realmente a fazer.

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

Todas as operações em objetos de dicionário fiáveis (exceto clearAsync, que não é indomável), requerem um objeto ITransac. Este objeto associou-se a ele qualquer e todas as alterações que está a tentar fazer a qualquer dicionário fiável e/ou objetos de fila fiáveis dentro de uma única divisória. Adquire um objeto ITransac, ligando para o método CreateTransaction da partição.

No código acima, o objeto ITransac é passado para um método AddAsync de um dicionário fiável. Internamente, os métodos do dicionário que aceitam uma chave levam um bloqueio de leitor/escritor associado à chave. Se o método modificar o valor da chave, o método pega num bloqueio de escrita na chave e se o método apenas ler a partir do valor da chave, então um bloqueio de leitura é tomado na chave. Uma vez que addasync modifica o valor da chave para o novo valor passado, o bloqueio de escrita da chave é tomado. Assim, se 2 (ou mais) fios tentarem adicionar valores com a mesma tecla ao mesmo tempo, um fio adquirirá o bloqueio de escrita, e os outros fios bloquearão. Por predefinição, os métodos bloqueiam até 4 segundos para adquirir o bloqueio; após 4 segundos, os métodos lançam uma TimeoutException. Existem sobrecargas de método que lhe permitem passar um valor de tempo explícito, se preferir.

Normalmente, escreve o seu código para reagir a uma TimeoutException, capturando-a e reexperimentando toda a operação (como indicado no código acima). No meu código simples, estou apenas a chamar task.Delay passando 100 milissegundos de cada vez. Mas, na realidade, talvez seja melhor usar algum tipo de atraso exponencial.

Uma vez adquirido o bloqueio, addAsync adiciona as referências de objetochave e valor a um dicionário temporário interno associado ao objeto ITransac. Isto é feito para lhe fornecer semântica de leitura da sua própria escrita. Ou seja, depois de ligar para addasync, uma chamada posterior para TryGetValueAsync (usando o mesmo objeto ITransac) devolverá o valor mesmo que ainda não tenha cometido a transação. Em seguida, addAsync serializa a sua chave e valoriza objetos para bytemarrays e anexa estas matrizes byte a um ficheiro de log no nó local. Finalmente, addAsync envia as matrizes byte para todas as réplicas secundárias para que tenham a mesma informação chave/valor. Embora a informação chave/valor tenha sido escrita num ficheiro de registo, a informação não é considerada parte do dicionário até que a transação a que estão associadas tenha sido cometida.

No código acima, a chamada para a CommitAsync compromete todas as operações da transação. Especificamente, anexa o compromisso com o ficheiro de registo no nó local e também envia o registo de compromisso para todas as réplicas secundárias. Uma vez que um quórum (maioria) das réplicas tenha respondido, todas as alterações de dados são consideradas permanentes e quaisquer fechaduras associadas a chaves que foram manipuladas através do objeto ITransac são libertadas para que outros fios/transações possam manipular as mesmas teclas e os seus valores.

Se o CommitAsync não for chamado (normalmente devido a uma exceção a ser lançada), então o objeto ITransac fica eliminado. Ao eliminar um objeto ITransac não comprometido, o Service Fabric anexa abortar informações para o ficheiro de registo do nó local e nada precisa de ser enviado para nenhuma das réplicas secundárias. E então, quaisquer fechaduras associadas a chaves que foram manipuladas através da transação são libertadas.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e como evitá-las
Agora que entende como as coleções confiáveis funcionam internamente, vamos dar uma olhada em alguns erros comuns deles. Consulte o código abaixo:

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

Ao trabalhar com um dicionário regular .NET, pode adicionar uma chave/valor ao dicionário e, em seguida, alterar o valor de uma propriedade (como LastLogin). No entanto, este código não funcionará corretamente com um dicionário fiável. Lembre-se da discussão anterior, a chamada para AddAsync serializa os objetos chave/valor para bytearrays e, em seguida, guarda as matrizes para um arquivo local e também envia-as para as réplicas secundárias. Se posteriormente alterar um imóvel, isso altera o valor da propriedade apenas na memória; não afeta o ficheiro local nem os dados enviados para as réplicas. Se o processo falhar, o que está na memória é jogado fora. Quando um novo processo começa ou se outra réplica se torna primária, então o antigo valor da propriedade é o que está disponível.

Não posso sublinhar o quão fácil é cometer o tipo de erro acima mostrado. E, só aprenderá sobre o erro se/quando o processo for para baixo. A maneira correta de escrever o código é simplesmente inverter as duas linhas:


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

Mais uma vez, com dicionários regulares .NET, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para procurar um valor. Se o valor existir, o desenvolvedor altera o valor de uma propriedade. No entanto, com coleções fiáveis, este código apresenta o mesmo problema que já discutido: não deve modificar um objeto depois de o **ter dado a uma recolha fiável.**

A forma correta de atualizar um valor numa coleção fiável é obter uma referência ao valor existente e considerar o objeto referido por esta referência imutável. Em seguida, crie um novo objeto que seja uma cópia exata do objeto original. Agora, você pode modificar o estado deste novo objeto e escrever o novo objeto na coleção para que seja serializado para byte arrays, anexado ao arquivo local e enviado para as réplicas. Depois de cometer a mudança, os objetos de memória, o arquivo local, e todas as réplicas têm o mesmo estado exato. Está tudo bem!

O código abaixo mostra a forma correta de atualizar um valor numa coleção fiável:

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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erro do programador
Idealmente, gostaríamos que o compilador reportasse erros quando acidentalmente produzes código que muta o estado de um objeto que deves considerar imutável. Mas o compilador C# não tem a capacidade de fazer isto. Assim, para evitar potenciais bugs programadores, recomendamos vivamente que defina os tipos que utiliza com coleções fiáveis para serem tipos imutáveis. Especificamente, isto significa que se mantém nos tipos de valor de base (tais como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan, e similares). Também pode usar string. O melhor é evitar propriedades de recolha, uma vez que serializá-las e desserializá-las pode prejudicar frequentemente o desempenho. No entanto, se quiser utilizar propriedades de recolha, recomendamos vivamente a utilização de . Biblioteca de coleções imutáveis da NET[(System.Collections.Immutable).](https://www.nuget.org/packages/System.Collections.Immutable/) Esta biblioteca está disponível para download a partir de https://nuget.org. Recomendamos também selar as suas aulas e fazer os campos de leitura apenas sempre que possível.

O tipo UserInfo abaixo demonstra como definir um tipo imutável tirando partido de recomendações acima mencionadas.

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

O tipo ItemId é também um tipo imutável, como mostrado aqui:

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

## <a name="schema-versioning-upgrades"></a>Versão schema (upgrades)
Internamente, as Coleções Fiáveis serializam os seus objetos usando . NET's DataContractSerializer. Os objetos serializados são persistidos no disco local da réplica primária e também são transmitidos às réplicas secundárias. À medida que o seu serviço amadurece, é provável que queira alterar o tipo de dados (esquema) que o seu serviço necessita. Aborde a versão dos seus dados com muito cuidado. Em primeiro lugar, deve ser sempre capaz de desserializar dados antigos. Especificamente, isto significa que o seu código de desserialização deve ser infinitamente compatível com o retrocesso: a versão 333 do seu código de serviço deve poder operar em dados colocados numa recolha fiável pela versão 1 do seu código de serviço há 5 anos.

Além disso, o código de serviço é atualizado um domínio de atualização de cada vez. Assim, durante uma atualização, tem duas versões diferentes do seu código de serviço em funcionamento simultaneamente. Deve evitar que a nova versão do seu código de serviço utilize o novo esquema, uma vez que versões antigas do seu código de serviço podem não conseguir lidar com o novo esquema. Sempre que possível, deve projetar cada versão do seu serviço para ser compatível com uma versão. Especificamente, isto significa que o V1 do seu código de serviço deve ser capaz de ignorar quaisquer elementos de esquema que não manuseie explicitamente. No entanto, deve ser capaz de guardar quaisquer dados que não conheça explicitamente e escrevê-lo de volta ao atualizar uma chave ou valor do dicionário.

> [!WARNING]
> Embora possa modificar o esquema de uma chave, deve garantir que o código de hash da sua chave e algoritmos iguais são estáveis. Se alterar a forma como qualquer um destes algoritmos funciona, não poderá voltar a procurar a chave dentro do dicionário fiável.
> As cordas .NET podem ser usadas como chave, mas use a corda em si como chave -- não use o resultado de String.GetHashCode como chave.

Em alternativa, pode realizar o que é tipicamente referido como um upgrade de dois. Com uma atualização em duas fases, atualiza o seu serviço de V1 para V2: V2 contém o código que sabe lidar com a nova alteração de esquemas mas este código não executa. Quando o código V2 lê os dados v1, opera nele e escreve dados V1. Em seguida, após a atualização estar completa em todos os domínios de upgrade, pode de alguma forma sinalizar para as instâncias V2 em execução que a atualização está completa. (Uma maneira de sinalizar isto é lançar uma atualização de configuração; é isso que faz desta uma atualização em duas fases.) Agora, os casos V2 podem ler dados V1, convertê-lo em dados V2, operá-lo e escrevê-lo como dados V2. Quando outros casos lêem dados v2, não precisam de os converter, apenas operam e escrevem dados V2.

## <a name="next-steps"></a>Passos Seguintes
Para aprender sobre a criação de contratos de dados compatíveis a prazo, consulte [Contratos de Dados Compatíveis com O Futuro](https://msdn.microsoft.com/library/ms731083.aspx)

Para aprender as melhores práticas na versão de contratos de dados, consulte Versão [do Contrato](https://msdn.microsoft.com/library/ms731138.aspx) de Dados

Para aprender a implementar contratos de dados tolerantes à versão, consulte [Callbacks de Serialização Tolerantes](https://msdn.microsoft.com/library/ms733734.aspx) à versão

Para aprender como fornecer uma estrutura de dados que pode interoperar através de várias versões, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)