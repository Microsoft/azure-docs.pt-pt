---
title: Tecido de serviço Azure Hospedando ciclo de vida de ativação e desativação
description: Explica o ciclo de vida da Aplicação e do ServicePackage num nó
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779605"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Tecido de serviço Azure hospedando ciclo de vida

Este artigo fornece uma visão geral dos eventos que acontecem no Azure Service Fabric quando uma aplicação é ativada num nó e vários clusters configuras usados para controlar o comportamento.

Antes de prosseguir, certifique-se de que compreende os vários conceitos e relações explicados no [Modelo de Uma aplicação em Tecido de Serviço.][a1] 

> [!NOTE]
> Neste artigo, a menos que explicitamente mencionado como algo diferente:
>
> - *Réplica* refere-se tanto a uma réplica de um serviço estatal como a um caso de um serviço apátrida.
> - *O CodePackage* é tratado como equivalente a um processo *ServiceHost* que regista um *ServiceType,* e acolhe réplicas de serviços desse *ServiceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Ativação de Aplicação/Serviço Demômpatar

O gasoduto para a ativação é o seguinte:

1. Descarregue o ApplicationPackage. Por exemplo: ApplicationManifest.xml etc.
2. Configurar ambiente para aplicação para ex: criar utilizadores etc.
3. Comece a rastrear o pedido de desativação.
4. Baixar ServicePackage. Por exemplo: ServiceManifest.xml, código, config, pacotes de dados, etc.
5. Configurar ambiente para pacote de serviço para ex: firewall de configuração, alocar portas para pontos finais, etc.
6. Comece a rastrear o ServicePackage para desativação.
7. Inicie a configuraçãoEntryPoint de CodePackages e aguarde a conclusão.
8. Iniciar MainEntryPoint de CodePackages.

### <a name="servicetype-blocklisting"></a>Blocklisting deTipologia de Serviço
**O ServiceTypeDisableFailureThreshold** determina o número de falhas (ativação, download, falhas de CodePackage) após as quais o ServiceType está programado para bloquear a lista. A primeira falha de ativação, falha no download ou falha no CodePackage irá agendar a lista de bloqueios do ServiceType. O **ServiceTypeDisableGraceInterval** config determina o intervalo de graça após o qual o ServiceType é marcado como bloqueado nesse nó. Enquanto tudo isto está a acontecer, a ativação, o download e o reinício do CodePackage estão a ser novamente julgados em paralelo. Reagem, por exemplo, que o CodePackage será programado para recomeçar depois da falha ou o Service Fabric tentará descarregar novamente os pacotes.

Quando o ServiceType estiver bloqueado, verá um erro de saúde "'System.Hosting' reportado Erro para propriedade 'ServiceTypeRegistration:ServiceType'. O ServiceType foi desativado no nó."

O ServiceType será ativado no nó novamente se alguma das seguintes coisas acontecer:
- A ativação sucede ou atinge a **activaçãoMaxFailureCount** desativação após falha.
- O download tem sucesso ou atinge as retrações **Do DeploymentMaxFailureCount** após a falha.
- Um CodePackage que se despenhou começa e regista com sucesso o ServiceType.

**ActivationMaxFailureCount** e **DeploymentMaxFailureCount** são o número máximo de tentativas que o Service Fabric fará para ativar/descarregar uma aplicação num nó, após o qual o Service Fabric permitirá a ativação do ServiceType novamente. Isto é para dar ao serviço mais uma oportunidade de ativação, que pode ter sucesso, resultando na cura automática do problema. Uma nova operação de ativação/descarregamento desencadeada pela colocação e ativação de uma réplica pode desencadear novamente o bloqueio serviceType ou pode ter sucesso.

> [!NOTE]
> Se o seu CodePackage que não regista um ServiceType estiver a falhar, então não afeta o ServiceType. Apenas o CodePackage que hospeda uma falha de réplica impactará o ServiceType.
>

### <a name="codepackage-crash"></a>Falha no CodePackage
Quando um CodePackage se despenha, o Service Fabric usa um backoff para reiniciá-lo. O backoff é independente de saber se o pacote de código registou ou não um tipo com o tempo de execução do Tecido de Serviço.

O valor de backoff é Min(RetryTime, **ActivationMaxRetryInterval),** e este valor de backoff é aumentado em quantidades constantes, lineares ou exponenciais com base na **configuração activationRetryBackoffExponentiationBase.**

