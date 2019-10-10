---
title: Atualização do aplicativo Service Fabric | Microsoft Docs
description: Este artigo fornece uma introdução à atualização de um aplicativo Service Fabric, incluindo a escolha de modos de atualização e a execução de verificações de integridade.
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
ms.author: atsenthi
ms.openlocfilehash: 3c50ee149f5bcdda6cbb697830945cdc7f7a15f4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167272"
---
# <a name="service-fabric-application-upgrade"></a>Atualização de aplicação do Service Fabric
Um aplicativo Service Fabric do Azure é uma coleção de serviços. Durante uma atualização, o Service Fabric compara o novo [manifesto do aplicativo](service-fabric-application-and-service-manifests.md) com a versão anterior e determina quais serviços no aplicativo exigem atualizações. Service Fabric compara os números de versão nos manifestos do serviço com os números de versão na versão anterior. Se um serviço não tiver sido alterado, esse serviço não será atualizado.

## <a name="rolling-upgrades-overview"></a>Visão geral das atualizações sem interrupção
Em uma atualização de aplicativo sem interrupção, a atualização é executada em estágios. Em cada estágio, a atualização é aplicada a um subconjunto de nós no cluster, chamado de domínio de atualização. Como resultado, o aplicativo permanecerá disponível durante a atualização. Durante a atualização, o cluster pode conter uma combinação das versões antiga e nova.

Por esse motivo, as duas versões devem ser compatíveis com versões posteriores e retroativas. Se não forem compatíveis, o administrador do aplicativo será responsável por preparar uma atualização de várias fases para manter a disponibilidade. Em uma atualização de várias fases, a primeira etapa é atualizar para uma versão intermediária do aplicativo compatível com a versão anterior. A segunda etapa é atualizar a versão final que interrompe a compatibilidade com a versão anterior à atualização, mas é compatível com a versão intermediária.

Os domínios de atualização são especificados no manifesto do cluster quando você configura o cluster. Os domínios de atualização não recebem atualizações em uma ordem específica. Um domínio de atualização é uma unidade lógica de implantação para um aplicativo. Os domínios de atualização permitem que os serviços permaneçam em alta disponibilidade durante uma atualização.

Atualizações sem interrupção serão possíveis se a atualização for aplicada a todos os nós no cluster, que é o caso quando o aplicativo tem apenas um domínio de atualização. Essa abordagem não é recomendada, pois o serviço fica inativo e não está disponível no momento da atualização. Além disso, o Azure não fornece nenhuma garantia quando um cluster é configurado com apenas um domínio de atualização.

Após a conclusão da atualização, todos os serviços e réplicas (instâncias) permanecerão na mesma versão-i. e. se a atualização for bem sucedido, ela será atualizada para a nova versão; se a atualização falhar e for revertida, ela será revertida para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de integridade durante atualizações
Para uma atualização, as políticas de integridade precisam ser definidas (ou valores padrão podem ser usados). Uma atualização é concluída com êxito quando todos os domínios de atualização são atualizados dentro dos tempos limite especificados e quando todos os domínios de atualização são considerados íntegros.  Um domínio de atualização íntegro significa que o domínio de atualização passou todas as verificações de integridade especificadas na política de integridade. Por exemplo, uma política de integridade pode exigir que todos os serviços em uma instância de aplicativo devem estar *íntegros*, pois a integridade é definida por Service Fabric.

As políticas de integridade e verificações durante a atualização por Service Fabric são independentes de serviço e aplicativo. Ou seja, nenhum teste específico de serviço é feito.  Por exemplo, o serviço pode ter um requisito de taxa de transferência, mas Service Fabric não tem as informações para verificar a taxa de transferência. Consulte os [artigos de integridade](service-fabric-health-introduction.md) para as verificações que são executadas. As verificações que ocorrem durante uma atualização incluem testes para se o pacote de aplicativos foi copiado corretamente, se a instância foi iniciada e assim por diante.

A integridade do aplicativo é uma agregação das entidades filho do aplicativo. Em suma, Service Fabric avalia a integridade do aplicativo por meio da integridade relatada no aplicativo. Ele também avalia a integridade de todos os serviços do aplicativo dessa maneira. Service Fabric avalia ainda mais a integridade dos serviços de aplicativo agregando a integridade de seus filhos, como a réplica de serviço. Depois que a política de integridade do aplicativo for satisfeita, a atualização poderá continuar. Se a política de integridade for violada, a atualização do aplicativo falhará.

