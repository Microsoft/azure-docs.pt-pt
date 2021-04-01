---
title: Azure Service Fabric hospeda ciclo de vida de ativação e desativação
description: Saiba mais sobre o ciclo de vida de uma aplicação e um ServicePackage num nó.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831827"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Tecido de serviço Azure hospedando ciclo de vida

Este artigo fornece uma visão geral dos eventos que acontecem no Azure Service Fabric quando uma aplicação é ativada num nó. Explica várias configurações de cluster que controlam o comportamento.

Antes de continuar, certifique-se de que compreende os conceitos e relacionamentos explicados no [Modelo de Uma aplicação em Tecido de Serviço.][a1] 

> [!NOTE]
> Este artigo usa alguma terminologia de formas especializadas. Salvo indicação em contrário:
>
> - *Réplica* refere-se tanto a uma réplica de um serviço estatal como a um caso de um serviço apátrida.
> - *O CodePackage* é tratado como equivalente a um processo ServiceHost que regista um ServiceType. Acolhe réplicas de serviços daquele ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Ativar um Pacote de Aplicações ou Pacote de Serviço

Para ativar uma Embalagem de Aplicação ou Pacote de Serviço:

1. Descarregue o ApplicationPackage (por exemplo, *ApplicationManifest.xml).*
2. Crie um ambiente para a aplicação. Os seus passos incluem, por exemplo, a criação de utilizadores.
3. Comece a rastrear o pedido de desativação.
4. Descarregue o ServicePackage (por exemplo, *ServiceManifest.xml*, código, ficheiros de configuração e pacotes de dados).
5. Crie um ambiente para o ServicePackage. Os seus passos incluem, por exemplo, a criação de uma firewall e a atribuição de portas para pontos finais.
6. Comece a rastrear o ServicePackage para desativação.
7. Inicie o SetupEntryPoint para as CodePackages e aguarde que termine.
8. Inicie o MainEntryPoint para as CodePackages.

### <a name="servicetype-blocklisting"></a>Bloqueio de tipo de serviço
`ServiceTypeDisableFailureThreshold` determina o número de falhas permitidas de ativação, descarregamento e CodePackage. Após o limiar ser atingido, o ServiceType está programado para a lista de bloqueio. A primeira falha de ativação, falha no download ou falha no CodePackage programa a lista de bloqueios do ServiceType. 

A `ServiceTypeDisableGraceInterval` configuração determina o intervalo de graça antes de o ServiceType estar bloqueado no nó. À medida que este processo se desenrola, a ativação, o download e o reinício do CodePackage são novamente julgados em paralelo. Reagem significa, por exemplo, que o CodePackage está programado para recomeçar depois da falha ou o Service Fabric tentará descarregar novamente os pacotes.

Quando o ServiceType está bloqueado, vê-se um erro de saúde: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

O ServiceType está novamente ativado no nó se algum dos seguintes eventos acontecer:
- A ativação tem sucesso. Ou se falhar, atinge o `ActivationMaxFailureCount` número máximo de retrações.
- O download tem sucesso. Ou se falhar, atinge o `DeploymentMaxFailureCount` número máximo de retrações.
- Um CodePackage que se despenhou começa e regista com sucesso o ServiceType.

`ActivationMaxFailureCount` e `DeploymentMaxFailureCount` são o número máximo de tentativas que o Service Fabric fará para ativar ou descarregar uma aplicação num nó. Depois de atingido o máximo, o Tecido de Serviço permite novamente a ativação do ServiceType. 

Estes limiares dão ao serviço mais uma oportunidade de ativação. Se a ativação for bem sucedida, a questão é automaticamente curada. 

Uma réplica recém-colocada ou ativada pode desencadear uma nova operação de ativação ou descarregamento. Esta ação terá sucesso ou ativará novamente a lista de bloqueios do ServiceType.

> [!NOTE]
> Um CodePackage que não regista um ServiceType não afeta o ServiceType. Apenas um CodePackage que se despenha que acolhe uma réplica afeta o ServiceType.
>

### <a name="codepackage-crash"></a>Falha no CodePackage
Quando um CodePackage se despenha, o Service Fabric usa um backoff para reiniciá-lo. O backoff é aplicado independentemente de o CodePackage ter registado um tipo com o tempo de execução do Tecido de Serviço.

