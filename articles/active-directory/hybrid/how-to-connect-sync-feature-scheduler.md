---
title: 'Sincronização do Azure AD Connect: Scheduler | Documentos da Microsoft'
description: Este tópico descreve a funcionalidade de agendador interno do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d5f4dec48d81b032de293bb6c68ad62ac48d475
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193063"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: Scheduler
Este tópico descreve o agendador interno na sincronização do Azure AD Connect (também conhecido como motor de sincronização).

Esta funcionalidade foi introduzida com compilação 1.1.105.0 (lançada em Fevereiro de 2016).

## <a name="overview"></a>Descrição geral
Sincronização do Azure AD Connect sincronizar as alterações que ocorrem no seu diretório no local através de um agendador. Existem dois processos de agendador, uma para a sincronização de palavra-passe e outro para as tarefas de sincronização e a manutenção de objeto/atributo. Este tópico abrange o último.

Nas versões anteriores, o agendador para objetos e atributos foi externo para o motor de sincronização. Agendador de tarefas do Windows ou um serviço separado do Windows-utilizado para acionar o processo de sincronização. O agendador é com as 1.1 versões incorporada para a sincronização do motor e permitir uma personalização. A frequência de sincronização do novo padrão é 30 minutos.

O scheduler é responsável por duas tarefas:

* **Ciclo de sincronização**. O processo para importar, sincronizar e exportar as alterações.
* **Tarefas de manutenção**. Renove chaves e certificados para a reposição de palavra-passe e o serviço de registo de dispositivos (DRS). Remover o antigas entradas no registo de operações.

O agendador em si está sempre em execução, mas pode ser configurado para executar apenas uma ou nenhuma destas tarefas. Por exemplo, se precisa ter seu próprio processo de ciclo de sincronização, pode desativar esta tarefa no agendador, mas continua a executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do Programador
Para ver as definições de configuração atual, vá para o PowerShell e execute `Get-ADSyncScheduler`. Ele mostra algo semelhante a esta imagem:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Se vir **o comando de sincronização ou o cmdlet não está disponível** quando executar este cmdlet, em seguida, o módulo do PowerShell não está carregado. Esse problema pode ocorrer se executar o Azure AD Connect num controlador de domínio ou num servidor com níveis de restrição de PowerShell mais altos do que as configurações padrão. Se vir este erro, em seguida, execute `Import-Module ADSync` para disponibilizar o cmdlet.

* **AllowedSyncCycleInterval**. O intervalo de tempo mais curto entre os ciclos de sincronização permitido pelo Azure AD. Não é possível sincronizar com mais frequência do que esta definição e continuará a ser suportado.
* **CurrentlyEffectiveSyncCycleInterval**. A agenda atualmente em vigor. Ele tem o mesmo valor que CustomizedSyncInterval (se definir) se não for mais freqüente que AllowedSyncInterval. Se usar uma compilação antes 1.1.281 e alterar CustomizedSyncCycleInterval, esta alteração entrar em vigor após o próximo ciclo de sincronização. De compilação 1.1.281 a alteração tem efeito imediato.
* **CustomizedSyncCycleInterval**. Se pretender que o scheduler para executar a qualquer outra frequência que não o predefinido de 30 minutos, em seguida, configura esta definição. Na imagem acima, o scheduler foi definido para ser executado a cada hora em vez disso. Se definir esta definição para um valor inferior a AllowedSyncInterval, em seguida, a última é usada.
* **NextSyncCyclePolicyType**. O Delta ou inicial. Define se a próxima execução deve apenas alterações de delta de processo, ou se a próxima execução deve fazer um completo importar e sincronizar. A segunda opção seria Reprocessar também quaisquer regras novas ou alteradas.
* **NextSyncCycleStartTimeInUTC**. Próxima vez que o scheduler inicia o próximo ciclo de sincronização.
* **PurgeRunHistoryInterval**. O tempo de registos de operações devem ser mantidos. Estes registos podem ser revistos no Gestor do serviço de sincronização. A predefinição é manter estes registos de 7 dias.
* **SyncCycleEnabled**. Indica se o scheduler está a executar a importação, sincronização e processos de exportação como parte da operação.
* **MaintenanceEnabled**. Mostra se o processo de manutenção está ativado. Ele atualiza as certificados/chaves e remove o registo de operações.
* **StagingModeEnabled**. Mostra se [modo de teste](how-to-connect-sync-staging-server.md) está ativada. Se esta definição estiver ativada, em seguida, suprime as exportações a execução, mas continua a executar a importação e sincronização.
* **SchedulerSuspended**. Definido pelo Connect durante uma atualização para bloquear temporariamente o agendador de execução.

Pode alterar algumas destas definições com `Set-ADSyncScheduler`. Podem ser modificados os seguintes parâmetros:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Em compilações anteriores do Azure AD Connect **isStagingModeEnabled** foi exposta no conjunto ADSyncScheduler. É **não suportado** para definir esta propriedade. A propriedade **SchedulerSuspended** só deve ser modificado por Connect. É **não suportado** definir isso diretamente com o PowerShell.

