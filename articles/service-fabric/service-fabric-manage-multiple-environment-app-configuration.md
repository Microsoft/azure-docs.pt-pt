---
title: Gerir aplicativos para vários ambientes
description: As aplicações azure Service Fabric podem ser executadas em clusters que variam em tamanho de uma máquina a milhares de máquinas. Em alguns casos, você vai querer configurar a sua aplicação de forma diferente para esses ambientes variados. Este artigo abrange como definir diferentes parâmetros de aplicação por ambiente.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196984"
---
# <a name="manage-applications-for-multiple-environments"></a>Gerir as aplicações para vários ambientes

Os clusters azure Service Fabric permitem criar clusters usando em qualquer lugar de uma a muitas milhares de máquinas. Na maioria dos casos, encontra-se obrigado a implementar a sua aplicação em várias configurações de cluster: o seu cluster de desenvolvimento local, um cluster de desenvolvimento partilhado e o seu cluster de produção. Todos estes aglomerados são considerados ambientes diferentes que o seu código tem de ser executado. Os binários de aplicação podem ser executados sem modificações em todo este amplo espectro, mas muitas vezes pretende configurar a aplicação de forma diferente.

Considere dois exemplos simples:
  - seu serviço escuta em uma porta definida, mas você precisa que o porto seja diferente em todos os ambientes
  - você precisa fornecer diferentes credenciais vinculativas para uma base de dados em todos os ambientes

## <a name="specifying-configuration"></a>Especificação da configuração

A configuração que fornece pode ser dividida em duas categorias:

- Configuração que se aplica à forma como os seus serviços são executados
  - Por exemplo, o número da porta para um ponto final ou o número de instâncias de um serviço
  - Esta configuração é especificada no ficheiro de formulário de aplicação ou de serviço
- Configuração que se aplica ao seu código de aplicação
  - Por exemplo, informações vinculativas para uma base de dados
  - Esta configuração pode ser fornecida através de ficheiros de configuração ou variáveis ambientais

> [!NOTE]
> Nem todos os atributos nos parâmetros de suporte de ficheiros manifesto de aplicação e serviço.
> Nesses casos, tem de confiar na substituição das cordas como parte do seu fluxo de trabalho de implantação. Em Azure DevOps pode utilizar uma extensão como Substituir Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou em Jenkins pode executar uma tarefa de script para substituir os valores.
>

## <a name="specifying-parameters-during-application-creation"></a>Especificação de parâmetros durante a criação de candidaturas

Ao criar uma aplicação nomeada no Tecido de Serviço, tem a opção de passar em parâmetros. A forma como o faz depende da forma como cria a instância de candidatura.

  - No PowerShell, o [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet toma os parâmetros de aplicação como um hashtable.
  - Usando o sfctl, o comando [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) toma parâmetros como uma corda JSON. O guião install.sh usa sfctl.
  - O Visual Studio fornece-lhe um conjunto de ficheiros parâmetros na pasta Parâmetros no projeto de aplicação. Estes ficheiros de parâmetros são utilizados na publicação do Visual Studio, utilizando os Serviços Azure DevOps ou o Azure DevOps Server. No Estúdio Visual, os ficheiros de parâmetros estão a ser transmitidos para o script Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos mostram-lhe como usar alguns dos conceitos aqui descritos:

- [Como especificar variáveis ambientais para serviços em Tecido de Serviço](service-fabric-how-to-specify-environment-variables.md)
- [Como especificar o número de porta de um serviço utilizando parâmetros em Tecido de Serviço](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Como parametrizar ficheiros de configuração](service-fabric-how-to-parameterize-configuration-files.md)

- [Referência variável ambiental](service-fabric-environment-variables-reference.md)
