---
title: "Atualização da aplicação de Service Fabric | Microsoft Docs"
description: "Este artigo fornece uma introdução ao atualizar uma aplicação de Service Fabric, incluindo escolher entre modos de atualização e executar verificações do Estado de funcionamento."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 765931d8a888432e0cc77ff86d597b6e2a029a2a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-application-upgrade"></a>Atualização de aplicação do Service Fabric
Uma aplicação de Service Fabric do Azure é uma coleção de serviços. Durante uma atualização, o Service Fabric compara o novo [manifesto da aplicação](service-fabric-application-and-service-manifests.md) com a versão anterior e determina qual serviços nas atualizações de requerem a aplicação. Service Fabric compara a versão números no serviço de manifestos com os números de versão na versão anterior. Se não tiver sido alterado um serviço, esse serviço não está atualizado.

## <a name="rolling-upgrades-overview"></a>A descrição geral de atualizações
Numa atualização aplicação graduais, a atualização é executada em fases. Em cada fase, a atualização é aplicada a um subconjunto de nós no cluster, chamado um domínio de atualização. Como resultado, a aplicação permanece disponível durante a atualização. Durante a atualização, o cluster pode conter uma combinação das versões antigas e novas.

Por esse motivo, as duas versões tem de ser com versões anteriores e reencaminhar compatível. Se não forem compatíveis, o administrador da aplicação é responsável por uma atualização de fase de vários para manter a disponibilidade de teste. Numa atualização vários fase, o primeiro passo é atualizar para uma versão intermédia da aplicação que é compatível com a versão anterior. O segundo passo é para atualizar a versão final que interrompe a compatibilidade com a versão de pré-atualização, mas é compatível com a versão intermédia.

Domínios de atualização são especificados no manifesto do cluster quando configurar o cluster. Domínios de atualização não recebem atualizações por uma ordem específica. Um domínio de atualização é uma unidade lógica de implementação de uma aplicação. Domínios de atualização permitir que os serviços permaneça na disponibilidade elevada durante uma atualização.

Atualizações a anular não são possíveis se a atualização é aplicada a todos os nós do cluster, o que é o caso quando a aplicação tem apenas um domínio de atualização. Esta abordagem não é recomendada, uma vez que o serviço de fica inativo e não está disponível no momento da atualização. Além disso, o Azure não fornece quaisquer garantias quando configurar um cluster com o domínio de atualização apenas um.

Após a conclusão da atualização, todos os serviços e replicas(instances) seriam permaneça a mesma versão-ou seja, se a atualização tiver êxito, serão atualizados para a nova versão; Se a atualização falhar e é revertido, estes seriam ser revertidos para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de estado de funcionamento durante as atualizações
Para uma atualização, as políticas de estado de funcionamento têm de ser definido (ou podem ser utilizados valores predefinidos). Uma atualização é denominado com êxito quando todos os domínios de atualização são atualizados dentro os tempos limite especificados e quando todos os domínios de atualização são considerados bom estado de funcionamento padrão.  Um domínio de atualização em bom estado significa que o domínio de atualização transmitido todas as verificações de estado de funcionamento especificadas na política de estado de funcionamento. Por exemplo, uma política de estado de funcionamento pode obrigar que todos os serviços dentro de uma instância de aplicação tem de ser *bom*, como o estado de funcionamento é definido ao Service Fabric.

Políticas de estado de funcionamento e verificações durante a atualização do Service Fabric desconhecem serviço e de aplicações. Ou seja, são efetuados sem testes específicos do serviço.  Por exemplo, o serviço poderá ter um requisito de débito, mas os recursos de infraestrutura de serviço não tem as informações para verificar o débito. Consulte o [artigos de estado de funcionamento](service-fabric-health-introduction.md) para as verificações que são executadas. As verificações que ocorrem durante um testes de atualização incluir para se o pacote de aplicação foi corretamente copiado, se a instância foi iniciada e assim sucessivamente.

