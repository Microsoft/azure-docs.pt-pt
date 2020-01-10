---
title: 'Atualização de aplicação: serialização de dados'
description: Práticas recomendadas para a serialização de dados e como ela afeta a interrupção de atualizações de aplicativos.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457491"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta uma atualização de aplicativo
Em uma [atualização de aplicativo sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização por vez. Durante esse processo, alguns domínios de atualização estão na versão mais recente do seu aplicativo e alguns domínios de atualização estão na versão mais antiga do seu aplicativo. Durante a distribuição, a nova versão do seu aplicativo deve ser capaz de ler a versão antiga de seus dados, e a versão antiga do seu aplicativo deve ser capaz de ler a nova versão dos seus dados. Se o formato de dados não for compatível com versões posteriores e anteriores, a atualização poderá falhar ou pior, os dados poderão ser perdidos ou corrompidos. Este artigo discute o que constitui seu formato de dados e oferece práticas recomendadas para garantir que seus dados sejam compatíveis e com versões anteriores.

## <a name="what-makes-up-your-data-format"></a>O que compõe seu formato de dados?
No Azure Service Fabric, os dados que são persistidos e replicados vêm de suas C# classes. Para aplicativos que usam [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md), esses dados são os objetos nos dicionários e nas filas confiáveis. Para aplicativos que usam [Reliable Actors](service-fabric-reliable-actors-introduction.md), esse é o estado de backup do ator. Essas C# classes devem ser serializáveis para serem persistentes e replicadas. Portanto, o formato de dados é definido pelos campos e propriedades serializados, bem como como eles são serializados. Por exemplo, em um `IReliableDictionary<int, MyClass>` os dados são uma `int` serializada e uma `MyClass`serializada.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Alterações de código que resultam em uma alteração no formato de dados
Como o formato de dados é determinado C# pelas classes, as alterações nas classes podem causar uma alteração no formato dos dados. Deve-se ter cuidado para garantir que uma atualização sem interrupção possa lidar com a alteração do formato de dados. Exemplos que podem causar alterações no formato de dados:

* Adicionando ou removendo campos ou propriedades
* Renomeando campos ou propriedades
* Alterando os tipos de campos ou propriedades
* Alterando o nome da classe ou o namespace

### <a name="data-contract-as-the-default-serializer"></a>Contrato de dados como o serializador padrão
O serializador é geralmente responsável por ler os dados e desserializá-los na versão atual, mesmo que os dados estejam em uma versão mais antiga ou *mais recente* . O serializador padrão é o [serializador de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx), que tem regras de controle de versão bem definidas. As coleções confiáveis permitem que o serializador seja substituído, mas Reliable Actors atualmente não. O serializador de dados desempenha um papel importante na habilitação de atualizações sem interrupção. O serializador de contrato de dados é o serializador que recomendamos para aplicativos Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização sem interrupção
Durante uma atualização sem interrupção, há dois cenários principais em que o serializador pode encontrar uma versão mais antiga ou *mais recente* dos seus dados:

1. Depois que um nó é atualizado e começa a fazer backup, o novo serializador carregará os dados que persistiram no disco pela versão antiga.
2. Durante a atualização sem interrupção, o cluster conterá uma combinação das versões antiga e nova do seu código. Como as réplicas podem ser colocadas em diferentes domínios de atualização e as réplicas enviam dados entre si, a versão nova e/ou antiga de seus dados pode ser encontrada pela versão nova e/ou antiga do serializador.

> [!NOTE]
> A "nova versão" e a "versão antiga" aqui se referem à versão do seu código que está em execução. O "novo serializador" refere-se ao código do serializador que está sendo executado na nova versão do seu aplicativo. Os "novos dados" referem-se C# à classe serializada da nova versão do seu aplicativo.
> 
> 

As duas versões de código e formato de dados devem ser compatíveis com versões posteriores e anteriores. Se não forem compatíveis, a atualização sem interrupção poderá falhar ou os dados poderão ser perdidos. A atualização sem interrupção pode falhar porque o código ou o serializador pode lançar exceções ou uma falha ao encontrar a outra versão. Os dados poderão ser perdidos se, por exemplo, uma nova propriedade tiver sido adicionada, mas o serializador antigo a descartar durante a desserialização.

O contrato de dados é a solução recomendada para garantir que seus dados sejam compatíveis. Ele tem regras de controle de versão bem definidas para adicionar, remover e alterar campos. Ele também tem suporte para lidar com campos desconhecidos, conectar-se ao processo de serialização e desserialização e lidar com a herança de classe. Para obter mais informações, consulte [usando o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

