---
title: Atualização da aplicação do Tecido de Serviço
description: Este artigo fornece uma introdução à atualização de uma aplicação de Tecido de Serviço, incluindo a escolha de modos de atualização e realização de verificações de saúde.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: 8eecd923b009ecbe9f4e607ad57a99b3f20955b9
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309857"
---
# <a name="service-fabric-application-upgrade"></a>Atualização da aplicação do Tecido de Serviço
Uma aplicação Azure Service Fabric é uma coleção de serviços. Durante uma atualização, o Service Fabric compara o novo manifesto de [aplicação](service-fabric-application-and-service-manifests.md) com a versão anterior e determina quais os serviços na aplicação que requerem atualizações. O Service Fabric compara os números de versão do serviço com os números da versão na versão anterior. Se um serviço não tiver sido alterado, esse serviço não será atualizado.

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters?view=azure-dotnet#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s não são preservados através de uma atualização de aplicações. Para preservar os parâmetros de aplicação atuais, o utilizador deve obter os parâmetros primeiro e passá-los para a chamada de API de upgrade como abaixo:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>Visão geral das atualizações de rolamento
Numa atualização de aplicações rolantes, a atualização é realizada por etapas. Em cada fase, a atualização é aplicada a um subconjunto de nós no cluster, chamado domínio de atualização. Como resultado, a aplicação permanece disponível durante toda a atualização. Durante a atualização, o cluster pode conter uma mistura das versões antigas e novas.

Por essa razão, as duas versões devem ser compatíveis para a frente e para trás. Se não forem compatíveis, o administrador da aplicação é responsável pela realização de uma atualização de várias fases para manter a disponibilidade. Numa atualização em várias fases, o primeiro passo é o upgrade para uma versão intermédia da aplicação que é compatível com a versão anterior. O segundo passo é atualizar a versão final que quebra a compatibilidade com a versão pré-atualização, mas é compatível com a versão intermédia.

Os domínios de atualização são especificados no manifesto do cluster quando configurar o cluster. Os domínios de atualização não recebem atualizações numa determinada encomenda. Um domínio de atualização é uma unidade lógica de implementação para uma aplicação. Os domínios de atualização permitem que os serviços permaneçam em alta disponibilidade durante uma atualização.

As atualizações não rolantes são possíveis se a atualização for aplicada a todos os nós do cluster, o que acontece quando a aplicação tem apenas um domínio de atualização. Esta abordagem não é recomendada, uma vez que o serviço diminui e não está disponível no momento da atualização. Além disso, o Azure não oferece quaisquer garantias quando um cluster é configurado com apenas um domínio de atualização.

Após a conclusão da atualização, todos os serviços e réplicas (instâncias) permanecerão na mesma versão, ou seja, se a atualização for bem sucedida, serão atualizados para a nova versão; se a atualização falhar e for revirada, serão revertidas para a versão antiga.

## <a name="health-checks-during-upgrades"></a>Verificações de saúde durante as atualizações
Para uma atualização, as políticas de saúde têm de ser definidas (ou podem ser utilizados valores predefinidos). Uma atualização é denominada com sucesso quando todos os domínios de atualização são atualizados dentro dos intervalos de tempo especificados, e quando todos os domínios de atualização são considerados saudáveis.  Um domínio de atualização saudável significa que o domínio da atualização passou todos os controlos de saúde especificados na política de saúde. Por exemplo, uma política de saúde pode ordenar que todos os serviços dentro de uma instância de aplicação devem ser *saudáveis,* uma vez que a saúde é definida pela Service Fabric.

As políticas de saúde e os controlos durante a atualização pela Service Fabric são agnósticos de serviço e aplicação. Ou seja, não são feitos testes específicos de serviço.  Por exemplo, o seu serviço pode ter um requisito de produção, mas a Service Fabric não tem a informação para verificar a produção. Consulte os [artigos de saúde](service-fabric-health-introduction.md) para os controlos que são efetuados. Os controlos que ocorrem durante uma atualização incluem testes para saber se o pacote de aplicações foi copiado corretamente, se a instância foi iniciada, e assim por diante.

A saúde da aplicação é uma agregação das entidades infantis do pedido. Em suma, a Service Fabric avalia a saúde da aplicação através da saúde que é reportada na aplicação. Avalia ainda a saúde de todos os serviços para a aplicação desta forma. A Service Fabric avalia ainda a saúde dos serviços de aplicação agregando a saúde dos seus filhos, como a réplica do serviço. Uma vez satisfeita a política de saúde da aplicação, a atualização pode prosseguir. Se a política de saúde for violada, a atualização da aplicação falha.

