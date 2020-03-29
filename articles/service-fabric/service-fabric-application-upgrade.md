---
title: Upgrade de aplicação de tecido de serviço
description: Este artigo fornece uma introdução para atualizar uma aplicação service fabric, incluindo escolher modos de upgrade e realizar verificações de saúde.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259048"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade de aplicação de tecido de serviço
Uma aplicação Azure Service Fabric é uma coleção de serviços. Durante uma atualização, o Service Fabric compara o manifesto de [aplicação](service-fabric-application-and-service-manifests.md) com a versão anterior e determina quais os serviços da aplicação que requerem atualizações. O Service Fabric compara os números da versão no serviço manifestos com os números da versão na versão anterior. Se um serviço não tiver mudado, esse serviço não é atualizado.

## <a name="rolling-upgrades-overview"></a>Rolling upgrades visão geral
Numa atualização de aplicações rolantes, a atualização é realizada por etapas. Em cada fase, a atualização é aplicada a um subconjunto de nós no cluster, chamado domínio de atualização. Como resultado, a aplicação permanece disponível durante toda a atualização. Durante a atualização, o cluster pode conter uma mistura das versões antigas e novas.

Por essa razão, as duas versões devem ser compatíveis para a frente e para trás. Se não forem compatíveis, o administrador de aplicação é responsável pela realização de uma atualização de várias fases para manter a disponibilidade. Numa atualização em várias fases, o primeiro passo é atualizar para uma versão intermédia da aplicação compatível com a versão anterior. O segundo passo é atualizar a versão final que quebra a compatibilidade com a versão pré-actualização, mas é compatível com a versão intermédia.

Os domínios de atualização são especificados no manifesto do cluster quando configurar o cluster. Os domínios de atualização não recebem atualizações numa determinada ordem. Um domínio de atualização é uma unidade lógica de implantação para uma aplicação. Os domínios de atualização permitem que os serviços permaneçam em alta disponibilidade durante uma atualização.

As atualizações não rolantes são possíveis se a atualização for aplicada a todos os nós do cluster, o que acontece quando a aplicação tem apenas um domínio de atualização. Esta abordagem não é recomendada, uma vez que o serviço desce e não está disponível no momento da atualização. Além disso, o Azure não oferece quaisquer garantias quando um cluster é configurado com apenas um domínio de atualização.

Após a atualização concluída, todos os serviços e réplicas (instâncias) permanecerão na mesma versão- ou seja, se a atualização for bem sucedida, serão atualizadas para a nova versão; se a atualização falhar e for relançada, serão relançadas para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de saúde durante upgrades
Para uma atualização, as políticas de saúde têm de ser definidas (ou podem ser utilizados valores predefinidos). Uma atualização é denominada bem sucedida quando todos os domínios de atualização são atualizados dentro dos intervalos de tempo especificados, e quando todos os domínios de atualização são considerados saudáveis.  Um domínio de atualização saudável significa que o domínio da atualização passou todos os controlos de saúde especificados na política de saúde. Por exemplo, uma política de saúde pode ordenar que todos os serviços dentro de uma instância de candidatura devem ser *saudáveis*, uma vez que a saúde é definida pelo Service Fabric.

As políticas e verificações de saúde durante a atualização pelo Service Fabric são agnósticos de serviço e aplicação. Ou seja, não são feitos testes específicos de serviço.  Por exemplo, o seu serviço pode ter um requisito de entrada, mas o Service Fabric não tem a informação para verificar a entrada. Consulte os artigos de saúde para os [controlos](service-fabric-health-introduction.md) que são realizados. Os controlos que ocorrem durante uma atualização incluem testes para saber se o pacote de aplicações foi copiado corretamente, se a instância foi iniciada, e assim por diante.

A saúde da aplicação é uma agregação das entidades infantis da aplicação. Em suma, o Service Fabric avalia a saúde da aplicação através da saúde que é reportada na aplicação. Avalia também a saúde de todos os serviços para a aplicação desta forma. Service Fabric avalia ainda a saúde dos serviços de candidatura agregando a saúde dos seus filhos, como a réplica do serviço. Uma vez satisfeita a política de saúde da aplicação, a atualização pode prosseguir. Se a política de saúde for violada, a atualização da aplicação falha.

