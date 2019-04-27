---
title: Atualização da aplicação de Service Fabric | Documentos da Microsoft
description: Este artigo fornece uma introdução ao atualizar uma aplicação do Service Fabric, incluindo escolher entre modos de atualização e executar verificações do Estado de funcionamento.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615077"
---
# <a name="service-fabric-application-upgrade"></a>Atualização de aplicação do Service Fabric
Uma aplicação do Azure Service Fabric é uma coleção de serviços. Durante uma atualização, compara o novo Service Fabric [manifesto do aplicativo](service-fabric-application-and-service-manifests.md) com a versão anterior e determina qual dos serviços nas atualizações do aplicativo requer. Service Fabric compara a versão manifestos de números no serviço com os números de versão na versão anterior. Se um serviço não foi alterada, esse serviço não está atualizado.

## <a name="rolling-upgrades-overview"></a>Sem interrupção de descrição geral de atualizações
Numa atualização sem interrupção do aplicativo, a atualização é executada em fases. Cada fase, a atualização é aplicada a um subconjunto de nós do cluster, chamado de um domínio de atualização. Como resultado, a aplicação permanece disponível durante a atualização. Durante a atualização, o cluster pode conter uma combinação das versões antigas e novas.

Por esse motivo, as duas versões tem de ser progressivos e regressivos compatível. Se não forem compatíveis, o administrador do aplicativo é responsável por uma atualização de várias fases para manter a disponibilidade de teste. Numa atualização várias fases, o primeiro passo está a atualizar para uma versão intermediária do aplicativo que é compatível com a versão anterior. O segundo passo é atualizar a versão final quebras de compatibilidade com a versão de pré-atualização, mas é compatível com a versão intermediária.

Domínios de atualização são especificados no manifesto do cluster quando configurar o cluster. Domínios de atualização não recebem as atualizações numa determinada ordem. Um domínio de atualização é uma unidade lógica de implementação para uma aplicação. Domínios de atualização permitem que os serviços para permanecer no momento da disponibilidade elevada durante uma atualização.

As atualizações sem interrupção não são possíveis, se a atualização é aplicada a todos os nós do cluster, o que é o caso quando o aplicativo tem apenas um domínio de atualização. Essa abordagem não é recomendável, uma vez que o serviço fica inativo e não está disponível no momento da atualização. Além disso, o Azure não fornece quaisquer garantias, quando configurar um cluster com apenas um domínio de atualização.

Depois de concluída a atualização, todos os serviços e replicas(instances) seriam manter-se na mesma versão-ou seja, se a atualização tiver êxito, estes serão atualizados para a nova versão; Se a atualização falhar e é revertido, eles seriam ser revertidos para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de estado de funcionamento durante as atualizações
Para uma atualização, as políticas de estado de funcionamento tem de ser definido (ou podem ser utilizados valores predefinidos). Uma atualização é chamada com êxito quando todos os domínios de atualização são atualizados dentro de esgotamentos de tempo limite especificados, e quando todos os domínios de atualização são considerados em bom estado de funcionamento.  Um domínio de atualização em bom estado significa que o domínio de atualização transmitido todas as verificações de estado de funcionamento especificadas na política de estado de funcionamento. Por exemplo, pode impor uma política de estado de funcionamento que todos os serviços dentro de uma instância de aplicação tem de ser *bom estado de funcionamento*, como o estado de funcionamento é definido ao Service Fabric.

Políticas de estado de funcionamento e verificações durante a atualização ao Service Fabric são agnósticos relativamente à aplicação e serviço. Ou seja, não existem testes específicos do serviço terminar.  Por exemplo, o seu serviço pode ter um requisito de débito, mas o Service Fabric não tem as informações para verificar a taxa de transferência. Consulte a [artigos de estado de funcionamento](service-fabric-health-introduction.md) para as verificações que são executadas. As verificações que ocorrem durante uma testes de inclusão de atualização para se o pacote de aplicação foi copiado corretamente, se a instância foi iniciada e assim por diante.

O estado de funcionamento do aplicativo é uma agregação das entidades filho do aplicativo. Em resumo, o Service Fabric avalia o estado de funcionamento do aplicativo por meio do Estado de funcionamento é comunicado no aplicativo. Ele também avalia o estado de funcionamento de todos os serviços para a aplicação desta forma. Ainda mais o Service Fabric avalia o estado de funcionamento dos serviços de aplicativo ao agregar o estado de funcionamento dos seus filhos, como a réplica de serviço. Depois da política de estado de funcionamento de aplicações está satisfeita, pode continuar a atualização. Se a política de estado de funcionamento é violada, a atualização da aplicação falha.

