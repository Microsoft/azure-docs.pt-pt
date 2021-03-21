---
title: Serialização de objetos de coleção fiável
description: Saiba mais sobre a Azure Service Fabric Reliable Collections objeto serialização, incluindo a estratégia padrão e como definir serialização personalizada.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 29bb9a2dfb028d223d63559b35735e78d7e6bcf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784364"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialização de objetos de coleção fiável no tecido de serviço Azure
A fiáveis coleções replica e persiste os seus itens para se certificar de que são duráveis através de falhas de máquinas e falhas de energia.
Tanto para replicar como para persistir itens, a Reliable Collections precisa de os serializar.

Reliable Collections's get the appropriate serializer for a given type from Reliable State Manager.
O Reliable State Manager contém serializers incorporados e permite que os serializers personalizados sejam registados para um determinado tipo.

## <a name="built-in-serializers"></a>Serializers incorporados

O Gestor de Estado fiável inclui serializador incorporado para alguns tipos comuns, para que possam ser serializados eficientemente por padrão. Para outros tipos, o Gestor de Estado fiável volta a utilizar o [DataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer).
Os serializers incorporados são mais eficientes uma vez que sabem que os seus tipos não podem mudar e não precisam de incluir informações sobre o tipo como o seu nome.

O Gestor estatal fiável tem serializador incorporado para seguintes tipos: 
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
- long
- ulong
- curto
- ushort

## <a name="custom-serialization"></a>Serialização personalizada

Os serializers personalizados são geralmente usados para aumentar o desempenho ou encriptar os dados sobre o fio e no disco. Entre outras razões, os serializers personalizados são geralmente mais eficientes do que o serializador genérico, uma vez que não precisam de serializar informações sobre o tipo. 

[IReliableStateManager.TryAddStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) é utilizado para registar um serializer personalizado para o tipo T dado. Este registo deverá ocorrer na construção da StatefulServiceBase para garantir que, antes do início da recuperação, todas as Coleções Fiáveis tenham acesso ao serializador relevante para ler os seus dados persistidos.

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
> Os serializers personalizados têm precedência sobre serializers incorporados. Por exemplo, quando um serializer personalizado para int é registado, é usado para serializar inteiros em vez do serializador incorporado para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializer personalizado

Um serializer personalizado precisa implementar a interface [IStateSerializer. \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> O IStateSerializer \<T> inclui uma sobrecarga para escrever e ler que requer um T adicional chamado valor base. Esta API é para serialização diferencial. Atualmente, a função de serialização diferencial não está exposta. Assim, estas duas sobrecargas não são chamadas até que a serialização diferencial seja exposta e ativada.

Segue-se um tipo personalizado de exemplo chamado OrderKey que contém quatro propriedades

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

Segue-se um exemplo de implementação do IStateSerializer \<OrderKey> .
Note que leia e escreva sobrecargas que tomem em baseValue, chame a sua respetiva sobrecarga para compatibilidade de avançados.

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

## <a name="upgradability"></a>Atualização
Numa [atualização de aplicações rolantes,](service-fabric-application-upgrade.md)a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante este processo, alguns domínios de upgrade estarão na versão mais recente da sua aplicação, e alguns domínios de upgrade estarão na versão mais antiga da sua aplicação. Durante o lançamento, a nova versão da sua aplicação deve ser capaz de ler a versão antiga dos seus dados, e a versão antiga da sua aplicação deve ser capaz de ler a nova versão dos seus dados. Se o formato de dados não for compatível para a frente e para trás, a atualização pode falhar, ou pior, os dados podem ser perdidos ou corrompidos.

Se estiver a usar serializador incorporado, não tem de se preocupar com a compatibilidade.
No entanto, se estiver a utilizar um serializador personalizado ou o DataContractSerializer, os dados têm de ser infinitamente compatívels para trás e para a frente.
Por outras palavras, cada versão do serializer tem de ser capaz de serializar e des-serializar qualquer versão do tipo.

Os utilizadores do Data Contract devem seguir as regras de versão bem definidas para adicionar, remover e alterar campos. O Data Contract também tem apoio para lidar com campos desconhecidos, ligando-se ao processo de serialização e deserialização, e lidar com a herança de classe. Para obter mais informações, consulte [o Contrato de Dados.](/dotnet/framework/wcf/feature-details/using-data-contracts)

Os utilizadores de serializadores personalizados devem respeitar as diretrizes do serializer que estão a usar para se certificarem de que é compatível com o retro-direito e para a frente.
A forma comum de suportar todas as versões é adicionar informações de tamanho no início e apenas adicionar propriedades opcionais.
Desta forma, cada versão pode ler o máximo que puder e saltar sobre a parte restante do fluxo.

## <a name="next-steps"></a>Passos seguintes
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  * [A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.
  * [Atualizar a sua aplicação Utilizando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.
  * Controle como a sua aplicação atualiza utilizando [parâmetros de atualização.](service-fabric-application-upgrade-parameters.md)
  * Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [Tópicos Avançados.](service-fabric-application-upgrade-advanced.md)
  * Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos em [Atualizações de Aplicações de Resolução de Problemas](service-fabric-application-upgrade-troubleshooting.md).
