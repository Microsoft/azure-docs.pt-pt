---
title: 'Sincronização Azure AD Connect: Scheduler / Microsoft Docs'
description: Este tópico descreve a funcionalidade de programador incorporado na sincronização Azure AD Connect.
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
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261076"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização azure AD Connect: Scheduler
Este tópico descreve o programador incorporado no sincronizacro Azure AD Connect (motor de sincronização).

Esta funcionalidade foi introduzida com a build 1.1.105.0 (lançado em fevereiro de 2016).

## <a name="overview"></a>Descrição geral
O Azure AD Connect sincroniza as alterações que ocorrem no seu diretório no local utilizando um programador. Existem dois processos de programação, um para sincronização de palavras-passe e outro para tarefas de sincronização e manutenção de objetos/atributos. Este tema abrange este último.

Em lançamentos anteriores, o programador de objetos e atributos era externo ao motor de sincronização. Utilizou o programador de tarefas do Windows ou um serviço Windows separado para desencadear o processo de sincronização. O programador está com os lançamentos 1.1 incorporados no motor de sincronização e permitem alguma personalização. A nova frequência de sincronização padrão é de 30 minutos.

O programador é responsável por duas tarefas:

* **Ciclo de sincronização.** O processo de importação, sincronização e exportação alterações.
* **Tarefas de manutenção.** Renovar chaves e certificados para reset de palavra-passe e Serviço de Registo de Dispositivos (DRS). Purgue entradas antigas no registo de operações.

O programador em si está sempre a funcionar, mas pode ser configurado apenas para executar uma ou nenhuma destas tarefas. Por exemplo, se precisar de ter o seu próprio processo de ciclo de sincronização, pode desativar esta tarefa no programador, mas ainda executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do programador
Para ver as definições de configuração atuais, vá ao PowerShell e execute `Get-ADSyncScheduler`. Mostra algo como esta foto:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Se vir o comando de sincronização ou o **cmdlet não está disponível** quando executar este cmdlet, então o módulo PowerShell não está carregado. Este problema pode acontecer se executar o Azure AD Connect num controlador de domínio ou num servidor com níveis de restrição PowerShell mais elevados do que as definições predefinidas. Se vir este erro, faça `Import-Module ADSync` para disponibilizar o cmdlet.

* **Intervalo de ciclos de sincronização permitido**. O intervalo de tempo mais curto entre ciclos de sincronização permitidos pela Azure AD. Não é possível sincronizar com mais frequência do que este ajuste e ainda ser apoiado.
* **AtualmenteEficazSyncCycleInterval**. O horário está em vigor. Tem o mesmo valor que o CustomizedSyncInterval (se definido) se não for mais frequente do que o AllowedSyncInterval. Se utilizar uma construção antes de 1.1.281 e alterar CustomizedSyncCycleInterval, esta alteração entra em vigor após o próximo ciclo de sincronização. A partir da construção 1.1.281 a mudança entra em vigor imediatamente.
* **SyncCycleInterval personalizado**. Se pretender que o programador seja executado em qualquer outra frequência que não os 30 minutos predefinidos, então configura esta definição. Na imagem acima, o programador foi programado para funcionar a cada hora em vez disso. Se definir esta definição para um valor inferior ao PermitidoSync, então este último é utilizado.
* Tipo de **política de sincronização seguinte**. Delta ou Inicial. Define se a próxima execução deve apenas processar alterações delta, ou se o próximo ensaio deve fazer uma importação completa e sincronização. Este último também reprocessaria quaisquer novas ou alteradas regras.
* **NextSyncCycleStartTimeinUTC**. Da próxima vez que o programador começar o próximo ciclo de sincronização.
* **Intervalo de História de Purga.** Os registos de funcionamento devem ser mantidos. Estes registos podem ser revistos no gestor de serviços de sincronização. O padrão é manter estes registos durante 7 dias.
* **SyncCycleEnabled**. Indica se o programador está a executar os processos de importação, sincronização e exportação como parte do seu funcionamento.
* **ManutençãoHabilitada**. Mostra se o processo de manutenção está ativado. Atualiza os certificados/teclas e expurga o registo de operações.
* **StagingModeEnabled**. Mostra se o [modo de preparação](how-to-connect-sync-staging-server.md) está ativado. Se esta definição estiver ativada, então suprime as exportações de funcionamento, mas continua a ser a importação e a sincronização.
* **SchedulerSuspenso .** Configurado por Connect durante uma atualização para bloquear temporariamente o programador de funcionamento.