O valor de backoff é `Min(RetryTime, ActivationMaxRetryInterval)` . O valor é incrementado em quantidades constantes, lineares ou exponenais com base na definição de `ActivationRetryBackoffExponentiationBase` configuração:

- **Constante:** `ActivationRetryBackoffExponentiationBase == 0` Se, então `RetryTime = ActivationRetryBackoffInterval` . .
- **Linear**:  `ActivationRetryBackoffExponentiationBase == 0` Se, em `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` seguida, onde `ContinuousFailureCount` está o número de vezes que um CodePackage se despenha ou não ativa.
- **Exponencial:** `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
Pode controlar o comportamento alterando os valores. Por exemplo, se desejar várias tentativas rápidas de reinício, pode utilizar quantidades lineares definindo `ActivationRetryBackoffExponentiationBase` `0` e definindo para `ActivationRetryBackoffInterval` `10` . Assim, se um CodePackage falhar, o intervalo de partida será após 10 segundos. Se a embalagem continuar a falhar, o backoff muda para 20 segundos, 30 segundos, 40 segundos, e assim por diante, até que a ativação codePackage tenha sucesso ou o CodePackage seja desativado. 
    
O tempo máximo que o Service Fabric recua (isto é, espera) depois de uma falha é regida por `ActivationMaxRetryInterval` .
    
Se o seu CodePackage falhar e voltar a subir, tem de ficar acordado durante o período de tempo especificado por `CodePackageContinuousExitFailureResetInterval` . Após este intervalo, o Service Fabric considera o CodePackage saudável. O Tecido de Serviço substitui então o relatório de saúde de erro prévio como OK e repõe o `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage não registar OTipo de Serviço
Se um CodePackage permanecer em funcionamento e se espera que registe um ServiceType mas não o faça, o Service Fabric gera um relatório de saúde de aviso após o `ServiceTypeRegistrationTimeout` . O relatório indica que o ServiceType não foi registado dentro do tempo esperado.

### <a name="activation-failure"></a>Falha de ativação
Quando o Tecido de Serviço encontra um erro durante a ativação, utiliza sempre um backoff linear, tal como acontece com uma falha de CodePackage. A operação de ativação desiste após as retrações nestes intervalos: (0 + 10 + 20 + 30 + 40) = 100 segundos. (A primeira repetição é imediata.) Depois desta sequência, a ativação não é novamente tentada.
    
O devolução máximo de ativação pode ser `ActivationMaxRetryInterval` . A repetição pode `ActivationMaxFailureCount` ser.

### <a name="download-failure"></a>Falha no download
O Tecido de Serviço utiliza sempre um backoff linear quando encontra um erro durante o download. A operação de ativação desiste após as retrações nestes intervalos: (0 + 10 + 20 + 30 + 40) = 100 segundos. (A primeira repetição é imediata.) Depois desta sequência, o download não é novamente experimentado. 