## <a name="upgrade-modes"></a>Modos de atualização
O modo que recomendamos para a atualização da aplicação é o modo monitorizado, que é o modo comumente utilizado. O modo monitorizado realiza a atualização num domínio de atualização e, se passarem todos os controlos de saúde (de acordo com a política especificada), passa automaticamente para o domínio da próxima atualização.  Se as verificações de saúde falharem e/ou os intervalos forem atingidos, a atualização é relançada para o domínio da atualização, ou o modo é alterado para manual não monitorizado. Pode configurar a atualização para escolher um desses dois modos para atualizações falhadas. 

O modo manual não monitorizado necessita de intervenção manual após cada atualização de um domínio de atualização, para iniciar a atualização no domínio da próxima atualização. Não são realizados controlos de saúde de tecido de serviço. O administrador realiza as verificações de saúde ou estado antes de iniciar a atualização no domínio da próxima atualização.

## <a name="upgrade-default-services"></a>Atualizar serviços por predefinição
Alguns parâmetros de serviço predefinidos definidos no manifesto de [aplicação](service-fabric-application-and-service-manifests.md) também podem ser atualizados como parte de uma atualização da aplicação. Apenas os parâmetros de serviço que suportam ser alterados através do [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) podem ser alterados como parte de uma atualização. O comportamento de alterar os serviços predefinidos durante a atualização da aplicação é o seguinte:

1. São criados serviços predefinidos no novo manifesto de aplicação que ainda não existem no cluster.
2. Os serviços predefinidos que existem nos manifestos de aplicação anteriores e novos são atualizados. Os parâmetros do serviço predefinido no manifesto de aplicação sobrepor os parâmetros do serviço existente. A atualização da aplicação será relançada automaticamente se a atualização de um serviço predefinido falhar.
3. Os serviços predefinidos que não existam no novo manifesto de aplicação são eliminados se existirem no cluster. **Note que a abater um serviço predefinido resultará na adesão de todo o estado desse serviço e não pode ser desfeita.**

Quando uma atualização da aplicação é revertida, os parâmetros de serviço padrão são revertidos para os seus valores antigos antes do início da atualização, mas os serviços eliminados não podem ser recriados com o seu antigo estado.

> [!TIP]
> A definição de config de cluster [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) deve ser *fiel* para ativar as regras 2) e 3) acima (atualização e eliminação do serviço predefinido). Esta funcionalidade é suportada a partir da versão 5.5 do Service Fabric.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualizar várias aplicações com pontos finais HTTPS
É necessário ter cuidado para não utilizar a **mesma porta** para diferentes instâncias da mesma aplicação ao utilizar http**S**. A razão é que o Tecido de Serviço não será capaz de atualizar o certificado para uma das instâncias de aplicação. Por exemplo, se a aplicação 1 ou a aplicação 2 quiserem atualizar o seu certificado 1 para o cert 2. Quando a atualização acontece, o Tecido de Serviço pode ter limpo o registo cert 1 com http.sys, mesmo que a outra aplicação ainda esteja a usá-lo. Para evitar isso, a Service Fabric deteta que já existe outra instância de candidatura registada no porto com o certificado (devido a http.sys) e falha a operação.

Por isso, a Service Fabric não suporta a modernização de dois serviços diferentes utilizando **a mesma porta** em diferentes instâncias de aplicação. Por outras palavras, não é possível utilizar o mesmo certificado em diferentes serviços no mesmo porto. Se precisa de um certificado partilhado na mesma porta, tem de garantir que os serviços são colocados em diferentes máquinas com restrições de colocação. Ou considere utilizar portas dinâmicas de tecido de serviço, se possível para cada serviço em cada instância de aplicação. 

Se vir uma falha de atualização com https, um aviso de erro a dizer "O Windows HTTP Server API não suporta vários certificados para aplicações que partilham uma porta."

## <a name="application-upgrade-flowchart"></a>Fluxograma de atualização de aplicação
O fluxograma que se segue a este parágrafo pode ajudá-lo a compreender o processo de atualização de uma aplicação De Tecido de Serviço. Em particular, o fluxo descreve como os intervalos de tempo, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, e *UpgradeHealthCheckInterval,* ajudam a controlar quando a atualização num domínio de atualização é considerada um sucesso ou uma falha.

![O processo de upgrade para uma aplicação de tecido de serviço][image]

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação Utilizando o Estúdio Visual](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando powerShell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.

Controle a forma como a sua aplicação melhora utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização](service-fabric-application-upgrade-data-serialization.md)de Dados .

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação referindo-se a [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de aplicações de [resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
