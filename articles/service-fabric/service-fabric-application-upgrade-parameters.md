---
title: 'Atualização da aplicação: parâmetros de atualização'
description: Descreve parâmetros relacionados com a atualização de uma aplicação de Tecido de Serviço, incluindo verificações de saúde para executar e políticas para desfazer automaticamente a atualização.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75377978"
---
# <a name="application-upgrade-parameters"></a>Parâmetros da atualização da aplicação
Este artigo descreve os vários parâmetros que se aplicam durante a atualização de uma aplicação Azure Service Fabric. Os parâmetros de atualização da aplicação controlam os tempos e os controlos de saúde que são aplicados durante a atualização, e especificam as políticas que devem ser aplicadas quando uma atualização falha. Os parâmetros de aplicação aplicam-se às atualizações utilizando:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

As atualizações da aplicação são iniciadas através de um dos três modos de atualização selecionáveis pelo utilizador. Cada modo tem o seu próprio conjunto de parâmetros de aplicação:
- Monitorizado
- Auto não monitorizado
- Manual não monitorizado

Os parâmetros aplicáveis e opcionais são descritos em cada secção da seguinte forma.

## <a name="visual-studio-and-powershell-parameters"></a>Parâmetros do Estúdio Visual e da PowerShell

Upgrades de aplicações de tecido de serviço utilizando powerShell use o comando [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) O modo de atualização é selecionado através do parâmetro **Manual Monitorizado,** **Não Monitorizado**ou **Não Monitorizado** para [iniciar o Serviço de Aplicação de Aplicações .](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)