O backoff linear para um download é igual a `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . O backoff máximo pode ser `DeploymentMaxRetryInterval` . Tal como as ativações, as operações de descarregamento podem voltar a tentar o `ActivationMaxFailureCount` limite.

> [!NOTE]
> Antes de alterar estas definições, tenha em mente os seguintes exemplos:
>
>* Se o CodePackage continuar a bater e a recuar, o ServiceType será desativado. Mas se a configuração de ativação tiver um reinício rápido, então o CodePackage pode surgir algumas vezes antes do ServiceType ser realmente bloqueado. 
>
>    Por exemplo, digamos que o seu CodePackage aparece, regista o ServiceType com o Service Fabric e depois falha. Nesse caso, após o alojamento receber um registo de tipo, o `ServiceTypeDisableGraceInterval` período é cancelado. Este processo pode repetir-se até que o codepackage recue para um valor superior ao `ServiceTypeDisableGraceInterval` período. Em seguida, o ServiceType será bloqueado no nó. A lista de bloqueios do ServiceType pode demorar mais tempo do que se espera.
>
>* Em caso de ativações, quando um sistema de Tecido de Serviço necessita de colocar uma Réplica num nó, o agente de reconfiguração pede ao subsistema de hospedagem para ativar a aplicação. Retrição o pedido de ativação a cada 15 segundos. (A duração rege-se pela definição de `RAPMessageRetryInterval` configuração.) O Tecido de Serviço não pode saber que o ServiceType foi bloqueado a menos que a operação de ativação no alojamento esteja em alta por um período mais longo do que o intervalo de repetição e o `ServiceTypeDisableGraceInterval` . 
>
>    Por exemplo, assuma que o cluster `ActivationMaxFailureCount` está definido para 5, e `ActivationRetryBackoffInterval` está definido para 1 segundo. Neste caso, a operação de ativação desiste após intervalos de (0 + 1 + 2 + 3 + 4) = 10 segundos. (A primeira repetição é imediata.) Após esta sequência, o hospedeiro desiste de voltar a tentar. A operação de ativação termina e não voltará a funcionar após 15 segundos. 
>
>    O Tecido de Serviço esgotou todas as retróscas permitidas em 15 segundos. Assim, cada repetição do agente de reconfiguração cria uma nova operação de ativação no subsistema de hospedagem, e o padrão continua a repetir-se. Como resultado, o ServiceType nunca está bloqueado no nó. Como o ServiceType não será bloqueado no nó, a Réplica não será movida e experimentada num nó diferente.
> 

## <a name="deactivation"></a>Desativação

Quando um ServicePackage é ativado num nó, é rastreado para desativação. A desativação funciona de duas formas:

- **Desativação periódica**: Em cada `DeactivationScanInterval` um, o sistema verifica os pacotes de serviço que *nunca* acolheram uma Réplica e os marca como candidatos à desativação.
- **ReplicaClose desativação**: Se uma réplica estiver fechada, o desactivador obtém um `DecrementUsageCount` . A contagem é de 0 quando o ServicePackage não acolhe nenhuma réplica, por isso o ServicePackage é um candidato à desativação.

O modo de ativação determina quando os candidatos estão programados para desativação. Em modo partilhado, os candidatos à desativação são agendados após o `DeactivationGraceInterval` . Em modo exclusivo, estão agendados após o `ExclusiveModeDeactivationGraceInterval` . Se uma nova colocação de réplica chegar entre estes tempos, a desativação é cancelada. 

Para obter mais informações, consulte [o modo Exclusivo e o modo partilhado.][a2]

### <a name="periodic-deactivation"></a>Desativação periódica
Aqui estão alguns exemplos de desativação periódica:

* **Exemplo 1**: Digamos que o desactivador inicia uma varredura no momento T (o `DeactivationScanInterval` ). A próxima tomografia será no 2T. Assuma que uma ativação servicePackage aconteceu em T +1. Este ServicePackage não acolhe uma réplica, por isso tem de ser desativada. 

    Para ser candidato à desativação, o ServicePackage não necessita de hospedar nenhuma Réplica durante pelo menos o tempo T. Será elegível para desativação em 2T+1. Assim, a varredura no 2T não identificará este ServicePackage como um candidato à desativação. 

    O próximo ciclo de desativação, 3T, irá agendar este ServicePackage para desativação porque agora o pacote está em estado de não-replicação durante o tempo T.  

* **Exemplo 2**: Digamos que um ServicePackage é ativado no momento T - 1, e o desactivador inicia uma varredura em T. O ServicePackage não acolhe uma réplica. Na próxima digitalização, 2T, o ServicePackage será identificado como um candidato à desativação, pelo que será agendado para desativação.  

* **Exemplo 3**: Digamos que um ServicePackage é ativado em T – 1, e o desactivador inicia uma varredura em T. O ServicePackage ainda não acolhe uma réplica. Agora no T + 1, uma réplica é colocada. Ou seja, hospedar obtém uma `IncrementUsageCount` , o que significa que uma réplica é criada. 

    No dia 2T, este ServicePackage não será programado para desativação. Como o pacote contém uma Réplica, a desativação seguirá a lógica ReplicaClose, como explica a próxima secção deste artigo.

* **Exemplo 4**: Digamos que o seu ServicePackage é de 10 GB. Como o pacote é grande, leva tempo para descarregar no nó. Quando uma aplicação é ativada, o desactivador acompanha o seu ciclo de vida. Se o `DeactivationScanInterval` valor for definido para um pequeno valor, o seu ServicePackage pode não ter tempo para ativar no nó devido ao tempo que demorou a descarregar. Para ultrapassar este problema, pode [descarregar o ServicePackage com antecedência no nó][p1] ou aumentar o `DeactivationScanInterval` . 

> [!NOTE]
> Um ServicePackage pode ser desativado em qualquer lugar entre `DeactivationScanInterval` (a 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose desativação

> [!NOTE]
> Esta secção refere-se às seguintes definições de configuração:
> - **DesativaçãoGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: O tempo dado a uma ServicePackage para acolher outra Réplica se já tiver hospedado qualquer Réplica. 
> - **DesactivaçãoScanInterval**: O tempo mínimo dado a uma ServicePackage para hospedar uma Réplica se *nunca* tiver hospedado qualquer Réplica, isto é, se não tiver sido utilizada.
>

O sistema mantém a contagem das réplicas que um ServicePackage detém. Se um ServicePackage estiver a segurar uma réplica e a réplica estiver fechada ou abaixo, o hospedeiro recebe um `DecrementUsageCount` . Quando uma réplica é aberta, o hospedeiro recebe um `IncrementUsageCount` . 

O decremento indica que o número de réplicas que o ServicePackage está a hospedar foi reduzido por uma réplica. Quando a contagem cai para 0, o ServicePackage está programado para desativação. O tempo após o qual será desativado é `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Por exemplo, digamos que um decrépito acontece em T, e o ServicePackage está programado para desativar a 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). Durante este tempo, se o hospedeiro receber uma `IncrementUsage` réplica porque é criada uma réplica, a desativação é cancelada.

