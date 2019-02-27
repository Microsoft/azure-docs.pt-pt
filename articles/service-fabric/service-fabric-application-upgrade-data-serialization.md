---
title: 'Atualização da aplicação: serialização de dados | Documentos da Microsoft'
description: Melhores práticas para a serialização de dados e como ele afeta as atualizações sem interrupção de aplicações.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 43d19e5c69733689be184f06b853fa4e488dd51e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871728"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta uma atualização da aplicação
Num [aplicativo atualização sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, de um domínio de atualização de cada vez. Durante este processo, alguns domínios de atualização estão na versão mais recente da sua aplicação e alguns domínios de atualização na versão mais antiga do seu aplicativo. Durante a implementação, a nova versão da sua aplicação tem de ser capaz de ler a versão antiga dos seus dados e a versão antiga da sua aplicação tem de ser capaz de ler a nova versão dos seus dados. Se o formato de dados não é compatível com a frente e para trás, a atualização poderá falhar ou, pior ainda, os dados podem ser perdidos ou corrompidos. Este artigo aborda o que constitui o seu formato de dados e oferece práticas recomendadas para garantir que os dados estão progressivos e regressivos compatível.

## <a name="what-makes-up-your-data-format"></a>O que constitui a seu formato de dados?
No Azure Service Fabric, os dados que são mantidos e replicados provêm de seu C# classes. Para aplicações que utilizam [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), que os dados são os objetos nos dicionários fiáveis e filas. Para aplicações que utilizam [Reliable Actors](service-fabric-reliable-actors-introduction.md), ou seja o estado de segurança para o ator. Estes C# classes tem de ser serializáveis para persistência e replicação. Por conseguinte, o formato de dados é definido pelos campos e propriedades que são a serializadas, bem como a forma como eles são serializados. Por exemplo, num `IReliableDictionary<int, MyClass>` os dados são um serializada `int` e um serializada `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Alterações de código que resultam numa alteração de formato de dados
Uma vez que o formato de dados é determinado pelo C# classes, as alterações às classes podem causar uma alteração de formato de dados. Deve ter cuidado para garantir que uma atualização sem interrupção pode lidar com a alteração de formato de dados. Exemplos que podem causar alterações de formato de dados:

* Adicionar ou remover campos ou propriedades
* Mudar o nome de campos ou propriedades
* Alterar os tipos de campos ou propriedades
* Alterar o nome de classe ou espaço de nomes

### <a name="data-contract-as-the-default-serializer"></a>Contrato de dados como o serializador padrão
O serializador é geralmente responsável pela leitura de dados e anular a serialização para a versão atual, mesmo que os dados estão sendo mais antigo ou *mais recente* versão. O serializador padrão é o [serializador do contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx), que tem regras de controlo de versões bem definidos. As coleções fiáveis permitir que o serializador a ser substituído, mas Reliable Actors atualmente não. O serializador de dados desempenha um papel importante na habilitação de atualizações sem interrupção. O serializador de contrato de dados é o serializador que recomendamos para aplicações do Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização sem interrupção
Durante uma atualização sem interrupção, existem dois cenários principais em que o serializador pode ser encontrados mais antigo ou *mais recente* versão dos seus dados:

1. Depois de um nó é atualizado e inicia a cópia de segurança, o novo serializador irá carregar os dados que foram transferidos para o disco, a versão antiga.
2. Durante a atualização sem interrupção, o cluster irá conter uma combinação das versões antigas e novas do seu código. Uma vez que as réplicas podem ser colocadas em diferentes domínios de atualização e réplicas enviam dados entre si, a versão nova e/ou antiga dos seus dados pode ser encontrada pela versão nova e/ou antiga do seu serializador.

> [!NOTE]
> O "nova versão" e "versão antiga" Consulte aqui a versão do seu código que está em execução. O serializador"novo" refere-se para o código de serializador que está em execução na nova versão do seu aplicativo. Os "dados de novo" refere-se para o serializada C# classe da nova versão do seu aplicativo.
> 
> 

As duas versões do formato de código e os dados têm de ser progressivos e regressivos compatível. Se não forem compatíveis, a atualização sem interrupção pode falhar ou dados poderão perder-se. A atualização sem interrupção pode falhar porque o código ou o serializador pode lançar a exceções ou uma falha quando ele encontra a outra versão. Dados poderão perder-se se, por exemplo, foi adicionada uma nova propriedade, mas o serializador antigo elimina-o durante a desserialização.

Contrato de dados é a solução recomendada para garantir que os seus dados são compatíveis. Ele tem regras de controlo de versões bem definidos para adicionar, remover e alterar os campos. Ela também oferece suporte para lidar com campos desconhecidos, entrar no processo de serialização e desserialização e lidar com a herança de classe. Para obter mais informações, consulte [contrato de dados usando](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.

Controlar a forma como a aplicação seja atualizada com o [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [resolução de problemas de atualizações de aplicações](service-fabric-application-upgrade-troubleshooting.md).

