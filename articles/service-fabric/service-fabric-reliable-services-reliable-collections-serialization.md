---
title: Serialização de objeto de coleção fiável no Azure Service Fabric | Documentos da Microsoft
description: Serialização de objeto do Azure Service Fabric Reliable Collections
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: b02952cf82707966cb0f3b3473b2020e14e48931
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805854"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialização de objeto de coleção fiável no Azure Service Fabric
Coleções fiáveis replicam e manter seus itens para se certificar de que eles são duráveis em falhas de máquina e falhas de energia.
Para replicar e para manter os itens, Reliable Collections tem serializá-los.

Coleções fiáveis obtém o serializador adequado para um determinado tipo do Reliable State Manager.
Reliable State Manager contém Serializadores incorporadas e permite que os Serializadores personalizados sejam registrados para um determinado tipo.

## <a name="built-in-serializers"></a>Serializadores incorporadas

Reliable State Manager inclui serializador incorporado para alguns tipos comuns, para que possa ser serializados com eficiência por predefinição. Para outros tipos, Reliable State Manager vai para utilizar o [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Serializadores internos são mais eficientes, uma vez que já estão habituados não é possível alterar os tipos e não terão de incluir informações sobre o tipo como seu nome de tipo.

Reliable State Manager tem serializador incorporado para os seguintes tipos: 
- Guid
- booleano
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
- longa
- ulong
- curto
- ushort

## <a name="custom-serialization"></a>Serialização personalizada

Serializadores personalizados são frequentemente utilizadas para melhorar o desempenho ou para encriptar os dados durante a transmissão e no disco. Entre outras razões, Serializadores personalizados são geralmente mais eficientes do que o serializador genérico, uma vez que não precisam de informações sobre o tipo de serialização. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) é utilizada para registar um serializador personalizado para o determinado tipo T. Esse Registro deve acontecer na construção do StatefulServiceBase para se certificar de que, antes de inicia a recuperação, todas as coleções fiáveis têm acesso para o serializador relevante para ler os dados persistentes.

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
> Serializadores personalizados são dada precedência sobre Serializadores incorporadas. Por exemplo, quando é registado um serializador personalizado para int, é utilizado para serializar os números inteiros em vez do serializador incorporado para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializador personalizado

Um serializador personalizado precisa de implementar o [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) interface.

> [!NOTE]
> IStateSerializer<T> inclui uma sobrecarga para escrita e leitura que usa um T adicional chamado valor base. Esta API está para serialização diferencial. Atualmente não é exposta a funcionalidade de serialização diferencial. Por conseguinte, estas duas sobrecargas não serem chamadas até que a serialização diferencial está exposta e ativada.

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

Segue-se um exemplo de implementação de IStateSerializer<OrderKey>.
Tenha em atenção que Leem e escrevem sobrecargas que utilizam no baseValue, chamar seus respectiva sobrecarga para reencaminhamentos de compatibilidade.

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

## <a name="upgradability"></a>Upgradability
Num [aplicativo atualização sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, de um domínio de atualização de cada vez. Durante este processo, alguns domínios de atualização estará na versão mais recente da sua aplicação e alguns domínios de atualização estará na versão mais antiga do seu aplicativo. Durante a implementação, a nova versão da sua aplicação tem de ser capaz de ler a versão antiga dos seus dados e a versão antiga da sua aplicação tem de ser capaz de ler a nova versão dos seus dados. Se o formato de dados não é compatível com a frente e para trás, a atualização poderá falhar ou, pior ainda, os dados podem ser perdidos ou corrompidos.

Se estiver a utilizar o serializador interno, não precisa se preocupar sobre a compatibilidade.
No entanto, se estiver a utilizar um serializador personalizado ou o DataContractSerializer, os dados têm infinitamente ser compatível com versões anteriores e para frente.
Em outras palavras, cada versão do serializador precisa ser capaz de serializar e anular a serialização de qualquer versão do tipo.

Os utilizadores de contrato de dados devem seguir as regras de controlo de versões bem definidos para adicionar, remover e alterar os campos. Contrato de dados também tem suporte para lidar com campos desconhecidos, entrar no processo de serialização e desserialização e lidar com a herança de classe. Para obter mais informações, consulte [contrato de dados usando](https://msdn.microsoft.com/library/ms733127.aspx).

Os utilizadores de serializador personalizado devem seguir as diretrizes de serializador estão a utilizar para garantir que ele é com versões anteriores e reencaminha compatível.
Uma forma comum de oferecer suporte a todas as versões é adicionar informações de tamanho no início e apenas a adição de propriedades opcionais.
Desta forma cada versão pode ler como muito poder e a parte restante da transmissão em fluxo, conseguem pulá.

## <a name="next-steps"></a>Passos Seguintes
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.
  * [Atualizar a sua aplicação utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.
  * Controlar a forma como a aplicação seja atualizada com o [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).
  * Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).
  * Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [resolução de problemas de atualizações de aplicações](service-fabric-application-upgrade-troubleshooting.md).