## <a name="upgrade-modes"></a>Modos de atualização
O modo que recomendamos para a atualização do aplicativo é o modo monitorado, que é o modo usado normalmente. O modo monitorado executa a atualização em um domínio de atualização e, se todas as verificações de integridade forem aprovadas (de acordo com a política especificada), o próximo domínio de atualização será movido automaticamente.  Se as verificações de integridade falharem e/ou o tempo limite forem atingidos, a atualização será revertida para o domínio de atualização ou o modo será alterado para manual não monitorado. Você pode configurar a atualização para escolher um desses dois modos para atualizações com falha. 

O modo manual não monitorado precisa de intervenção manual após cada atualização em um domínio de atualização para iniciar a atualização no próximo domínio de atualização. Nenhuma verificação de integridade Service Fabric é executada. O administrador executa as verificações de integridade ou de status antes de iniciar a atualização no próximo domínio de atualização.

## <a name="upgrade-default-services"></a>Atualizar serviços padrão
Alguns parâmetros de serviço padrão definidos no [manifesto do aplicativo](service-fabric-application-and-service-manifests.md) também podem ser atualizados como parte de uma atualização de aplicativo. Somente os parâmetros de serviço que dão suporte à alteração por meio [de Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) podem ser alterados como parte de uma atualização. O comportamento da alteração de serviços padrão durante a atualização do aplicativo é o seguinte:

1. Os serviços padrão no novo manifesto do aplicativo que ainda não existem no cluster são criados.
2. Os serviços padrão que existem nos manifestos de aplicativo anteriores e novos são atualizados. Os parâmetros do serviço padrão no novo manifesto do aplicativo substituem os parâmetros do serviço existente. A atualização do aplicativo será revertida automaticamente se a atualização de um serviço padrão falhar.
3. Os serviços padrão que não existem no novo manifesto do aplicativo serão excluídos se existirem no cluster. **Observe que a exclusão de um serviço padrão resultará na exclusão de todo o estado do serviço e não poderá ser desfeita.**

Quando uma atualização de aplicativo é revertida, os parâmetros de serviço padrão são revertidos para seus valores antigos antes do início da atualização, mas os serviços excluídos não podem ser recriados com seu estado antigo.

> [!TIP]
> A configuração de cluster [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) deve ser *verdadeira* para habilitar as regras 2) e 3) acima (atualização e exclusão de serviço padrão). Esse recurso tem suporte a partir do Service Fabric versão 5,5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualizando vários aplicativos com pontos de extremidade HTTPS
Você precisa ter cuidado para não usar a **mesma porta** para instâncias diferentes do mesmo aplicativo ao usar http**S**. O motivo é que Service Fabric não poderá atualizar o certificado para uma das instâncias do aplicativo. Por exemplo, se o aplicativo 1 ou o aplicativo 2 quiser atualizar seu certificado 1 para CERT 2. Quando ocorre a atualização, Service Fabric pode ter limpado o registro do certificado 1 com o http. sys, mesmo que o outro aplicativo ainda o esteja usando. Para evitar isso, Service Fabric detecta que já existe outra instância do aplicativo registrada na porta com o certificado (devido a http. sys) e falha na operação.

Portanto Service Fabric não dá suporte à atualização de dois serviços diferentes usando **a mesma porta** em diferentes instâncias de aplicativo. Em outras palavras, você não pode usar o mesmo certificado em serviços diferentes na mesma porta. Se precisar ter um certificado compartilhado na mesma porta, você precisará garantir que os serviços sejam colocados em computadores diferentes com restrições de posicionamento. Ou considere usar Service Fabric portas dinâmicas, se possível, para cada serviço em cada instância do aplicativo. 

Se você vir uma falha de atualização com HTTPS, um aviso de erro dizendo "a API do servidor HTTP do Windows não dá suporte a vários certificados para aplicativos que compartilham uma porta".

## <a name="application-upgrade-flowchart"></a>Fluxograma de atualização de aplicativo
O fluxograma após este parágrafo pode ajudá-lo a entender o processo de atualização de um aplicativo Service Fabric. Em particular, o fluxo descreve como os tempos limite, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout*e *UpgradeHealthCheckInterval*, ajudam a controlar quando a atualização em um domínio de atualização é considerada um êxito ou uma falha.

![O processo de atualização para um aplicativo Service Fabric][image]

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
