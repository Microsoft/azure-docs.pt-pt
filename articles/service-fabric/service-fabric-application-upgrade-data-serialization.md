---
title: 'Atualização da aplicação: serialização de dados'
description: As melhores práticas para a serialização de dados e como isso afeta as atualizações de aplicações rolantes.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457491"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta uma atualização de aplicação
Numa [atualização](service-fabric-application-upgrade.md)de aplicações rolantes, a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante este processo, alguns domínios de upgrade estão na versão mais recente da sua aplicação, e alguns domínios de upgrade estão na versão mais antiga da sua aplicação. Durante o lançamento, a nova versão da sua aplicação deve poder ler a versão antiga dos seus dados, e a versão antiga da sua aplicação deve poder ler a nova versão dos seus dados. Se o formato de dados não for compatível para a frente e para trás, a atualização pode falhar, ou pior, os dados podem ser perdidos ou corrompidos. Este artigo discute o que constitui o seu formato de dados e oferece as melhores práticas para garantir que os seus dados são compatíveis para a frente e para trás.

## <a name="what-makes-up-your-data-format"></a>O que compõe o seu formato de dados?
No Tecido de Serviço Azure, os dados que são persistidos e replicados provêm das suas aulas de C#. Para aplicações que utilizam [Coleções Fiáveis,](service-fabric-reliable-services-reliable-collections.md)esses dados são os objetos nos dicionários e filas fiáveis. Para aplicações que usam [Atores Fiáveis,](service-fabric-reliable-actors-introduction.md)este é o estado de apoio para o ator. Estas aulas de C# devem ser ser ilizáveis para serem persistidas e replicadas. Portanto, o formato de dados é definido pelos campos e propriedades que são serializados, bem como como são serializados. Por exemplo, `IReliableDictionary<int, MyClass>` num dos dados `int` está um `MyClass`serializado e um serializado .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Alterações de código que resultam numa alteração do formato de dados
Uma vez que o formato de dados é determinado pelas classes C#, as alterações nas classes podem provocar uma alteração no formato de dados. Deve-se ter cuidado para garantir que uma atualização de rolamento possa lidar com a alteração do formato de dados. Exemplos que podem causar alterações no formato de dados:

* Adicionar ou remover campos ou propriedades
* Campos ou propriedades de renomeação
* Alteração dos tipos de campos ou propriedades
* Alterar o nome da classe ou espaço de nome

### <a name="data-contract-as-the-default-serializer"></a>Contrato de Dados como serializador padrão
O serializador é geralmente responsável por ler os dados e desserializá-lo na versão atual, mesmo que os dados se traduzam numa versão mais antiga ou *mais recente.* O serializador padrão é o [serializador data Contract,](https://msdn.microsoft.com/library/ms733127.aspx)que tem regras de versão bem definidas. Coleções fiáveis permitem que o serializador seja ultrapassado, mas os Atores Fiáveis não o fazem atualmente. O serializador de dados desempenha um papel importante na ativação de upgrades. O serializador data Contract é o serializador que recomendamos para aplicações de Tecido de Serviço.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização em rolo
Durante uma atualização em andamento, existem dois cenários principais em que o serializador pode encontrar uma versão mais antiga ou *mais recente* dos seus dados:

1. Depois de um nó ser atualizado e recomeça, o novo serializador carregará os dados que foram persistidos em disco pela versão antiga.
2. Durante a atualização de rolamento, o cluster conterá uma mistura das versões antigas e novas do seu código. Uma vez que as réplicas podem ser colocadas em diferentes domínios de upgrade, e as réplicas enviam dados uns para os outros, a nova e/ou antiga versão dos seus dados pode ser encontrada pela nova e/ou antiga versão do seu serializador.

> [!NOTE]
> A "nova versão" e a "versão antiga" aqui referem-se à versão do seu código que está em execução. O "novo serializador" refere-se ao código serializador que está a ser executado na nova versão da sua aplicação. Os "novos dados" referem-se à classe C# serializada a partir da nova versão da sua aplicação.
> 
> 

As duas versões de código e formato de dados devem ser compatíveis para a frente e para trás. Se não forem compatíveis, a atualização de rolamento pode falhar ou os dados podem ser perdidos. A atualização de rolamento pode falhar porque o código ou serializador pode lançar exceções ou uma falha quando encontrar a outra versão. Os dados podem ser perdidos se, por exemplo, uma nova propriedade for adicionada, mas o velho serializador descarta-o durante a desserialização.

Data Contract é a solução recomendada para garantir que os seus dados são compatíveis. Tem regras de versão bem definidas para adicionar, remover e mudar de campo. Tem também apoio para lidar com campos desconhecidos, ligar-se ao processo de serialização e desserialização e lidar com a herança de classe. Para mais informações, consulte [a Utilização](https://msdn.microsoft.com/library/ms733127.aspx)do Contrato de Dados .

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação Utilizando o Estúdio Visual](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando powershell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.

Controle a forma como a sua aplicação melhora utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação referindo-se a [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de aplicações de [resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)