## <a name="upgrade-modes"></a>Modos de atualização
O modo que recomendamos para a atualização da aplicação é o modo monitorizado, que é o modo normalmente utilizado. O modo monitorizado executa a atualização num domínio de atualização e se todos os controlos de saúde passarem (de acordo com a política especificada), passa automaticamente para o domínio de atualização seguinte.  Se os controlos de saúde falharem e/ou forem alcançados intervalos, a atualização é revirada para o domínio da atualização ou o modo é alterado para manual não monitorizado. Pode configurar a atualização para escolher um desses dois modos para atualizações falhadas. 

O modo manual não monitorizado necessita de uma intervenção manual após cada atualização num domínio de atualização, para iniciar a atualização no domínio da atualização seguinte. Não são efetuados controlos de saúde do Service Fabric. O administrador executa as verificações de saúde ou estado antes de iniciar a atualização no domínio da atualização seguinte.

## <a name="upgrade-default-services"></a>Atualizar serviços predefinidos
Alguns parâmetros de serviço predefinidos definidos no manifesto de [aplicação](service-fabric-application-and-service-manifests.md) também podem ser atualizados como parte de uma atualização de aplicação. Apenas os parâmetros de serviço que suportam a ser alterados através [do Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) podem ser alterados como parte de uma atualização. O comportamento da alteração dos serviços predefinidos durante a atualização da aplicação é o seguinte:

1. São criados os serviços predefinidos na nova aplicação que já não existem no cluster.
2. Os serviços predefinidos que existem nos manifestos de aplicação anteriores e novos são atualizados. Os parâmetros do serviço predefinido na nova aplicação manifestam-se sobrepor os parâmetros do serviço existente. A atualização da aplicação será reversão automaticamente se a atualização de um serviço predefinido falhar.
3. Os serviços predefinidos que não existem no novo manifesto de aplicação são eliminados se existirem no cluster. **Note que a eliminação de um serviço predefinido resultará na eliminação de todo o estado do serviço e não pode ser desfeito.**

Quando uma atualização de aplicação é revertida, os parâmetros de serviço predefinidos são revertidos de volta aos seus valores antigos antes do início da atualização, mas os serviços eliminados não podem ser recriados com o seu estado antigo.

> [!TIP]
> A definição de configuração do cluster [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) deve ser *verdadeira* para permitir as regras 2) e 3) acima (atualização e eliminação do serviço predefinido). Esta funcionalidade é suportada a partir da versão 5.5 do Service Fabric.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualizar várias aplicações com pontos finais HTTPS
É necessário ter cuidado para não utilizar a **mesma porta** para diferentes instâncias da mesma aplicação quando utilizar HTTP**S**. A razão é que o Service Fabric não será capaz de atualizar o cert para uma das instâncias de aplicação. Por exemplo, se a aplicação 1 ou a aplicação 2 quiserem atualizar o seu cert 1 a cert 2. Quando a atualização acontece, o Service Fabric pode ter limpo o registo cert 1 com http.sys mesmo que a outra aplicação ainda esteja a usá-lo. Para evitar isto, a Service Fabric deteta que já existe outra instância de aplicação registada na porta com o certificado (devido a http.sys) e falha a operação.

Assim, a Service Fabric não suporta a atualização de dois serviços diferentes utilizando **a mesma porta** em diferentes instâncias de aplicação. Por outras palavras, não pode utilizar o mesmo certificado em diferentes serviços na mesma porta. Se precisa de ter um certificado partilhado na mesma porta, tem de garantir que os serviços são colocados em diferentes máquinas com restrições de colocação. Ou considere usar portas dinâmicas de Tecido de Serviço, se possível, para cada serviço em cada instância de aplicação. 

Se vir uma falha de atualização com https, um aviso de erro que diz "A API do Servidor HTTP do Windows HTTP não suporta vários certificados para aplicações que partilham uma porta."

## <a name="application-upgrade-flowchart"></a>Fluxograma de upgrade de aplicações
O fluxograma que segue este parágrafo pode ajudá-lo a entender o processo de upgrade de uma aplicação de Tecido de Serviço. Em particular, o fluxo descreve como os intervalos de tempo, incluindo *o HealthCheckStableDuration,* *HealthCheckRetryTimeout*e *UpgradeHealthCheckInterval,* ajudam a controlar quando a atualização num domínio de atualização é considerada um sucesso ou uma falha.

![O processo de upgrade para uma Aplicação de Tecido de Serviço][image]

## <a name="next-steps"></a>Passos seguintes
[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

Controle como a sua aplicação atualiza utilizando [parâmetros de atualização.](service-fabric-application-upgrade-parameters.md)

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização de Dados.](service-fabric-application-upgrade-data-serialization.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [Tópicos Avançados.](service-fabric-application-upgrade-advanced.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos em [Atualizações de Aplicações de Resolução de Problemas](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png