Pode alterar algumas destas definições com `Set-ADSyncScheduler`. Podem ser modificados os seguintes parâmetros:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* ManutençãoHabilitada

Em construções anteriores do Azure AD Connect, **isStagingModeEnabled** foi exposto em Set-ADSyncScheduler. Não é **suportado** para definir esta propriedade. A propriedade **SchedulerSuspended** só deve ser modificada pela Connect. Não é **suportado** para definir isto com powerShell diretamente.

A configuração do programador é armazenada em Azure AD. Se tiver um servidor de encenação, qualquer alteração no servidor primário também afeta o servidor de encenação (exceto o IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, mm - minutos, ss - segundos

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Muda o programador para funcionar a cada 3 horas.

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
As alterações mudam o programador para funcionar diariamente.

### <a name="disable-the-scheduler"></a>Desativar o programador  
Se precisar de fazer alterações de configuração, então pretende desativar o programador. Por exemplo, quando [configura filtrar](how-to-connect-sync-configure-filtering.md) ou [fazer alterações às regras](how-to-connect-sync-change-the-configuration.md)de sincronização .

Para desativar o programador, corra `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desativar o programador](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Quando tiver feito as suas alterações, não se esqueça de voltar a ativar o programador com `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Inicie o programador
O programador é executado por padrão a cada 30 minutos. Em alguns casos, é melhor fazer um ciclo de sincronização entre os ciclos programados ou é necessário executar um tipo diferente.

### <a name="delta-sync-cycle"></a>Ciclo de sincronização delta
Um ciclo de sincronização delta inclui os seguintes passos:


- Importação delta em todos os Conectores
- Sincronização delta em todos os Conectores
- Exportação de todos os Conectores

### <a name="full-sync-cycle"></a>Ciclo de sincronização completa
Um ciclo de sincronização completo inclui os seguintes passos:

- Importação total em todos os Conectores
- Sincronização completa em todos os Conectores
- Exportação de todos os Conectores

Pode ser que tenha uma mudança urgente que deve ser sincronizada imediatamente, e é por isso que precisa de fazer um ciclo manualmente. 

Se precisar de executar manualmente um ciclo de sincronização, então a partir de PowerShell executar `Start-ADSyncSyncCycle -PolicyType Delta`.

Para iniciar um ciclo de sincronização completo, execute `Start-ADSyncSyncCycle -PolicyType Initial` a partir de um pedido powerShell.   

Executar um ciclo de sincronização completo pode ser muito demorado, leia a secção seguinte para ler como otimizar este processo.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Passos de sincronização necessários para diferentes alterações de configuração
Diferentes alterações de configuração requerem diferentes passos de sincronização para garantir que as alterações são corretamente aplicadas a todos os objetos.

- Adicione mais objetos ou atributos a importar de um diretório de origem (adicionando/modificando as regras de sincronização)
    - É necessária uma importação completa no Connector para esse diretório de origem
- Fez alterações às regras de Sincronização
    - Um Sincronizado Completo é necessário no Conector para as regras de sincronização alteradas
- [Filtragem](how-to-connect-sync-configure-filtering.md) alterada para que um número diferente de objetos deve ser incluído
    - É necessária uma importação completa no Conector para cada Conector AD, a menos que esteja a utilizar a filtragem baseada em Atributos com base em atributos que já estão a ser importados para o motor de sincronização

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Personalizando um ciclo de sincronização executar a mistura certa de passos de sincronização Delta e Full
Para evitar a execução de um ciclo de sincronização completo, pode marcar Conectores específicos para executar um passo completo utilizando os seguintes cmdlets.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Exemplo: Se tiver alterações nas regras de sincronização do Connector "AD Forest A" que não exijam a importação de novos atributos, executará os seguintes cmdlets para executar um ciclo de sincronização delta que também fez um passo Full Sync para esse Conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Exemplo: Se tiver alterações nas regras de sincronização do Connector "AD Forest A" para que agora exijam a importação de um novo atributo, executará os seguintes cmdlets para executar um ciclo de sincronização delta que também fez um passo completo de importação, full sync para esse Conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Pare o programador
Se o programador estiver atualmente a executar um ciclo de sincronização, poderá ter de o parar. Por exemplo, se iniciar o assistente de instalação e tiver este erro:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Quando um ciclo de sincronização está em execução, não é possível fazer alterações de configuração. Pode esperar até que o programador termine o processo, mas também pode detê-lo para que possa fazer as suas alterações imediatamente. Parar o ciclo atual não é prejudicial e as alterações pendentes são processadas com a próxima execução.

1. Comece por dizer ao programador para parar o seu ciclo atual com o cmdlet PowerShell `Stop-ADSyncSyncCycle`.
2. Se utilizar uma construção antes de 1.1.281, então parar o programador não impede o conector atual da sua tarefa atual. Para forçar o Conector a parar, tome as seguintes ações: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Inicie o Serviço de **Sincronização** a partir do menu inicial. Vá a **Conectores,** realce o Conector com o estado **de funcionamento**, e selecione **Parar** das Ações.

O programador ainda está ativo e recomeça na próxima oportunidade.

## <a name="custom-scheduler"></a>Programador personalizado
Os cmdlets documentados nesta secção só estão disponíveis na construção [1.1.130.0](reference-connect-version-history.md#111300) e posteriormente.

Se o programador incorporado não satisfazer os seus requisitos, então pode agendar os Conectores utilizando o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Pode iniciar um perfil para um Conector desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a utilizar para [nomes de Conector](how-to-connect-sync-service-manager-ui-connectors.md) e Nomes de [Perfil de Execução](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontrados no UI do Gestor de Serviços de [Sincronização](how-to-connect-sync-service-manager-ui.md).

![Invocar perfil de execução](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é sincronizado, isto é, não devolve o controlo até que o Conector tenha concluído a operação, com sucesso ou com um erro.

Quando agendar os seus Conectores, a recomendação é agendá-los na seguinte ordem:

1. (Full/Delta) Importação de diretórios no local, tais como Diretório Ativo
2. (Full/Delta) Importação de Azure AD
3. (Full/Delta) Sincronização a partir de diretórios no local, tais como Diretório Ativo
4. (Full/Delta) Sincronização da Azure AD
5. Exportação para AD Azure
6. Exportação para diretórios no local, tais como Diretório Ativo

Esta ordem é como o programador incorporado executa os Conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Também pode monitorizar o motor de sincronização para ver se está ocupado ou inativo. Este cmdlet devolve um resultado vazio se o motor de sincronização estiver inativo e não estiver a funcionar um Connector. Se um Conector estiver em funcionamento, devolve o nome do Conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado de execução do conector](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um estado onde o motor de sincronização está inativo. A segunda linha de quando o Conector Azure AD está em funcionamento.

## <a name="scheduler-and-installation-wizard"></a>Programador e assistente de instalação
Se ligar o assistente de instalação, o programador está temporariamente suspenso. Este comportamento deve-se ao facto de se presumir que efaz alterações de configuração e estas definições não podem ser aplicadas se o motor sincronizado estiver a funcionar ativamente. Por esta razão, não deixe o assistente de instalação aberto, uma vez que impede o motor de sincronização de realizar quaisquer ações de sincronização.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