### <a name="ctrl--c"></a>Ctrl+C
Se um ServicePackage passar `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` o e ainda não estiver a hospedar uma Réplica, a desativação não pode ser cancelada. CodePackages recebem um manipulador Ctrl + C. Então agora o oleoduto de desativação tem que terminar para derrubar o processo. 

Durante este tempo, se uma nova Réplica para o mesmo ServicePackage estiver a tentar ser colocada, falhará porque o processo não pode passar da desativação para a ativação.

## <a name="cluster-configurations"></a>Configurações de clusters

Esta secção lista configurações que têm predefinições que afetam a ativação e desativação.

### <a name="servicetype"></a>ServiceType
- **ServiçoTypeDisableFailureThreshold**: Predefinição: 1. Limiar para a contagem de falhas; após este limiar ser atingido, o FailoverManager é notificado para desativar o tipo de serviço no nó e tentar um nó diferente para colocação.
- **ServiceTypeDisableGraceInterval**: Padrão: 30 segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desativado.
- **ServiçoTipRegistrationTimeout**: Predefinição: 300 segundos. Tempo limite para o ServiceType registar-se com o Service Fabric.

### <a name="activation"></a>Ativação
- **ActivationRetryBackoffInterval**: Padrão: 10 segundos. Intervalo de recuo para cada falha de ativação.
- **ActivationMaxFailureCount**: Padrão: 20. Contagem máxima para a qual o sistema tentará novamente uma ativação falhada antes de desistir. 
- **ActivationRetryBackoffExponentiationBase**: Padrão: 1.5.
- **ActivationMaxRetryInterval**: Padrão: 3.600 segundos. Intervalo máximo de retenção para ativação após falhas.
- **CodePackageContinuousExitFailureResetInterval:** Padrão: 300 segundos. Intervalo de tempo para repor a contagem contínua de falha de saída para o CodePackage.

### <a name="download"></a>Download
- **ImplementaçãoRetryBackoffInterval**: Padrão: 10. Intervalo de recuo para a falha de implantação.
- **ImplementaçãoMaxRetryInterval:** Padrão: 3.600 segundos. Intervalo máximo de recuo para a colocação após falhas.
- **ImplementaçãoMaxFailureCount**: Padrão: 20. A implementação da aplicação será novamente julgada por `DeploymentMaxFailureCount` tempos antes de falhar a implementação dessa aplicação no nó.

### <a name="deactivation"></a>Desativação
- **DesactivaçãoScanInterval:** Padrão: 600 segundos. Tempo mínimo dado ao ServicePackage para hospedar uma Réplica se nunca tiver hospedado qualquer Réplica (isto é, se não for utilizada).
- **DesactivaçãoGraceInterval:** Padrão: 60 segundos. Num modelo de processo *partilhado,* o tempo dado a um ServicePackage para voltar a acolher outra Réplica depois de já ter acolhido qualquer Réplica.
- **ExclusiveModeDeactivationGraceInterval**: Padrão: 1 segundo. Num modelo de processo *exclusivo,* o tempo dado a um ServicePackage para voltar a acolher outra Réplica depois de já ter acolhido qualquer Réplica.

## <a name="next-steps"></a>Passos seguintes

- [Embale uma aplicação][a3] e prepare-a para ser implantada.
- [Implementar e remover aplicações][a4] no PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
