---
title: Gerir aplicativos para vários ambientes
description: As aplicações Azure Service Fabric podem ser executadas em clusters que variam em tamanho de uma máquina a milhares de máquinas. Em alguns casos, irá querer configurar a sua aplicação de forma diferente para esses ambientes variados. Este artigo abrange como definir diferentes parâmetros de aplicação por ambiente.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787982"
---
# <a name="manage-applications-for-multiple-environments"></a>Gerir as aplicações para vários ambientes

Os clusters de tecido de serviço Azure permitem criar clusters usando qualquer lugar de uma a muitas milhares de máquinas. Na maioria dos casos, encontra-se a ter de implementar a sua aplicação em várias configurações de cluster: o seu cluster de desenvolvimento local, um cluster de desenvolvimento partilhado e o seu cluster de produção. Todos estes aglomerados são considerados ambientes diferentes em que o seu código tem de funcionar. Os binários de aplicação podem ser executados sem modificação em todo este amplo espectro, mas muitas vezes pretende configurar a aplicação de forma diferente.

Considere dois exemplos simples:
  - seu serviço ouve em uma porta definida, mas você precisa que a porta seja diferente em todos os ambientes
  - você precisa fornecer diferentes credenciais vinculativas para uma base de dados em todos os ambientes

## <a name="specifying-configuration"></a>Especificar a configuração

A configuração que fornece pode ser dividida em duas categorias:

- Configuração que se aplica à forma como os seus serviços são executados
  - Por exemplo, o número da porta para um ponto final ou o número de casos de um serviço
  - Esta configuração é especificada no ficheiro manifesto de aplicação ou serviço
- Configuração que se aplica ao seu código de aplicação
  - Por exemplo, informações vinculativas para uma base de dados
  - Esta configuração pode ser fornecida através de ficheiros de configuração ou variáveis ambientais

> [!NOTE]
> Nem todos os atributos nos parâmetros de suporte de ficheiros manifesto de aplicação e serviço.
> Nesses casos, tem de confiar na substituição das cordas como parte do seu fluxo de trabalho de implantação. Em Azure DevOps pode utilizar uma extensão como Substituir Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou em Jenkins poderá executar uma tarefa de script para substituir os valores.
>

## <a name="specifying-parameters-during-application-creation"></a>Especificar parâmetros durante a criação da aplicação

Ao criar uma aplicação nomeada em Service Fabric, tem a opção de passar em parâmetros. A forma como o fazes depende da forma como crias a aplicação.

  - No PowerShell, o [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) cmdlet toma os parâmetros de aplicação como um haxixe.
  - Utilizando sfctl, o [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) comando toma os parâmetros como uma corda JSON. O install.sh script usa sfctl.
  - O Visual Studio fornece-lhe um conjunto de ficheiros de parâmetros na pasta Parâmetros no projeto de aplicação. Estes ficheiros de parâmetros são utilizados na publicação do Visual Studio, utilizando os Serviços Azure DevOps ou o Azure DevOps Server. No Estúdio Visual, os ficheiros dos parâmetros estão a ser transmitidos para o Deploy-FabricApplication.ps1 script.

## <a name="next-steps"></a>Próximos passos
Os seguintes artigos mostram-lhe como utilizar alguns dos conceitos aqui descritos:

- [Como especificar variáveis ambientais para serviços em Tecido de Serviço](service-fabric-how-to-specify-environment-variables.md)
- [Como especificar o número de porta de um serviço usando parâmetros em Tecido de Serviço](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Como parametrizar ficheiros de configuração](service-fabric-how-to-parameterize-configuration-files.md)

- [Referência variável do ambiente](service-fabric-environment-variables-reference.md)
