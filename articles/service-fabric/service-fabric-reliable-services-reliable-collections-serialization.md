---
title: Serialização de objeto de coleção confiável no Azure Service Fabric | Microsoft Docs
description: Serialização de objeto de coleções confiáveis do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: atsenthi
ms.openlocfilehash: d5e7dfb84f6e8a8fbd029ccc0b15c17f68216c33
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599304"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serialização de objeto de coleção confiável no Azure Service Fabric
As coleções confiáveis replicam e persistem seus itens para garantir que sejam duráveis em caso de falhas de máquina e interrupções de energia.
Para replicar e para manter itens, as coleções confiáveis são necessárias para serializá-los.

As coleções confiáveis obtêm o serializador apropriado para um determinado tipo do Gerenciador de estado confiável.
O Gerenciador de estado confiável contém serializadores internos e permite que serializadores personalizados sejam registrados para um determinado tipo.

## <a name="built-in-serializers"></a>Serializadores internos

O Gerenciador de estado confiável inclui o serializador interno para alguns tipos comuns, para que eles possam ser serializados com eficiência por padrão. Para outros tipos, o Gerenciador de estado confiável volta a usar o [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Serializadores internos são mais eficientes, pois sabem que seus tipos não podem ser alterados e não precisam incluir informações sobre o tipo, como seu nome de tipo.

O Gerenciador de estado confiável tem um serializador interno para os seguintes tipos: 
- Guid
- bool
- byte
- SByte
- byte[]
- char
- Cadeia de caracteres
- decimal
- double
- float
- int
- uint
- long
- ULONG
- baixo
- num

## <a name="custom-serialization"></a>Serialização personalizada

Serializadores personalizados são comumente usados para aumentar o desempenho ou para criptografar os dados pela conexão e no disco. Entre outros motivos, os serializadores personalizados são geralmente mais eficientes do que o serializador genérico, pois não precisam serializar informações sobre o tipo. 

[IReliableStateManager. TryAddStateSerializer\<T >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) é usado para registrar um serializador personalizado para o tipo T especificado. Esse registro deve ocorrer na construção do StatefulServiceBase para garantir que antes do início da recuperação, todas as coleções confiáveis tenham acesso ao serializador relevante para ler seus dados persistentes.

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
> Serializadores personalizados recebem precedência sobre serializadores internos. Por exemplo, quando um serializador personalizado para int é registrado, ele é usado para serializar inteiros em vez do serializador interno para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializador personalizado

Um serializador personalizado precisa implementar a [interface\<IStateSerializer T >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) .

> [!NOTE]
> IStateSerializer\<T > inclui uma sobrecarga para Write e Read que usa um T chamado valor base adicional. Essa API é para serialização diferencial. Atualmente, o recurso de serialização diferencial não é exposto. Portanto, essas duas sobrecargas não são chamadas até que a serialização diferencial seja exposta e habilitada.

Veja a seguir um exemplo de tipo personalizado chamado OrderKey que contém quatro propriedades

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

Veja a seguir um exemplo de implementação\<de IStateSerializer OrderKey >.
Observe que as sobrecargas de leitura e gravação que levam em baseValue chamam sua respectiva sobrecarga para a compatibilidade de encaminhamentos.

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

## <a name="upgradability"></a>Possibilidade
Em uma [atualização de aplicativo sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização por vez. Durante esse processo, alguns domínios de atualização estarão na versão mais recente do seu aplicativo e alguns domínios de atualização estarão na versão mais antiga do seu aplicativo. Durante a distribuição, a nova versão do seu aplicativo deve ser capaz de ler a versão antiga de seus dados, e a versão antiga do seu aplicativo deve ser capaz de ler a nova versão dos seus dados. Se o formato de dados não for compatível com versões posteriores e anteriores, a atualização poderá falhar ou pior, os dados poderão ser perdidos ou corrompidos.

Se você estiver usando o serializador interno, não precisará se preocupar com a compatibilidade.
No entanto, se você estiver usando um serializador personalizado ou o DataContractSerializer, os dados terão de ser infinitamente compatíveis com versões anteriores e posteriores.
Em outras palavras, cada versão do serializador precisa ser capaz de serializar e desserializar qualquer versão do tipo.

Os usuários do contrato de dados devem seguir as regras de controle de versão bem definidas para adicionar, remover e alterar campos. O contrato de dados também tem suporte para lidar com campos desconhecidos, conectar-se ao processo de serialização e desserialização e lidar com a herança de classe. Para obter mais informações, consulte [usando o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

Os usuários do serializador personalizado devem aderir às diretrizes do serializador que estão usando para verificar se ele é compatível com versões anteriores e posteriores.
A maneira comum de dar suporte a todas as versões é adicionar informações de tamanho no início e apenas adicionar propriedades opcionais.
Dessa forma, cada versão pode ler o máximo possível e pular a parte restante do fluxo.

## <a name="next-steps"></a>Passos Seguintes
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.
  * [Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.
  * Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).
  * Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).
  * Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).
