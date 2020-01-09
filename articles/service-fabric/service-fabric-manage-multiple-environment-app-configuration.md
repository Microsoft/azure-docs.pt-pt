---
title: Gerenciar aplicativos para vários ambientes
description: Os aplicativos de Service Fabric do Azure podem ser executados em clusters que variam de tamanho de um computador para milhares de computadores. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferentes por ambiente.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 33dfc91381b23bf1ac33bef5274e1098df411f4a
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609847"
---
# <a name="manage-applications-for-multiple-environments"></a>Gerir as aplicações para vários ambientes

Os clusters Service Fabric do Azure permitem que você crie clusters usando qualquer lugar de um a muitos milhares de máquinas. Na maioria dos casos, você tem de implantar seu aplicativo em várias configurações de cluster: seu cluster de desenvolvimento local, um cluster de desenvolvimento compartilhado e seu cluster de produção. Todos esses clusters são considerados diferentes ambientes em que seu código precisa ser executado. Os binários de aplicativo podem ser executados sem modificação nesse amplo espectro, mas muitas vezes você deseja configurar o aplicativo de forma diferente.

Considere dois exemplos simples:
  - seu serviço escuta em uma porta definida, mas você precisa que essa porta seja diferente entre os ambientes
  - Você precisa fornecer credenciais de associação diferentes para um banco de dados entre os ambientes

## <a name="specifying-configuration"></a>Especificando a configuração

A configuração que você fornece pode ser dividida em duas categorias:

- Configuração que se aplica a como os serviços são executados
  - Por exemplo, o número da porta para um ponto de extremidade ou o número de instâncias de um serviço
  - Essa configuração é especificada no arquivo de manifesto do aplicativo ou do serviço
- Configuração que se aplica ao código do aplicativo
  - Por exemplo, informações de associação para um banco de dados
  - Essa configuração pode ser fornecida por meio de arquivos de configuração ou variáveis de ambiente

> [!NOTE]
> Nem todos os atributos no arquivo de manifesto do aplicativo e do serviço dão suporte a parâmetros.
> Nesses casos, você precisa contar com a substituição de cadeias de caracteres como parte de seu fluxo de trabalho de implantação. No Azure DevOps, você pode usar uma extensão como substituir tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou no Jenkins você pode executar uma tarefa Script para substituir os valores.
>

## <a name="specifying-parameters-during-application-creation"></a>Especificando parâmetros durante a criação do aplicativo

Ao criar instâncias de um aplicativo nomeado no Service Fabric, você tem a opção de passar parâmetros. A maneira como você faz isso depende de como você cria a instância do aplicativo.

  - No PowerShell, o cmdlet [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) usa os parâmetros do aplicativo como uma tabela de hash.
  - Usando sfctl, o comando [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) usa parâmetros como uma cadeia de caracteres JSON. O script install.sh usa sfctl.
  - O Visual Studio fornece um conjunto de arquivos de parâmetro na pasta Parameters no projeto de aplicativo. Esses arquivos de parâmetro são usados durante a publicação do Visual Studio, usando Azure DevOps Services ou Team Foundation Server. No Visual Studio, os arquivos de parâmetro estão sendo passados para o script Deploy-FabricApplication. ps1.

## <a name="next-steps"></a>Passos seguintes
Os artigos a seguir mostram como usar alguns dos conceitos descritos aqui:

- [Como especificar variáveis de ambiente para serviços no Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Como especificar o número da porta de um serviço usando parâmetros em Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Como parametrizar arquivos de configuração](service-fabric-how-to-parameterize-configuration-files.md)

- [Referência de variável de ambiente](service-fabric-environment-variables-reference.md)
