---
title: Tópicos avançados de atualização de aplicações
description: Este artigo abrange alguns tópicos avançados relativos à atualização de uma aplicação de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: cc2fdc8f99b74078bd8d5274cbe52265ab8455ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248089"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização da aplicação do Tecido de Serviço: Tópicos avançados

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Adicionar ou remover tipos de serviço durante uma atualização de aplicação

Se um novo tipo de serviço for adicionado a uma aplicação publicada como parte de uma atualização, então o novo tipo de serviço é adicionado à aplicação implementada. Esta atualização não afeta nenhuma das instâncias de serviço que já faziam parte da aplicação, mas deve ser criada uma instância do tipo de serviço que foi adicionado para que o novo tipo de serviço esteja ativo (ver [New-ServiceFabricService).](/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)

Da mesma forma, os tipos de serviço podem ser removidos de uma aplicação como parte de uma atualização. No entanto, todas as instâncias de serviço do tipo de serviço a remover devem ser removidas antes de prosseguir com a atualização (ver [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evite quedas de ligação durante o tempo de inatividade previsto para o serviço apátrida

Para os tempos de inatividade previstos, tais como atualização de aplicações/clusters ou desativação do nó, as ligações podem ser retiradas à medida que o ponto final exposto é removido após a queda do caso, o que resulta em fechos de ligação vigorosos.

Para evitar isto, configurar a função *RequestDrain* adicionando uma *duração de atraso de caso* na configuração do serviço para permitir que os pedidos existentes dentro do cluster drene nos pontos finais expostos. Isto é conseguido à medida que o ponto final anunciado pela instância apátrida é removido *antes* do atraso começar antes de fechar o caso. Este atraso permite que os pedidos existentes drene graciosamente antes que o caso realmente se atrase. Os clientes são notificados da alteração do ponto final por uma função de retorno no momento do início do atraso, para que possam re-resolver o ponto final e evitar o envio de novos pedidos para a instância que está a decorrer. Estes pedidos podem ser originários de clientes que utilizem [o Reverse Proxy](./service-fabric-reverseproxy.md) ou utilizem a API's de resolução de pontos finais de serviço com o modelo de notificação[(ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) para atualizar os pontos finais.

### <a name="service-configuration"></a>Configuração do serviço

Existem várias formas de configurar o atraso no lado do serviço.

 * **Ao criar um novo serviço,** especifique: `-InstanceCloseDelayDuration`

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Ao definir o serviço na secção de incumprimentos no manifesto de aplicação,** atribua o `InstanceCloseDelayDurationSeconds` imóvel:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Ao atualizar um serviço existente,** especifique: `-InstanceCloseDelayDuration`

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Ao criar ou atualizar um serviço existente através do modelo ARM,** especifique o `InstanceCloseDelayDuration` valor (versão API suportada mínima: 2019-11-01-pré-visualização):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Configuração do cliente

Para receber a notificação quando um ponto final tiver sido alterado, os clientes devem registar uma chamada ver [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
A notificação de alteração é uma indicação de que os pontos finais mudaram, o cliente deve re-resolver os pontos finais, e não usar os pontos finais que não são mais anunciados, uma vez que irão descer em breve.

### <a name="optional-upgrade-overrides"></a>Sobreposições de upgrade opcionais

Além de definir as durações de atraso por defeito por serviço, também pode anular o atraso durante a atualização da aplicação/cluster utilizando a mesma `InstanceCloseDelayDurationSec` opção ( )

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A duração do atraso ultrapassado aplica-se apenas à instância de atualização invocada e não altera as configurações individuais de atraso de serviço. Por exemplo, pode usá-lo para especificar um atraso de `0` modo a evitar quaisquer atrasos de atualização pré-configurados.

> [!NOTE]
> * As definições para drenar os pedidos não poderão impedir o equilibrador de carga Azure de enviar novos pedidos para os pontos finais que estão a ser drenados.
> * Um mecanismo de resolução baseado em reclamações não resultará numa drenagem graciosa dos pedidos, uma vez que desencadeia uma resolução de serviço após uma falha. Tal como descrito anteriormente, este deve ser melhorado para subscrever as notificações de alteração de ponto final utilizando [o ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * As configurações não são honradas quando a atualização é infundada, ou seja, quando as réplicas não serão derrubas durante a atualização.
>
>

> [!NOTE]
> Esta função pode ser configurada nos serviços existentes utilizando Update-ServiceFabricService cmdlet ou o modelo ARM, como mencionado acima, quando a versão do código cluster for 7.1.XXX ou acima.
>
>

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [!NOTE]
> O modo de atualização *monitorado* é recomendado para todas as atualizações do Tecido de Serviço.
> O modo de atualização *Não monitorizadomanual* só deve ser considerado para atualizações falhadas ou suspensas. 
>
>

No modo *Monitorado,* o Service Fabric aplica políticas de saúde para garantir que a aplicação é saudável à medida que a atualização progride. Se as políticas de saúde forem violadas, a atualização é suspensa ou revoada automaticamente dependendo da Falha de *Produção*especificada .

No modo *Não monitorizadoManual,* o administrador da aplicação tem controlo total sobre a progressão da atualização. Este modo é útil quando se aplicam políticas de avaliação de saúde personalizadas ou se realizam atualizações não convencionais para contornar completamente a monitorização da saúde (por exemplo, a aplicação já está em perda de dados). Uma atualização em execução neste modo suspender-se-á após completar cada UD e deve ser explicitamente retomada utilizando [o Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando uma atualização for suspensa e pronta a ser retomada pelo utilizador, o seu estado de atualização mostrará *RollforwardPending* (ver [Estado de Atualização).](/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)

Finalmente, o modo *UnmonitoredAuto* é útil para realizar iterações de upgrade rápido durante o desenvolvimento ou teste do serviço, uma vez que não é necessária nenhuma entrada do utilizador e nenhuma política de saúde da aplicação é avaliada.

## <a name="upgrade-with-a-diff-package"></a>Upgrade com um pacote diff

Em vez de forenar um pacote completo de aplicações, as atualizações também podem ser realizadas fornecendo pacotes difusos que contenham apenas os pacotes de código/config/dados atualizados, juntamente com o manifesto completo da aplicação e manifestos de serviço completos. Pacotes de aplicação completos só são necessários para a instalação inicial de uma aplicação ao cluster. As atualizações subsequentes podem ser de pacotes de aplicações completos ou pacotes difusos.  

Qualquer referência no manifesto de aplicação ou manifestos de serviço de um pacote difuso que não possa ser encontrado no pacote de aplicações é automaticamente substituída pela versão atualmente prevista.

Os cenários para a utilização de um pacote difuso são:

* Quando tem um grande pacote de aplicações que faz referência a vários ficheiros manifestos de serviço e/ou vários pacotes de código, config pacotes ou pacotes de dados.
* Quando tem um sistema de implementação que gera o layout de construção diretamente do processo de construção da sua aplicação. Neste caso, mesmo que o código não tenha mudado, os conjuntos recém-construídos recebem uma caixa de verificação diferente. A utilização de um pacote completo de aplicações exigiria que atualizasse a versão em todos os pacotes de código. Utilizando um pacote difuso, apenas fornece os ficheiros que mudaram e os ficheiros manifestos onde a versão foi alterada.

Quando uma aplicação é atualizada usando o Visual Studio, um pacote difuso é publicado automaticamente. Para criar um pacote difuso manualmente, o manifesto de aplicação e os manifestos de serviço devem ser atualizados, mas apenas os pacotes alterados devem ser incluídos no pacote final de candidatura.

Por exemplo, vamos começar com a seguinte aplicação (números de versão previstos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que queria atualizar apenas o pacote de código de serviço1 usando um pacote difuso. A sua aplicação atualizada tem as seguintes alterações na versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Neste caso, atualiza o manifesto da aplicação para 2.0.0 e o manifesto de serviço para o serviço1 para refletir a atualização do pacote de código. A pasta para o seu pacote de candidaturas teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Por outras palavras, crie normalmente um pacote de aplicação completo e, em seguida, remova quaisquer pastas de pacote de código/config/data para as quais a versão não tenha sido alterada.

## <a name="upgrade-application-parameters-independently-of-version"></a>Atualizar parâmetros de aplicação independentemente da versão

Por vezes, é desejável alterar os parâmetros de uma aplicação de Tecido de Serviço sem alterar a versão manifesta. Isto pode ser feito convenientemente usando a bandeira **-ApplicationParameter** com o **start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell cmdlet. Assuma uma aplicação de Tecido de Serviço com as seguintes propriedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Agora, atualize a aplicação utilizando o **cmdlet Start-ServiceFabricApplicationUpgrade.** Este exemplo mostra uma atualização monitorizada, mas também pode ser utilizada uma atualização não monitorizada. Para ver uma descrição completa das bandeiras aceites por este cmdlet, consulte a referência do [módulo PowerShell do tecido de serviço Azure](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

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

Embora as atualizações possam ser lançadas para a frente num dos três modos *(Monitorado*, *UnmonitoredAuto*, ou *UnmonitoredManual*), só podem ser reroladas no modo *UnmonitoredAuto* ou *UnmonitoredManual.* Voltar a rolar no modo *UnmonitoredAuto* funciona da mesma forma que avançar com a exceção de que o valor padrão do *UpgradeReplicaSetCheckTimeout* é diferente - consulte [parâmetros de atualização da aplicação](service-fabric-application-upgrade-parameters.md). Voltar a rolar no modo *UnmonitoredManual* funciona da mesma forma que avançar - o revés suspender-se-á após completar cada UD e deve ser explicitamente retomado usando [o Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar com o revés.

As reversãos podem ser ativadas automaticamente quando as políticas de saúde de uma atualização no modo *Monitorado* com uma *FalhaAcção* de *Reversão* são violadas (ver [Parâmetros de Atualização de Aplicações)](service-fabric-application-upgrade-parameters.md)ou usando explicitamente [o Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante o reversão, o valor do *UpgradeReplicaSetCheckTimeout* e do modo ainda pode ser alterado a qualquer momento utilizando [o Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passos seguintes
[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

Controle como a sua aplicação atualiza utilizando [parâmetros de atualização.](service-fabric-application-upgrade-parameters.md)

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização de Dados.](service-fabric-application-upgrade-data-serialization.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos em [Atualizações de Aplicações de Resolução de Problemas](service-fabric-application-upgrade-troubleshooting.md).