O estado de funcionamento de aplicações é uma agregação das entidades de subordinados da aplicação. Resumindo, o Service Fabric avalia o estado de funcionamento da aplicação através do Estado de funcionamento que é reportado na aplicação. Também avalia o estado de funcionamento de todos os serviços para a aplicação desta forma. Mais recursos de infraestrutura de serviço avalia o estado de funcionamento dos serviços de aplicação por agregar o estado de funcionamento dos respetivos subordinados, tais como a réplica de serviço. Depois da política de estado de funcionamento de aplicações é cumprida, pode continuar a atualização. Se a política de estado de funcionamento é violada, a atualização da aplicação falha.

## <a name="upgrade-modes"></a>Modos de atualização
O modo que recomendamos para atualização da aplicação é o monitorizados, que é o modo comummente utilizado. Modo monitorizado efetua a atualização no domínio de uma atualização e se verifica o estado de funcionamento de todos os passagem (por política especificada), passa para o domínio de atualização seguinte automaticamente.  Se falharem de verificações de estado de funcionamento e/ou tempos limite é atingidos, a atualização é colocada revertida para o domínio de atualização ou alterar o modo para manual não monitorizado. Pode configurar a atualização para escolher uma desses dois modos de atualizações falhou. 

Modo manual não monitorizado tem de intervenção manual após cada atualização num domínio de atualização, para iniciar a atualização no domínio de atualização seguinte. São efetuadas verificações de estado de funcionamento não Service Fabric. O administrador executa as verificações de estado ou de estado de funcionamento antes de iniciar a atualização no domínio de atualização seguinte.

## <a name="upgrade-default-services"></a>Serviços de atualização predefinido
Alguns parâmetros predefinidos de serviço definidos no [manifesto da aplicação](service-fabric-application-and-service-manifests.md) também possa ser atualizado como parte de uma atualização da aplicação. Apenas os parâmetros de serviço que suportam a ser alteradas através do [atualização ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pode ser alterado como parte de uma atualização. O comportamento da alteração serviços predefinida durante a atualização da aplicação é o seguinte:

1. Serviços de predefinido no manifesto da aplicação nova, que já não existe no cluster são criados.
2. Serviços de predefinido que existe nos manifestos de aplicação anteriores e nova são atualizados. Os parâmetros do serviço predefinido no manifesto da aplicação nova substituem os parâmetros do serviço existente. A atualização da aplicação será automaticamente reversão se atualizar um serviço predefinido falhar.
3. Serviços de predefinido que não existem no manifesto da aplicação nova são eliminados caso existam no cluster. **Tenha em atenção que eliminar um serviço predefinido resultará na eliminação tudo o que o serviço do Estado e não pode ser anulada.**

Quando uma atualização da aplicação é revertida, os parâmetros de serviço predefinidos são revertidos para os respetivos valores antigos antes de iniciar a atualização, mas eliminados serviços não podem ser recriados com o respetivo estado antigo.

> [!TIP]
> O [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) definição de configuração do cluster tem de ser *verdadeiro* para ativar as regras de 2) e 3) acima (atualização de serviço predefinido e eliminação). Esta funcionalidade é suportada a partir do Service Fabric versão 5.5.

## <a name="application-upgrade-flowchart"></a>Fluxograma de atualização de aplicação
O fluxograma a seguir esta parágrafo pode ajudá-lo a compreender o processo de atualização de uma aplicação de Service Fabric. Em particular, descreve o fluxo de como os tempos limite, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, e *UpgradeHealthCheckInterval*, ajudar controlo quando a atualização no domínio de uma atualização é considerada um êxito ou a uma falha.

![O processo de atualização para uma aplicação de recursos de infraestrutura de serviço][image]

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Faça as atualizações de aplicações compatíveis aprender a utilizar [dados serialização](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [resolução de problemas de atualizações de aplicação](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