- Constante: Se **ActivationRetryBackoffExponentitionBase** == 0 então RetryTime = **ActivationRetryBackoffInterval**;
- Linear: Se  **ActivationRetryBackoffExponentiationBase** == 0 então RetryTime = ContinuousFailureCount* **ActivationRetryBackoffInterval** onde ContinousFailureCount é o número de vezes que um CodePackage falha ou falha em ativar.
- Exponencial: RetryTime =**(ActivationRetryBackoffInterval** em segundos) ***(ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Pode controlar o comportamento alterando os valores. Por exemplo, se quiser várias tentativas rápidas de reinício, pode utilizar o Linear definindo **activationRetryBackoffExponentiationBase** para 0 e **ActivationRetryBackoffInterlor** a 10. Com estas definições, se um CodePackage se despenhar, o intervalo de partida será após 10 segundos. Se a embalagem continuasse a falhar, o backoff mudaria para, 20, 30, 40 segundos, e assim por diante, até que a ativação codePackage fosse bem sucedida ou o pacote de código fosse desativado. 
    
A quantidade máxima de tempo que o Tecido de Serviço recua (espera) depois de uma falha ser regida pela **ActivationMaxRetryInterval**.
    
Se o seu CodePackage falhar e voltar a subir, tem de ficar acordado para **o CodePackageContinuousExitFailureResetInterval** para o Service Fabric considerá-lo saudável, altura em que substituirá o relatório de saúde de Erro anterior como OK e redefinirá o ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage não registar OTipo de Serviço
Se um CodePackage permanecer vivo e se espera que registe um ServiceType connosco, mas não o faz, o Service Fabric gerará um aviso HealthReport após **o ServiceTypeRegistrationTimeout,** dizendo que o ServiceType não foi registado dentro do tempo esperado.

### <a name="activation-failure"></a>Falha de ativação
O Tecido de Serviço utiliza sempre um back-off linear (o mesmo que o codepackage crash) quando encontra erro durante a ativação. Isto significa que a operação de ativação desistirá depois (0 + 10 + 20 + 30 + 40) = 100 seg (a primeira repetição é imediata). Após esta ativação não é novamente experimentada.
    
O backoff de ativação max pode ser **ActivationMaxRetryInterval** e retry **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Falha no download
O Tecido de Serviço utiliza sempre um back-off linear quando encontra erro durante o download. Isto significa que a operação de ativação desistirá depois (0+ 10 + 20 + 30 + 40) = 100 seg (a primeira repetição é imediata). Depois disso, o download não é novamente experimentado. O back-off linear para download é igual a ContinuousFailureCount**_DeploymentRetryBackoffInterval_* e pode fazer o máximo de back-off para **DeploymentMaxRetryInterval**. Tal como as ativações, a operação de descarregamento pode voltar a funcionar para **activationMaxFailureCount**.

> [!NOTE]
> Antes de alterar estas definições, aqui estão alguns exemplos que deve ter em mente.

* Se o CodePackage continuar a falhar e a recuar, o ServiceType será desativado. Mas se a configuração de ativação for tal que tenha um reinício rápido, então o CodePackage pode surgir algumas vezes antes do ServiceType ser realmente bloqueado. Para ex: assuma que o seu CodePackage aparece, regista o ServiceType com o Service Fabric e depois falha. Nesse caso, uma vez que o Hosting recebe um registo de tipo, o período **ServiceTypeDisableGraceInterval** é cancelado. E isto pode repetir-se até que o codepackage recue para um valor superior ao  **ServiceTypeDisableGraceInterval** e, em seguida, o ServiceType será bloqueado no nó. Pode demorar mais do que espera ver o ServiceType bloqueado.

* Em caso de ativações, quando o sistema de tecido de serviço precisa de colocar uma réplica num nó, o RA (ReconfigurationAgent) pede ao subssistema de hospedagem para ativar a aplicação e recauchutar o pedido de ativação a cada 15 segundos (regido pela configuração de configuração **RAPMessAgeInterval).** Para que o Service Fabric saiba que o ServiceType foi bloqueado, a operação de ativação no alojamento tem de viver por um período mais longo do que o intervalo de retrip e o **ServiceTypeDisableGraceInterval**. Por exemplo: assuma que o cluster tem **ActivationMaxFailureCount** definido para 5 e **ActivationRetryBackoffInterval** definido para 1 segundo. Isto significa que a operação de ativação desistirá depois (0 + 1 + 2 + 3 + 4) = 10 segundos (lembre-se que a primeira reagem é imediata) e depois que o Hospedeiro desiste de voltar a tentar. Neste caso, a operação de ativação ficará concluída e não voltará a funcionar após 15 segundos. Isto acontece porque o Tecido de Serviço esgotou todas as retróscas permitidas em 15 segundos. Assim, cada repetição da ReconfigurationAgent cria uma nova operação de ativação no subsistema de hospedagem e o padrão continuará a repetir-se. Como resultado, o ServiceType nunca será bloqueado no nó. Uma vez que o ServiceType não será bloqueado no nó, a Réplica não será movida e experimentada num nó diferente.
> 

## <a name="deactivation"></a>Desativação

Quando um ServicePackage é ativado num nó, é rastreado para desativação. 

A desativação funciona de duas formas:

- Periodicamente: Em todas as **DesactivaçõesScanInterval,** verifica o ServicePackages que nunca acolheu uma Réplica e marca-as como candidatas à desativação.
- ReplicaClose: Se uma réplica estiver fechada, o Deactivador obtém um DecrementUsageCount. Se a contagem for para 0, significa que o ServicePackage não está a hospedar nenhuma Réplica e, portanto, é um candidato à desativação.

 Com base no modo de ativação [Exclusivo/Partilhado,][a2]os candidatos à desativação são agendados após a **DeactivationGraceInterval** para SharedMode e após o **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode. Se uma nova colocação de réplica chegar entre este momento, a desativação é cancelada.

### <a name="periodically"></a>Periodicamente:
Vamos discutir alguns exemplos de desativação periódica:

Exemplo 1: Digamos que o Desactivador faz uma varredura no Time T (**DesactivaçãoScanInterval).** A próxima tomografia será no 2T. Assuma que uma ativação de ServicePackage aconteceu na T+1. Este ServicePackage não está a alojar uma Réplica e, por isso, tem de ser desativado. Para que o ServicePackage seja um candidato à desativação, tem de estar no estado de não réplica durante o tempo T pelo menos. Ou seja, será elegível para desativação em 2T+1. Então, a varredura no 2T não vai encontrar este ServicePackage como um candidato para desativação. O próximo ciclo de desativação 3T irá agendar este ServicePackage para desativação porque agora não está em estado de Replica há tempo T.  

Exemplo 2: Digamos que um ServicePackage é ativado no momento T-1 e o Desactivador faz uma varredura em T. O ServicePackage não acolhe uma réplica. Em seguida, na próxima digitalização 2T este ServicePackage será encontrado como um candidato para desativação e, portanto, será agendado para desativação.  

Exemplo 3: Digamos que um ServicePackage é ativado no T-1 e o Desactivador faz uma verificação em T. O ServicePackage ainda não acolhe uma réplica. Agora no T+1 uma réplica é colocada, ou seja, O anfitrião recebe um IncrementUsageCount, o que significa que uma réplica é criada. Agora, no dia 2T, este ServicePackage não será programado para desativação. Como contém uma réplica, a desativação irá mover-se para a lógica ReplicaClose explicada abaixo.

Exemplo 4: Digamos que o seu ServicePackage é grande, digamos 10 GB, e pode levar algum tempo para descarregar no nó. Uma vez ativada uma aplicação, o Desactivador rastreia o seu ciclo de vida. Agora, se tiver o **desactivationScanInterval** config definido para um pequeno valor, pode encontrar problemas em que o seu ServicePackage não está a ter tempo para ativar no nó porque todo o tempo foi para o download. Para ultrapassar o problema, pode [pré-descarregar o ServicePackage no nó][p1] ou aumentar a **DeactivationScanInterval**. 

> [!NOTE]
> Um ServicePackage pode ser desativado em qualquer lugar entre (**DesactivaçãoScanInterval** a 2 **_DesactivaçõesScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Réplica Perto:
A desativação mantém a contagem de réplicas que um ServicePackage detém. Se um ServicePackage estiver a segurar uma réplica e a réplica estiver fechada/abaixo, o Hosting recebe um DecrementUsageCount. Quando uma réplica é aberta, o hosting recebe um IncrementUsageCount. O Decrement significa que o ServicePackage está agora a alojar uma réplica a menos e quando a contagem cai para 0, então o ServicePackage está programado para desativação e a hora após a qual será desativada é **DeactivationGraceInterval** / **ExclusiveModeDeactivationInterceInterval**. 

Para ex: digamos que um Decrement acontece na T e o ServicePackage está programado para desativar em 2T+X(**DeactivationGraceInterval** / **ExclusiveModeDeactivationInterceInterval.** Se durante este tempo o Hosting obtém um IncrementUsage que significa que uma réplica é criada, então a desativação é cancelada.

> [!NOTE]
> O que significam estas definições config?
**DesativaçãoGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: O tempo dado a um ServicePackage para acolher novamente outra Réplica uma vez que tenha hospedado qualquer Réplica. 
**DesativaçãoScanInterval**: O tempo mínimo dado ao ServicePackage para acolher uma réplica se nunca tiver hospedado qualquer Réplica i.e se não for usado.
>

### <a name="ctrlc"></a>Ctrl+C
Uma vez que um ServicePackage passa a **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** e ainda não está a hospedar uma Réplica, a desativação não é cancelável. O CodePackage é emitido um manipulador Ctrl+C que significa que agora o gasoduto de desativação tem de passar para derrubar o processo. Durante este tempo, se uma nova réplica para o mesmo ServicePackage estiver a tentar ser colocada, falhará porque não podemos transitar da desativação para a Ativação.

## <a name="cluster-configs"></a>Cluster Configs

Configs com incumprimentos que impactam a ativação/decativação.

### <a name="servicetype"></a>ServiceType
**ServiçoTypeDisableFailureThreshold**: Predefinição 1. O limiar para a contagem de avarias após o qual FM (FailoverManager) é notificado para desativar o tipo de serviço nesse nó e tentar um nó diferente para colocação.
**ServiceTypeDisableGraceInterval**: Padrão 30 seg. Intervalo de tempo após o qual o tipo de serviço pode ser desativado.
**ServiçoTypeRegistrationTimeout**: Padrão 300 seg. O tempo limite para o ServiceType registar-se com o Service Fabric.

### <a name="activation"></a>Ativação
**ActivationRetryBackoffInterval**: Padrão 10 seg. Intervalo de recuo em todas as falhas de ativação.
**ActivationMaxFailureCount**: Padrão 20. Contagem máxima para qual o sistema vai voltar a tentar a ativação falhada antes de desistir. 
**ActivationRetryBackoffExponentiationBase**: Predefinição 1.5.
**ActivationMaxRetryInterval**: Padrão 3600 seg. Máximo de recuo para ativação em falhas.
**CodePackageContinuousExitFailureResetInterval**: Padrão 300 seg. O tempo limite para repor a contagem contínua de falha de saída para codepackage.

### <a name="download"></a>Download
**ImplementaçãoRetryBackoffInterval**: Padrão 10. Intervalo de retenção para a falha de implantação.
**ImplementaçãoMaxRetryInterval**: Padrão 3600 seg. Max recua para a implantação de falhas.
**ImplementaçãoMaxFailureCount**: Padrão 20. A implementação da aplicação será novamente julgada para o DeploymentMaxFailureCount vezes antes de falhar a implementação dessa aplicação no nó.

### <a name="deactivation"></a>Desativação
**DesactivaçãoScanInterval**: Padrão 600 seg. Tempo mínimo dado ao ServicePackage para acolher uma Réplica se nunca tiver hospedado qualquer Réplica, ou seja, se não for usado.
**DesactivaçãoGraceInterval**: Padrão 60 seg. O tempo dado a um ServicePackage para acolher novamente outra Réplica uma vez que tenha hospedado qualquer Réplica em caso de modelo de Processo **Partilhado.**
**ExclusivoModeDeactivationGraceInterval**: Padrão 1 seg. O tempo dado a um ServicePackage para acolher novamente outra Réplica uma vez que tenha hospedado qualquer Réplica em caso de modelo de Processo **Exclusivo.**

## <a name="next-steps"></a>Passos seguintes
[Embale uma aplicação][a3] e prepare-a para ser implantada.

[Implementar e remover aplicações][a4]. Este artigo descreve como usar o PowerShell para gerir instâncias de aplicação.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
