---
title: Serialização de objetos de coleção confiável
description: Saiba mais sobre a serialização de objetos de objetos de tecido de serviço Azure, incluindo a estratégia padrão e como definir a serialização personalizada.
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639552"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialização de objetos de coleção confiável em tecido de serviço Azure
A reprodução e persistência das Coleções Fiáveis e os seus itens para garantir que são duráveis através de falhas de máquinas e falhas de energia.
Tanto para replicar como para persistir itens, a Necessidade de Coleções Fiáveis os serializar.

A Reliable Collections obtém o serializador apropriado para um determinado tipo de Gestor de Estado Fiável.
O Gestor de Estado fiável contém serializadores incorporados e permite que os serializadores personalizados sejam registados para um determinado tipo.

## <a name="built-in-serializers"></a>Serializers embutidos

O Gestor de Estado fiável inclui serializador incorporado para alguns tipos comuns, para que possam ser serializados de forma eficiente por padrão. Para outros tipos, o Gestor de Estado Fiável volta a utilizar o [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Os serializadores incorporados são mais eficientes, uma vez que sabem que os seus tipos não podem mudar e não precisam de incluir informações sobre o tipo como o seu nome tipo.

O Gestor de Estado fiável tem serializador incorporado para seguintes tipos: 
- GUID
- bool
- byte
- sbyte
- byte[]
- char
- string
- decimal
- double
- float
- int
- uint
- longo
- ulongo
- curto
- ucurto

## <a name="custom-serialization"></a>Serialização Personalizada

Os serializadores personalizados são comumente usados para aumentar o desempenho ou para encriptar os dados sobre o fio e sobre o disco. Entre outras razões, os serializadores personalizados são geralmente mais eficientes do que os serializadores genéricos, uma vez que não precisam de serializar informações sobre o tipo. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) é utilizado para registar um serializador personalizado para o dado tipo T. Este registo deve ocorrer na construção da StatefulServiceBase para garantir que, antes do início da recuperação, todas as Coleções Fiáveis tenham acesso ao serializador relevante para ler os seus dados persistentes.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Os serializadores personalizados têm precedência sobre os serializadores embutidos. Por exemplo, quando um serializador personalizado para int é registado, é usado para serializar inteiros em vez do serializador incorporado para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializador personalizado

Um serializador personalizado precisa implementar a interface [iStateSerializer\<T>.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> IStateSerializer\<T> inclui uma sobrecarga para Write and Read que leva um T adicional chamado valor base. Esta API é para serialização diferencial. Atualmente, a funcionalidade de serialização diferencial não está exposta. Assim, estas duas sobrecargas não são chamadas até que a serialização diferencial seja exposta e ativada.

Segue-se um exemplo de tipo personalizado chamado OrderKey que contém quatro propriedades

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Segue-se um exemplo de\<implementação do IStateSerializer OrderKey>.
Note que Ler e Escrever sobrecargas que tomam baseValue, ligue para a respetiva sobrecarga para a compatibilidade dos avançados.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Agradabilidade
Numa [atualização](service-fabric-application-upgrade.md)de aplicações rolantes, a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante este processo, alguns domínios de upgrade estarão na versão mais recente da sua aplicação, e alguns domínios de upgrade estarão na versão mais antiga da sua aplicação. Durante o lançamento, a nova versão da sua aplicação deve poder ler a versão antiga dos seus dados, e a versão antiga da sua aplicação deve poder ler a nova versão dos seus dados. Se o formato de dados não for compatível para a frente e para trás, a atualização pode falhar, ou pior, os dados podem ser perdidos ou corrompidos.

Se estiver a usar serializador incorporado, não tem de se preocupar com compatibilidade.
No entanto, se estiver a utilizar um serializador personalizado ou o DataContractSerializer, os dados têm de ser infinitamente compatíveis para trás e para a frente.
Por outras palavras, cada versão do serializador tem de ser capaz de serializar e desserializar qualquer versão do tipo.

Os utilizadores do Data Contract devem seguir as regras de versão bem definidas para adicionar, remover e alterar campos. O Data Contract também tem apoio para lidar com campos desconhecidos, ligar-se ao processo de serialização e desserialização, e lidar com a herança de classe. Para mais informações, consulte [a Utilização](https://msdn.microsoft.com/library/ms733127.aspx)do Contrato de Dados .

Os utilizadores de serializadorpersonalizadodevem aderir às diretrizes do serializador que estão a utilizar para se certificarem de que é compatível para trás e para a frente.
A forma comum de suportar todas as versões é adicionar informações de tamanho no início e apenas adicionar propriedades opcionais.
Desta forma, cada versão pode ler o máximo que puder e saltar sobre a parte restante do fluxo.

## <a name="next-steps"></a>Passos seguintes
  * [Serialização e upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Atualizar a sua aplicação Utilizando o Estúdio Visual](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações utilizando o Visual Studio.
  * [Atualizar a sua aplicação Utilizando powershell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.
  * Controle a forma como a sua aplicação melhora utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).
  * Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação referindo-se a [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).
  * Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de aplicações de [resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)