## <a name="upgrade-modes"></a>Modos de atualização
O modo que recomendamos para atualização da aplicação é o modo monitorizado, o que é o modo mais usado. Modo monitorizado executa a atualização num domínio de atualização e se verifica o estado de funcionamento de todos os pass (por política especificada), passa para o domínio de atualização seguinte automaticamente.  Se falharem de verificações de estado de funcionamento e/ou tempos limite é atingidos, a atualização é seja revertida para o domínio de atualização ou o modo de alterar para manual não monitorizado. Pode configurar a atualização para escolher uma desses dois modos para atualizações com falha. 

Modo manual não monitorizado tem de intervenção manual após cada atualização num domínio de atualização, para iniciar a atualização no domínio de atualização seguinte. Sem verificações de estado de funcionamento do Service Fabric são executadas. O administrador efetua as verificações de estado ou de estado de funcionamento antes de iniciar a atualização no domínio de atualização seguinte.

## <a name="upgrade-default-services"></a>Serviços de atualização predefinido
Alguns parâmetros de serviço padrão definidos no [manifesto do aplicativo](service-fabric-application-and-service-manifests.md) também podem ser atualizados como parte de uma atualização da aplicação. Apenas os parâmetros de serviço que suportam a ser alterada através de [ServiceFabricService atualização](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pode ser alterado como parte de uma atualização. O comportamento da alteração serviços padrão durante a atualização da aplicação é o seguinte:

1. Serviços de predefinição no manifesto de aplicação nova que ainda não existir no cluster são criados.
2. Serviços de predefinição que existem nos manifestos de aplicativo anteriores e novos são atualizados. Os parâmetros do serviço predefinido no manifesto do novo aplicativo substituem os parâmetros do serviço atual. A atualização da aplicação será automaticamente reversão se atualizar um serviço predefinido falhar.
3. Serviços de predefinição que não existem no manifesto de aplicação nova são eliminados se existirem no cluster. **Tenha em atenção que eliminar um serviço predefinido resulta em eliminar tudo o que o serviço do Estado e não pode ser anulada.**

Quando uma atualização da aplicação é revertida, parâmetros de serviço padrão são revertidos para os valores antigos antes de iniciar a atualização, mas serviços eliminados não podem ser recriados com o respetivo estado antigo.

> [!TIP]
> Quanto [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) definição de configuração de cluster tem de ser *verdadeiro* para ativar as regras de 2) e 3) acima (e exclusão de atualização do serviço predefinido). Esta funcionalidade é suportada a partir do Service Fabric versão 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualização dos vários aplicativos com pontos finais de HTTPS
Precisa ter cuidado para não utilizar o **mesma porta** das diferentes instâncias da mesma aplicação ao utilizar HTTP**S**. O motivo é que o Service Fabric não será capaz de atualizar o certificado para uma das instâncias da aplicação. Por exemplo, se o aplicativo 1 ou aplicação 2 ambos os quiser atualizar o respetivo certificado 1 para cert 2. Quando a atualização ocorre, Service Fabric pode ter limpar o registo de cert 1 com HTTP. sys, apesar do outro aplicativo ainda o esteja usando. Para evitar esta situação, o Service Fabric detecta que há já é outra instância do aplicativo registrada na porta com o certificado (devido ao HTTP. sys) e não a operação.

Por conseguinte, o Service Fabric não suporta a atualizar dois serviços diferentes usando **a mesma porta** em diferentes instâncias da aplicação. Em outras palavras, não é possível utilizar o mesmo certificado no serviços diferentes na mesma porta. Se precisar de ter um certificado partilhado na mesma porta, precisa garantir que os serviços são colocados em máquinas diferentes com restrições de posicionamento. Ou considere a utilização de portas dinâmicas do Service Fabric se possível para cada serviço em cada instância da aplicação. 

Se vir uma falha de atualização com https, um erro, aviso a indicar "O Windows a HTTP Server API não suporta vários certificados para aplicações que partilhem uma porta."

## <a name="application-upgrade-flowchart"></a>Fluxograma de atualização de aplicação
O fluxograma a seguir a este parágrafo pode ajudá-lo a compreender o processo de atualização de uma aplicação do Service Fabric. Em particular, o fluxo descreve como os tempos limite, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, e *UpgradeHealthCheckInterval*, ajuda controlo de quando a atualização num domínio de atualização é considerada um êxito ou a uma falha.

![O processo de atualização para uma aplicação do Service Fabric][image]

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação utilizar o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.

Controlar a forma como a aplicação seja atualizada com o [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Tornar as atualizações de aplicações compatíveis, aprendendo a usar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [resolução de problemas de atualizações de aplicações](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
