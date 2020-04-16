---
title: Tópicos avançados de atualização de aplicações
description: Este artigo aborda alguns tópicos avançados relativos à atualização de uma aplicação de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: a12d2ec55bda95c1c61d4a73c76f4a777f4237f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414489"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade de aplicação service Fabric: Tópicos avançados

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Adicionar ou remover tipos de serviço durante uma atualização de aplicações

Se um novo tipo de serviço for adicionado a uma aplicação publicada como parte de uma atualização, então o novo tipo de serviço é adicionado à aplicação implementada. Esta atualização não afeta nenhuma das instâncias de serviço que já faziam parte da aplicação, mas deve ser criada uma instância do tipo de serviço que foi adicionada para que o novo tipo de serviço esteja ativo (ver [New-ServiceFabricService).](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)

Da mesma forma, os tipos de serviço podem ser removidos de uma aplicação como parte de uma atualização. No entanto, todas as instâncias de serviço do tipo de serviço a remover devem ser removidas antes de proceder à atualização (ver [Remover-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evite quedas de ligação durante o tempo de paragem planeado para o serviço apátrida

Para os tempos de paragem de instâncias apátridas planeadas, tais como a atualização de aplicações/cluster ou a desativação do nó, as ligações podem ser retiradas devido ao ponto final exposto ser removido após a ocorrência ter sido baixada, o que resulta em encerramentos de ligação forçadas.

Para evitar isto, configure a função *RequestDrain* (pré-visualização) adicionando uma *duração de atraso de atraso na* configuração do serviço para permitir a drenagem enquanto recebe pedidos de outros serviços dentro do cluster e estão a utilizar o Proxy Inverso ou a utilizar a API de resolução com o modelo de notificação para atualizar pontos finais. Isto garante que o ponto final anunciado pela instância apátrida seja removido *antes* do atraso começar antes de fechar a instância. Este atraso permite que os pedidos existentes escorram graciosamente antes que a instância realmente desça. Os clientes são notificados da alteração do ponto final por uma função de callback no momento do início do atraso, para que possam reresolver o ponto final e evitar o envio de novos pedidos para a instância que está a acontecer.

### <a name="service-configuration"></a>Configuração do serviço

Existem várias formas de configurar o atraso no lado do serviço.

 * **Ao criar um novo serviço,** especifique um: `-InstanceCloseDelayDuration`

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * Ao definir o serviço na secção de **incumprimentos no manifesto de aplicação,** atribua a `InstanceCloseDelayDurationSeconds` propriedade:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Ao atualizar um serviço existente,** especifique um: `-InstanceCloseDelayDuration`

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Configuração do cliente

Para receber a notificação quando um ponto final tiver mudado, os clientes devem registar um callback ver [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
A notificação de alteração é uma indicação de que os pontos finais mudaram, o cliente deve reresolver os pontos finais, e não utilizar os pontos finais que já não são anunciados, uma vez que irão descer em breve.

### <a name="optional-upgrade-overrides"></a>Sobreposições de upgrade opcionais

Além de definir as durações de atraso por serviço, também pode anular`InstanceCloseDelayDurationSec`o atraso durante a atualização de aplicações/clusterutilizando a mesma opção ( ) ):

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A duração do atraso aplica-se apenas à instância de atualização invocada e não altera as configurações individuais de atraso de serviço. Por exemplo, pode usá-lo para `0` especificar um atraso de modo a ignorar quaisquer atrasos de atualização pré-configurados.

> [!NOTE]
> A definição para drenar pedidos não é honrada para pedidos do equilibrador de carga Azure. A definição não é honrada se o serviço de chamadas usar a determinação baseada em reclamações.
>
>

> [!NOTE]
> Esta funcionalidade pode ser configurada nos serviços existentes utilizando o cmdlet Update-ServiceFabricService, conforme mencionado acima, quando a versão do código de cluster for 7.1.XXX ou superior.
>
>

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [!NOTE]
> O modo de atualização *Monitorizado* é recomendado para todas as atualizações do Tecido de Serviço.
> O modo de atualização *Manual Unmonitorsó* deve ser considerado para atualizações falhadas ou suspensas. 
>
>

No modo *Monitorizado,* o Tecido de Serviço aplica políticas de saúde para garantir que a aplicação é saudável à medida que a atualização progride. Se as políticas de saúde forem violadas, a atualização é suspensa ou relançada automaticamente dependendo da ação de *falhas*especificada .

No modo *UnmonitoredManual,* o administrador de aplicação tem total controlo sobre a progressão da atualização. Este modo é útil na aplicação de políticas personalizadas de avaliação da saúde ou na realização de atualizações não convencionais para contornar completamente a monitorização da saúde (por exemplo, a aplicação já está em perda de dados). Uma atualização em execução neste modo suspender-se-á depois de completar cada UD e deve ser explicitamente retomada utilizando o [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando uma atualização for suspensa e pronta a ser retomada pelo utilizador, o seu estado de atualização mostrará *RollforwardPending* (ver [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Finalmente, o modo *UnmonitoredAuto* é útil para realizar iterações de atualização rápida durante o desenvolvimento do serviço ou testes, uma vez que não é necessária nenhuma entrada do utilizador e não são avaliadas políticas de saúde de aplicação.

## <a name="upgrade-with-a-diff-package"></a>Upgrade com um pacote difuso

Em vez de fornecer um pacote completo de aplicações, as atualizações também podem ser efetuadas através do fornecimento de pacotes difusos que contenham apenas os pacotes de código/config/dados atualizados, juntamente com o manifesto completo de aplicação e manifestos completos de serviço. Os pacotes completos de aplicação só são necessários para a instalação inicial de uma aplicação ao cluster. As atualizações subsequentes podem ser de pacotes completos de aplicações ou pacotes difusos.  

Qualquer referência no manifesto de aplicação ou manifestos de serviço de um pacote difuso que não possa ser encontrado no pacote de aplicação é automaticamente substituída pela versão atualmente provisionada.

Os cenários para a utilização de um pacote difuso são:

* Quando tem um grande pacote de aplicações que faz referência a vários ficheiros de manifesto de serviço e/ou vários pacotes de código, pacotes de config ou pacotes de dados.
* Quando se tem um sistema de implementação que gere o layout de construção diretamente do seu processo de construção de aplicações. Neste caso, mesmo que o código não tenha mudado, os conjuntos recém-construídos recebem um controlo diferente. A utilização de um pacote completo de aplicações exigiria que atualizasse a versão em todos os pacotes de código. Utilizando um pacote difuso, apenas fornece os ficheiros que mudaram e os ficheiros manifestos onde a versão foi alterada.

Quando uma aplicação é atualizada utilizando o Visual Studio, um pacote difuso é publicado automaticamente. Para criar manualmente um pacote difuso, o manifesto de aplicação e os manifestos de serviço devem ser atualizados, mas apenas os pacotes alterados devem ser incluídos no pacote de aplicação final.

Por exemplo, comecemos pela seguinte aplicação (números de versão fornecidos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que queria atualizar apenas o pacote de código de serviço1 usando um pacote difuso. A sua aplicação atualizada tem as seguintes alterações de versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Neste caso, atualiza o manifesto de aplicação para 2.0.0 e o manifesto de serviço para o serviço1 para refletir a atualização do pacote de código. A pasta para o seu pacote de aplicações teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Por outras palavras, crie normalmente um pacote completo de aplicação e, em seguida, remova quaisquer pastas de pacote de código/config/data para as quais a versão não tenha mudado.

## <a name="upgrade-application-parameters-independently-of-version"></a>Upgrade de parâmetros de aplicação independentemente da versão

Por vezes, é desejável alterar os parâmetros de uma aplicação De Tecido de Serviço sem alterar a versão manifesto. Isto pode ser feito convenientemente utilizando a bandeira **-ApplicationParameter** com o sistema de fabricação powerShell do serviço **Start-ServiceFabricApplicationUpgrade** Azure PowerShell. Assuma uma aplicação de Tecido de Serviço com as seguintes propriedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Agora, atualize a aplicação utilizando o **cmdlet Start-ServiceFabricApplicationUpgrade.** Este exemplo mostra uma atualização monitorizada, mas também pode ser utilizada uma atualização não monitorizada. Para ver uma descrição completa das bandeiras aceites por este cmdlet, consulte a referência do módulo PowerShell de tecido de [serviço Azure](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Após a atualização, confirme que a aplicação tem os parâmetros atualizados e a mesma versão:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Reverter as atualizações de aplicações

Embora as atualizações possam ser lançadas para a frente num dos três modos *(Monitorizado*, *UnmonitoredAuto*, ou *UnmonitoredManual),* só podem ser reencaminhados no modo *UnmonitoredAuto* ou *UnmonitoredManual.* Voltar a rolar no modo *UnmonitoredAuto* funciona da mesma forma que avançar com a exceção de que o valor padrão do *UpgradeReplicaCheckCheckTimeout* é diferente - ver [Parâmetros](service-fabric-application-upgrade-parameters.md)de atualização de aplicações . Voltar a rolar no modo *Unmonitormanual* funciona da mesma forma que avançar - a reversão suspender-se-á após completar cada UD e deve ser explicitamente retomada utilizando o [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar com a reversão.

As reversão podem ser ativadas automaticamente quando as políticas de saúde de uma atualização no modo *Monitorizado* com uma *FalhaAction* of *Rollback* são violadas (ver Parâmetros de atualização de [aplicações](service-fabric-application-upgrade-parameters.md)) ou utilizar explicitamente o [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante a reversão, o valor do *UpgradeReplicaCheckCheckTimeout* e o modo ainda podem ser alterados a qualquer momento utilizando o [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação Utilizando o Estúdio Visual](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando powershell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.

Controle a forma como a sua aplicação melhora utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização](service-fabric-application-upgrade-data-serialization.md)de Dados .

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de aplicações de [resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)