A configuração do programador é armazenada no Azure AD. Se tiver um servidor de teste, qualquer alteração no servidor primário também afeta o servidor de preparação (exceto IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, segundos de mm - minutos, ss-

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Altera o scheduler para executar em três horas.

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Alterações de alterar o agendador para ser executada diariamente.

### <a name="disable-the-scheduler"></a>Desativar o scheduler  
Se precisar de efetuar alterações de configuração, em seguida, pretender desativar o scheduler. Por exemplo, quando [configurar a filtragem](how-to-connect-sync-configure-filtering.md) ou [efetuar alterações às regras de sincronização](how-to-connect-sync-change-the-configuration.md).

Para desativar o scheduler, executar `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desativar o scheduler](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Quando tiver feito as alterações, não se esqueça de ativar o agendador novamente com `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Iniciar o agendador
O agendador é por predefinição, executar a cada 30 minutos. Em alguns casos, pode querer executar um ciclo de sincronização entre os ciclos de agendada ou tiver de executar um tipo diferente.

**Ciclo de sincronização delta**  
Um ciclo de sincronização delta inclui os seguintes passos:

* Importação delta em todos os conectores
* Sincronização delta em todos os conectores
* Exportação em todos os conectores

É possível que tenha uma alteração urgente que têm de ser sincronizada imediatamente, o que é o motivo pelo qual precisa executar um ciclo manualmente. Se tiver de executar manualmente um ciclo, em seguida, da execução do PowerShell `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo de sincronização completa**  
Se efetuar uma das seguintes alterações de configuração, tem de executar um ciclo de sincronização completa (também conhecido como Inicial):

* Adicionar mais atributos a serem importados a partir de um diretório de origem ou de objetos
* Efetuou alterações para as regras de sincronização
* Alterado [filtragem](how-to-connect-sync-configure-filtering.md) , para um número diferente de objetos deve ser incluído

Se tiver efetuado uma dessas modificações, em seguida, terá de executar um ciclo de sincronização completa, para que o motor de sincronização tem a oportunidade de reconsolidate os espaços de conector. Um ciclo de sincronização completa inclui os seguintes passos:

* Importação completa em todos os conectores
* Sincronização completa em todos os conectores
* Exportação em todos os conectores

Para iniciar um ciclo de sincronização completa, execute `Start-ADSyncSyncCycle -PolicyType Initial` de uma linha de comandos do PowerShell. Este comando inicia um ciclo de sincronização completa.

## <a name="stop-the-scheduler"></a>Parar o programador
Se o scheduler está a ser executado um ciclo de sincronização, poderá ter interrompê-lo. Por exemplo, se iniciar o Assistente de instalação e obter este erro:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Quando um ciclo de sincronização está em execução, não é possível efetuar alterações de configuração. Poderia esperar até que o programador concluiu o processo, mas também pode impedi-lo para que possa tomar as suas alterações imediatamente. Interromper o ciclo atual não é perigoso e as alterações pendentes são processados com a próxima execução.

1. Comece por indicar o seu ciclo de atual com o cmdlet do PowerShell de paragem do programador `Stop-ADSyncSyncCycle`.
2. Se usar uma compilação antes 1.1.281, parar, em seguida, o agendador não impede que o conector atual da sua tarefa atual. Para forçar o conector para parar, efetue as seguintes ações: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Inicie **serviço de sincronização** do menu Iniciar. Aceda a **conectores**, realce o conector com o estado **em execução**e selecione **parar** das ações.

O agendador ainda está ativo e começa novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Scheduler personalizado
Os cmdlets documentados nesta secção só estão disponíveis na compilação [1.1.130.0](reference-connect-version-history.md#111300) e mais tarde.

Se o agendador interno não satisfaz os requisitos, em seguida, pode agendar os conectores com o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Pode iniciar um perfil para um conector desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a utilizar para [nomes de conector](how-to-connect-sync-service-manager-ui-connectors.md) e [nomes de perfil de execução](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontradas no [da IU do Gestor do serviço de sincronização](how-to-connect-sync-service-manager-ui.md).

![Invocar o perfil de execução](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é síncrono, ou seja, ela não retorna controle até que o conector seja concluída a operação, seja com êxito ou com um erro.

Quando agendar os conectores, a recomendação é agendá-las na seguinte ordem:

1. (Completo/Delta) Importar a partir de diretórios no local, como o Active Directory
2. (Completo/Delta) Importação do Azure AD
3. (Completo/Delta) Sincronização de diretórios no local, como o Active Directory
4. (Completo/Delta) Sincronização do Azure AD
5. Exportar para o Azure AD
6. Exportar para diretórios no local, como o Active Directory

Esta ordem é como o agendador interno é executado os conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Também pode monitorizar o motor de sincronização para ver se ele está ocupado ou inativo. Este cmdlet devolve um resultado vazio se o motor de sincronização está inativo e não está em execução de um conector. Se um conector está em execução, ele retorna o nome do conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado de execução do conector](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um Estado em que o motor de sincronização está ocioso. A segunda linha quando o Azure AD Connector esteja em execução.

## <a name="scheduler-and-installation-wizard"></a>E Assistente de instalação
Se iniciar o Assistente de instalação, o agendador fica temporariamente suspenso. Este comportamento é porque é suposto fazer alterações de configuração e não não possível aplicar estas definições se o motor de sincronização está ativamente em execução. Por esse motivo, não deixe o Assistente de instalação aberto, uma vez que ele deixa o motor de sincronização de realizar qualquer ação de sincronização.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