Os parâmetros de atualização da aplicação Visual Studio Service Fabric são definidos através do diálogo de definições de atualização do estúdio visual. O modo de atualização do Estúdio Visual é selecionado utilizando a caixa de dropdown do **Modo upgrade** para **monitorizado**, **UnmonitoredAuto**ou **UnmonitoredManual**. Para mais informações, consulte [Configure a atualização de uma aplicação Service Fabric no Estúdio Visual](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Parâmetros necessários
(PS=PowerShell, VS=Estúdio Visual)

| Parâmetro | Aplica-se A | Descrição |
| --- | --- | --- |
ApplicationName |PS| Nome da aplicação que está a ser atualizada. Exemplos: tecido:/Objetos visuais, tecido:/ClusterMonitor. |
AplicaçãoTypeVersion|PS|A versão do tipo de aplicação que os alvos de atualização. |
FalhaAction |PS, VS|Os valores permitidos são **Rollback,** **Manual**e **Inválidos.** A ação compensatória a realizar quando uma atualização *monitorizada* encontra a política de monitorização ou as violações das políticas de saúde. <br>**A reversão** especifica que a atualização voltará automaticamente para a versão pré-actualização. <br>**O manual** indica que a atualização passará para o modo de atualização *Manual Não monitorizado.* <br>**Inválido** indica que a ação de falha é inválida.|
Monitorizado |PS|Indica que o modo de atualização é monitorizado. Depois de o cmdlet terminar uma atualização para um domínio de upgrade, se a saúde do domínio de upgrade e do cluster cumprirem as políticas de saúde que define, o Service Fabric atualiza o próximo domínio de atualização. Se o domínio de atualização ou cluster não cumprir as políticas de saúde, a atualização falha e o Tecido de Serviço reverte a atualização para o domínio de atualização ou reverte para o modo manual de acordo com a política especificada. Este é o modo recomendado para atualizações de aplicações em ambiente de produção. |
UpgradeMode | VS | Os valores permitidos são **monitorizados** (predefinidos), **UnmonitoredAuto**ou **UnmonitoredManual**. Consulte os parâmetros PowerShell para cada modo neste artigo para obter mais detalhes. |
Auto-monitorizado | PS | Indica que o modo de atualização não é monitorizado automaticamente. Depois de o Service Fabric atualizar um domínio de upgrade, o Service Fabric atualiza o próximo domínio de atualização, independentemente do estado de saúde da aplicação. Este modo não é recomendado para produção, e só é útil durante o desenvolvimento de uma aplicação. |
Manual não monitorizado | PS | Indica que o modo de atualização não é monitorizado manual. Depois de o Service Fabric atualizar um domínio de atualização, aguarda-lhe a atualização do próximo domínio de atualização utilizando o cmdlet *resume-ServiceFabricApplicationUpgrade.* |

### <a name="optional-parameters"></a>Parâmetros opcionais

Os parâmetros de avaliação da saúde são opcionais. Se os critérios de avaliação de saúde não forem especificados quando uma atualização começa, o Service Fabric utiliza as políticas de saúde de aplicação especificadas na instância ApplicationManifest.xml da instância de aplicação.

> [!div class="mx-tdBreakAll"]
> | Parâmetro | Aplica-se A | Descrição |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Especifica as sobreposições para os parâmetros de aplicação.<br>Os parâmetros de aplicação PowerShell são especificados como pares de nome/valor hashtable. Por exemplo, @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Os parâmetros de aplicação do Estúdio Visual podem ser especificados no diálogo de aplicação de aplicação de tecido de serviço de publicação no campo de ficheiros de parâmetros de **aplicação.**
> | Confirmar |PS| Os valores permitidos são **verdadeiros** e **falsos.** Solicita a confirmação antes de executar o cmdlet. |
> | Considere advertênciaserro |PS, VS |Os valores permitidos são **verdadeiros** e **falsos.** O valor predefinido é **Falso**. Trate os eventos de saúde de alerta para a aplicação como erros ao avaliar a saúde da aplicação durante a atualização. Por padrão, o Service Fabric não avalia os eventos de saúde de alerta como falhas (erros), pelo que a atualização pode prosseguir mesmo que haja eventos de alerta. |
> | Política de Saúde DefaultServiceType | PS, VS |Especifica a política de saúde para o tipo de serviço padrão a utilizar para a atualização monitorizada no formato MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Por exemplo, 5,10,15 indica os seguintes valores: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Os valores permitidos são **verdadeiros** e **falsos.** Indica que o processo de atualização ignora a mensagem de aviso e força a atualização mesmo quando o número da versão não mudou. Isto é útil para testes locais, mas não é recomendado para ser usado em um ambiente de produção, uma vez que requer a remoção da implementação existente que causa tempo de inatividade e perda de dados potenciais. |
> | Reinício da força |PS, VS |Se atualizar uma configuração ou pacote de dados sem atualizar o código de serviço, o serviço só é reiniciado se a propriedade ForceRestart estiver definida para **True**. Quando a atualização estiver concluída, o Service Fabric notifica o serviço de que está disponível um novo pacote de configuração ou pacote de dados. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode reiniciar-se. |
> | HealthCheckRetryTimeoutSec |PS, VS |A duração (em segundos) que o Serviço Fabric continua a realizar uma avaliação de saúde antes de declarar a atualização como falhada. O padrão é de 600 segundos. Esta duração começa após o alcance do *HealthCheckWaitDurationSec.* Dentro deste *HealthCheckRetryTimeout,* o Tecido de Serviço pode realizar múltiplas verificações de saúde da saúde da aplicação. O valor predefinido é de 10 minutos e deve ser personalizado adequadamente para a sua aplicação. |
> | HealthCheckStableDurationSec |PS, VS |A duração (em segundos) para verificar se a aplicação está estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Esta duração de espera é utilizada para evitar alterações não detetadas na saúde logo após a verificação de saúde. O valor predefinido é de 120 segundos e deve ser personalizado adequadamente para a sua aplicação. |
> | HealthCheckWaitDurationSec |PS, VS | O tempo para esperar (em segundos) após a atualização terminar no domínio de atualização antes do Service Fabric avaliar a saúde da aplicação. Esta duração também pode ser considerada como o tempo que uma aplicação deve ser executar antes de poder ser considerada saudável. Se o exame de saúde passar, o processo de atualização prossegue para o próximo domínio de atualização.  Se o exame de saúde falhar, o Tecido de Serviço aguarda o [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) antes de voltar a tentar a verificação de saúde até que o *HealthCheckRetryTimeoutSec* seja atingido. O valor predefinido e recomendado é de 0 segundos. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |O valor predefinido e recomendado é 0. Especifique o número máximo de aplicações implementadas (ver secção de [Saúde)](service-fabric-health-introduction.md)que podem ser insalubres antes que a aplicação seja considerada insalubre e falhe na atualização. Este parâmetro define a saúde da aplicação no nó e ajuda a detetar problemas durante a atualização. Tipicamente, as réplicas da aplicação ficam equilibradas em carga para o outro nó, o que permite que a aplicação pareça saudável, permitindo assim que a atualização prossiga. Ao especificar uma saúde rigorosa de *Aplicações MaxPercentUnhealthy,* o Tecido de Serviço pode detetar rapidamente um problema com o pacote de aplicação e ajudar a produzir uma atualização rápida de falhas. |
> | MaxPercentUnhealthyServices |PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de serviços na instância de candidatura que podem ser insalubres antes que a aplicação seja considerada insalubre e falhe na atualização. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de divisórias num serviço que pode ser insalubre antes que o serviço seja considerado insalubre. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Um parâmetro para *DefaultServiceTypeHealthPolicy* e *ServiceTypeHealthPolicyMap*. O valor predefinido e recomendado é 0. Especifique o número máximo de réplicas na partição que podem ser insalubres antes que a partição seja considerada insalubre. |
> | Mapa de Política de Saúde do ServiçoTypeHealth | PS, VS | Representa a política de saúde utilizada para avaliar a saúde dos serviços pertencentes a um tipo de serviço. Toma uma entrada de mesa hash no seguinte formato: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Por exemplo: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | TimeoutSec | PS, VS | Especifica o período de tempo limite em segundos para a operação. |
> | UpgradeDomainTimeoutSec |PS, VS |Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se este prazo for atingido, a atualização para e prossegue com base na definição para *a FailureAction*. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. |
> | UpgradeReplicaCheckCheckTimeoutSec |PS, VS |Medido em segundos.<br>**Serviço apátrida**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que estão disponíveis instâncias adicionais do serviço. Se a contagem de instâncias-alvo for superior a uma, o Tecido de Serviço aguarda que mais de uma instância esteja disponível, até um valor máximo de tempo-out. Este intervalo de tempo é especificado utilizando a propriedade *UpgradeReplicaCheckCheckTimeoutSec.* Se o prazo expirar, o Service Fabric procede à atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias-alvo for uma, o Tecido de Serviço não espera e procede imediatamente à atualização.<br><br>**Serviço de estado**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que o conjunto de réplicas tem um quórum. O Tecido de Serviço aguarda a disponibilizância de um quórum, até um valor máximo de tempo limite (especificado pela propriedade *UpgradeReplicaCheckCheckTimeoutSec).* Se o prazo expirar, o Service Fabric procede à atualização, independentemente do quórum. Esta definição é definida como nunca (infinita) ao rolar para a frente, e 1200 segundos ao rolar para trás. |
> | UpgradeTimeoutSec |PS, VS |Um tempo de tempo (em segundos) que se aplica a toda a atualização. Se este prazo for atingido, a atualização para e *a FailAction* é ativada. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. |
> | WhatIf | PS | Os valores permitidos são **verdadeiros** e **falsos.** Apresenta o que aconteceria mediante a execução do cmdlet. O cmdlet não é executado. |

Os critérios *MaxPercentUnhealthyServices,* *MaxPercentUnhealthyPartitionsPerService*e *MaxPercentUnhealthyReplicasPerPartition* podem ser especificados por tipo de serviço para uma instância de aplicação. A definição destes parâmetros por serviço permite que uma aplicação contenha diferentes tipos de serviços com diferentes políticas de avaliação. Por exemplo, um tipo de serviço de gateway apátrida pode ter um *MaxPercentUnhealthyPartitionsPerService* que é diferente de um tipo de serviço de motor imponente para uma determinada instância de aplicação.

## <a name="sfctl-parameters"></a>Parâmetros SFCTL

Upgrades de aplicação de tecido de serviço utilizando o CLI de tecido de serviço utilizam o comando de atualização de [aplicações sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) juntamente com os seguintes parâmetros necessários e opcionais.

### <a name="required-parameters"></a>Parâmetros necessários

| Parâmetro | Descrição |
| --- | --- |
| aplicação-id  |Identificação da aplicação que está a ser atualizada. <br> Este é tipicamente o nome completo da aplicação sem o esquema URI 'fabric:' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o personagem ' ' . Por exemplo, se o nome da aplicação for 'tecido:/myapp/app1', a identidade da aplicação seria 'myapp\~app1' em 6.0+ e 'myapp/app1' em versões anteriores.|
aplicação-versão |A versão do tipo de aplicação que os alvos de atualização.|
parâmetros  |Uma lista codificada de parâmetros de aplicação a aplicar a aplicar durante a atualização da aplicação.|

### <a name="optional-parameters"></a>Parâmetros opcionais

| Parâmetro | Descrição |
| --- | --- |
padrão-serviço-política de saúde | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) codifica especificação da política de saúde utilizada por padrão para avaliar a saúde de um tipo de serviço. O mapa está vazio por defeito. |
falha-ação | Os valores permitidos são **Rollback,** **Manual**e **Inválidos.** A ação compensatória a realizar quando uma atualização *monitorizada* encontra a política de monitorização ou as violações das políticas de saúde. <br>**A reversão** especifica que a atualização voltará automaticamente para a versão pré-actualização. <br>**O manual** indica que a atualização passará para o modo de atualização *Manual Não monitorizado.* <br>**Inválido** indica que a ação de falha é inválida.|
reinício da força | Se atualizar uma configuração ou pacote de dados sem atualizar o código de serviço, o serviço só é reiniciado se a propriedade ForceRestart estiver definida para **True**. Quando a atualização estiver concluída, o Service Fabric notifica o serviço de que está disponível um novo pacote de configuração ou pacote de dados. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode reiniciar-se. |
saúde-check-retry-timeout | O tempo para voltar a tentar a avaliação de saúde quando a aplicação ou o cluster não é saudável antes da *execução da Ação de Falha.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Padrão: PT0H10M0s. |
saúde-check-duração estável | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização. É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Predefinição: PT0H2M0s. |
saúde-check-wait-duração | O tempo para esperar depois de completar um domínio de upgrade antes de aplicar políticas de saúde. É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. Padrão: 0.|
aplicativos max-unhealthy | O valor predefinido e recomendado é 0. Especifique o número máximo de aplicações implementadas (ver secção de [Saúde)](service-fabric-health-introduction.md)que podem ser insalubres antes que a aplicação seja considerada insalubre e falhe na atualização. Este parâmetro define a saúde da aplicação no nó e ajuda a detetar problemas durante a atualização. Tipicamente, as réplicas da aplicação ficam equilibradas em carga para o outro nó, o que permite que a aplicação pareça saudável, permitindo assim que a atualização prossiga. Ao especificar uma saúde rigorosa de *aplicações max-insalubres,* o Service Fabric pode detetar rapidamente um problema com o pacote de aplicação e ajudar a produzir uma atualização rápida de falhas. Representado como um número entre 0 e 100. |
mode | Os valores permitidos são **monitorizados**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Predefinição é **UnmonitoredAuto**. Consulte a secção *De parâmetros necessários* para o Estúdio Visual e PowerShell para descrições destes valores.|
replica-set-check-timeout |Medido em segundos. <br>**Serviço apátrida**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que estão disponíveis instâncias adicionais do serviço. Se a contagem de instâncias-alvo for superior a uma, o Tecido de Serviço aguarda que mais de uma instância esteja disponível, até um valor máximo de tempo-out. Este intervalo é especificado utilizando a propriedade de *tempo de verificação* de réplicas. Se o prazo expirar, o Service Fabric procede à atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias-alvo for uma, o Tecido de Serviço não espera e procede imediatamente à atualização.<br><br>**Serviço de estado**-- Dentro de um único domínio de upgrade, o Service Fabric tenta garantir que o conjunto de réplicas tem um quórum. O Tecido de Serviço aguarda a disponibilizância de um quórum, até um valor máximo de tempo limite (especificado pela propriedade de *check-timeout* de réplica). Se o prazo expirar, o Service Fabric procede à atualização, independentemente do quórum. Esta definição é definida como nunca (infinita) ao rolar para a frente, e 1200 segundos ao rolar para trás. |
política de saúde de serviço | Mapa codificado jSON com política de saúde do tipo de serviço por nome do tipo de serviço. O mapa está vazio. [Formato JSON parâmetro. .](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). O JSON para a parte "Value" contém **MaxPercentUnhealthyServices,** **MaxPercentUnhealthyPartitionsPerService**e **MaxPercentUnhealthyReplicasPerPartition**. Consulte a secção De parâmetros opcionais do Estúdio Visual e powerShell para obter descrições destes parâmetros.
tempo limite | Especifica o período de tempo limite em segundos para a operação. Padrão: 60. |
upgrade-domínio-timeout | O tempo que cada domínio de atualização tem de completar antes da *execução da Ação de Falha.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. Predefinição: P10675199DT02H48m05.4775807s. |
upgrade-timeout | O tempo que cada domínio de atualização tem de completar antes da *execução da Ação de Falha.* É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. O valor predefinido nunca é (Infinito) e deve ser personalizado adequadamente para a sua aplicação. Predefinição: P10675199DT02H48m05.4775807s.|
advertência-como-erro | Os valores permitidos são **verdadeiros** e **falsos.** O valor predefinido é **Falso**. Pode ser passado como uma bandeira. Trate os eventos de saúde de alerta para a aplicação como erros ao avaliar a saúde da aplicação durante a atualização. Por padrão, o Service Fabric não avalia os eventos de saúde de alerta como falhas (erros), pelo que a atualização pode prosseguir mesmo que haja eventos de alerta. |

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação Utilizando o Estúdio Visual](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações utilizando o Visual Studio.

[Atualizar a sua aplicação Utilizando powershell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.

[Atualizar a sua Aplicação utilizando o Tecido de Serviço CLI no Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) leva-o através de uma atualização de aplicação utilizando o Service Fabric CLI.

[Atualizar a sua aplicação utilizando o Plugin eclipse do tecido de serviço](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [Serialização](service-fabric-application-upgrade-data-serialization.md)de Dados .

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação referindo-se a [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de aplicações de [resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)
