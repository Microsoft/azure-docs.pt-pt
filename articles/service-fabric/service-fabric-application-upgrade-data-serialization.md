---
title: 'Atualização da aplicação: serialização de dados'
description: As melhores práticas para a serialização de dados e como afeta as atualizações de aplicações rolantes.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 9474a0bf8041ac541389f782f60bf1220d690cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575640"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta uma atualização de aplicação
Numa [atualização de aplicações rolantes,](service-fabric-application-upgrade.md)a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante este processo, alguns domínios de upgrade encontram-se na versão mais recente da sua aplicação, e alguns domínios de upgrade encontram-se na versão mais antiga da sua aplicação. Durante o lançamento, a nova versão da sua aplicação deve ser capaz de ler a versão antiga dos seus dados, e a versão antiga da sua aplicação deve ser capaz de ler a nova versão dos seus dados. Se o formato de dados não for compatível para a frente e para trás, a atualização pode falhar, ou pior, os dados podem ser perdidos ou corrompidos. Este artigo discute o que constitui o seu formato de dados e oferece as melhores práticas para garantir que os seus dados são compatíveis para a frente e para trás.

## <a name="what-makes-up-your-data-format"></a>O que compõe o seu formato de dados?
No Azure Service Fabric, os dados que são persistidos e replicados provêm das suas aulas C#. Para aplicações que utilizam [Coleções Fiáveis,](service-fabric-reliable-services-reliable-collections.md)esses dados são os objetos nos dicionários e filas fiáveis. Para aplicações que usam [Reliable Actors](service-fabric-reliable-actors-introduction.md), este é o estado de apoio para o ator. Estas classes C# devem ser serializáveis para serem persistidos e replicadas. Portanto, o formato de dados é definido pelos campos e propriedades que são serializados, bem como como são serializados. Por exemplo, num `IReliableDictionary<int, MyClass>` dado é um serializado e um `int` serializado `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Alterações de código que resultem numa alteração do formato de dados
Uma vez que o formato de dados é determinado pelas classes C#, as alterações nas classes podem causar uma alteração do formato de dados. Deve-se ter cuidado para que uma atualização rolante possa lidar com a alteração do formato de dados. Exemplos que podem causar alterações no formato de dados:

* Adicionar ou remover campos ou propriedades
* Renomear campos ou propriedades
* Alterar os tipos de campos ou propriedades
* Mudar o nome de classe ou espaço de nome

### <a name="data-contract-as-the-default-serializer"></a>Contrato de Dados como serializador padrão
O serializer é geralmente responsável pela leitura dos dados e pela sua deserização na versão atual, mesmo que os dados se desreseram numa versão mais antiga ou *mais recente.* O serializer padrão é o serializer do Contrato de [Dados,](/dotnet/framework/wcf/feature-details/using-data-contracts)que tem regras de versão bem definidas. As Coleções Fiáveis permitem que o serializer seja ultrapassado, mas atualmente os Atores Fiáveis não. O serializer de dados desempenha um papel importante na ativação de upgrades de rolos. O serializer do Contrato de Dados é o serializador que recomendamos para aplicações de Tecido de Serviço.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização rolante
Durante uma atualização, existem dois cenários principais em que o serializer pode encontrar uma versão mais antiga ou *mais recente* dos seus dados:

1. Depois de um nó ser atualizado e começar a funcionar de novo, o novo serializer carregará os dados que foram persistidos para o disco pela versão antiga.
2. Durante a atualização, o cluster conterá uma mistura das versões antigas e novas do seu código. Uma vez que as réplicas podem ser colocadas em diferentes domínios de upgrade, e as réplicas enviam dados entre si, a nova e/ou antiga versão dos seus dados pode ser encontrada pela nova e/ou antiga versão do seu serializer.

> [!NOTE]
> A "nova versão" e "versão antiga" aqui referem-se à versão do seu código que está em execução. O "novo serializer" refere-se ao código serializador que está a ser executado na nova versão da sua aplicação. Os "novos dados" referem-se à classe C# serializada da nova versão da sua aplicação.
> 
> 

As duas versões de código e formato de dados devem ser compatíveis tanto para a frente como para trás. Se não forem compatíveis, a atualização de rolamento pode falhar ou os dados podem ser perdidos. A atualização de rolamento pode falhar porque o código ou serializer pode lançar exceções ou uma falha quando encontra a outra versão. Os dados podem ser perdidos se, por exemplo, uma nova propriedade for adicionada, mas o serializer antigo o descarta durante a deserialização.

O Contrato de Dados é a solução recomendada para garantir que os seus dados são compatíveis. Tem regras de versão bem definidas para adicionar, remover e mudar de campo. Também tem apoio para lidar com campos desconhecidos, ligando-se ao processo de serialização e deserialização, e lidar com a herança de classes. Para obter mais informações, consulte [o Contrato de Dados.](/dotnet/framework/wcf/feature-details/using-data-contracts)

## <a name="next-steps"></a>Passos seguintes
[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

Controle como a sua aplicação atualiza utilizando [parâmetros de atualização.](service-fabric-application-upgrade-parameters.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [Tópicos Avançados.](service-fabric-application-upgrade-advanced.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos em [Atualizações de Aplicações de Resolução de Problemas](service-fabric-application-upgrade-troubleshooting.md).
