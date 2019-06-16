---
title: Gerir aplicações para vários ambientes no Azure Service Fabric | Documentos da Microsoft
description: As aplicações do Azure Service Fabric podem ser executadas em clusters desse intervalo de tamanho de um computador para milhares de máquinas. Em alguns casos, desejará configurar a sua aplicação diferentes para esses diversos ambientes. Este artigo aborda como definir os parâmetros de aplicação diferente por ambiente.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719231"
---
# <a name="manage-applications-for-multiple-environments"></a>Gerir as aplicações para vários ambientes

Clusters do Service Fabric do Azure permitem-lhe criar clusters em qualquer lugar através de um para muitos milhares máquinas. Na maioria dos casos, que se deparar ter de implementar a sua aplicação em várias configurações de cluster: cluster de desenvolvimento local, um cluster de desenvolvimento compartilhado e o cluster de produção. Todos esses agrupamentos são considerados ambientes diferentes, que tem de executar no seu código. Binários de aplicativo podem ser executado sem nenhuma modificação neste amplo espectro, mas que, muitas vezes, pretende configurar a aplicação de forma diferente.

Considere os dois exemplos simples:
  - o serviço de escuta a uma porta definida, mas terá essa porta seja diferente entre os ambientes
  - tem de fornecer credenciais de ligação diferentes para uma base de dados entre os ambientes

## <a name="specifying-configuration"></a>Especificar a configuração

A configuração que é fornecer pode ser dividida em duas categorias:

- Configuração aplica-se a forma como os serviços são executados
  - Por exemplo, o número de porta para um ponto final ou o número de instâncias de um serviço
  - Esta configuração é especificada na aplicação ou ficheiro de manifesto do serviço
- Configuração que se aplica ao código da aplicação
  - Por exemplo, as informações de enlace para uma base de dados
  - Esta configuração pode ser fornecida por meio de arquivos de configuração ou variáveis de ambiente

> [!NOTE]
> Nem todos os atributos na aplicação e serviço de parâmetros de suporte do ficheiro de manifesto.
> Nesses casos, precisa contar com substituindo cadeias de caracteres como parte do seu fluxo de trabalho de implantação. No Azure DevOps, pode utilizar uma extensão, como substituir Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou no Jenkins é possível executar uma tarefa de script para substituir os valores.
>

## <a name="specifying-parameters-during-application-creation"></a>Especificando parâmetros durante a criação da aplicação

Ao criar um instâncias da aplicação com nome no Service Fabric, tem a opção de passar parâmetros. A maneira de fazer isso depende de como criar a instância da aplicação.

  - No PowerShell, o [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet utiliza os parâmetros da aplicação como uma tabela de hash.
  - Utilizar o sfctl, o [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) comando aceita parâmetros como uma cadeia de caracteres do JSON. O script de install.sh usa sfctl.
  - O Visual Studio fornece um conjunto de ficheiros de parâmetro na pasta de parâmetros no projeto do aplicativo. Estes ficheiros de parâmetro são utilizados durante a publicação do Visual Studio, utilizando os serviços de DevOps do Azure ou o Team Foundation Server. No Visual Studio, os ficheiros de parâmetros estão sendo passados script Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Passos Seguintes
Os artigos seguintes mostram como usar alguns dos conceitos descritos aqui:

- [Como especificar variáveis de ambiente para serviços no Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Como especificar o número de porta de um serviço usando parâmetros no Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Como parametrizar os ficheiros de configuração](service-fabric-how-to-parameterize-configuration-files.md)

- [Referência de variável de ambiente](service-fabric-environment-variables-reference.md)
