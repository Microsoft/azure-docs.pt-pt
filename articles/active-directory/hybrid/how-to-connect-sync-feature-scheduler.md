---
title: 'Azure AD Connect sync: Scheduler / Microsoft Docs'
description: Este tópico descreve a funcionalidade de programador incorporado na sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084741"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: Scheduler
Este tópico descreve o programador incorporado na sincronização Azure AD Connect (motor de sincronização).

Esta funcionalidade foi introduzida com a construção 1.1.105.0 (lançada em fevereiro de 2016).

## <a name="overview"></a>Descrição geral
Azure AD Connect sincroniza as alterações ocorridas no seu diretório no local utilizando um programador. Existem dois processos de programador, um para sincronização de palavra-passe e outro para tarefas de sincronização e manutenção de objeto/atributo. Este tópico abrange este último.

Em lançamentos anteriores, o programador de objetos e atributos era externo ao motor de sincronização. Utilizou o programador de tarefas do Windows ou um serviço separado do Windows para desencadear o processo de sincronização. O programador está com os 1.1 lançamentos incorporados no motor de sincronização e permite alguma personalização. A nova frequência de sincronização padrão é de 30 minutos.

O programador é responsável por duas tarefas:

* **Ciclo de sincronização**. O processo de importação, sincronização e exportação alterações.
* **Tarefas de manutenção**. Renovar chaves e certificados para reset de password e serviço de registo de dispositivos (DRS). Purgue entradas antigas no registo de operações.

O agendador em si está sempre em execução, mas pode ser configurado para executar apenas uma ou nenhuma destas tarefas. Por exemplo, se precisar de ter o seu próprio processo de ciclo de sincronização, pode desativar esta tarefa no programador, mas ainda assim executar a tarefa de manutenção.

>[!IMPORTANT]
>Por predefinição a cada 30 minutos, é executado um ciclo de sincronização. Se tiver modificado a ciclo de sincronização, terá de se certificar de que um ciclo de sincronização é executado pelo menos uma vez a cada 7 dias. 
>
>* Uma sincronização delta tem de acontecer dentro de 7 dias a partir da última sincronização delta.
>* Uma sincronização delta (seguindo uma sincronização completa) tem de acontecer dentro de 7 dias a partir do momento em que a última sincronização completa foi concluída.
>
>Se não o fizer, poderá causar problemas de sincronização que exigirão que execute uma sincronização completa para resolver. Isto também se aplica aos servidores no modo de encenação.

## <a name="scheduler-configuration"></a>Configuração do programador
Para ver as definições de configuração atuais, vá ao PowerShell e corra `Get-ADSyncScheduler` . Mostra algo como esta foto:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Se vir **O comando de sincronização ou cmdlet não está disponível** quando executar este cmdlet, então o módulo PowerShell não está carregado. Este problema pode acontecer se executar O Azure AD Connect num controlador de domínio ou num servidor com níveis de restrição PowerShell mais elevados do que as definições predefinidas. Se vir este erro, corra `Import-Module ADSync` para disponibilizar o cmdlet.

* **PermitidoSssyncCycleInterval**. O intervalo de tempo mais curto entre os ciclos de sincronização permitidos pelo Azure AD. Não é possível sincronizar com mais frequência do que esta definição e continuar a ser suportada.
* **Atualmente, oFectiveSyncCycleInterval**. O horário está em vigor. Tem o mesmo valor que o CustomizedSyncInterval (se definido) se não for mais frequente do que o PermitidoSyncInterval. Se utilizar uma construção antes de 1.1.281 e alterar PersonalizedSyncCycleInterval, esta alteração produz efeitos após o próximo ciclo de sincronização. A partir da construção 1.1.281 a mudança entra em vigor imediatamente.
* **PersonalizadoSSCrícicosSyncCycleInterval**. Se pretender que o programador seja executado em qualquer outra frequência que não seja o padrão de 30 minutos, então configufique esta definição. Na imagem acima, o programador foi programado para ser executado a cada hora em vez disso. Se definir esta definição para um valor inferior ao PermitidoSyncInterval, então este último é usado.
* **NextSyncCyclePolicyType**. Delta ou Initial. Define se a próxima execução deve apenas processar alterações delta, ou se a próxima execução deve fazer uma importação completa e sincronização. Este último também reprocessaria quaisquer regras novas ou alteradas.
* **NextSyncCycleStartTimeInUTC**. Da próxima vez, o programador inicia o próximo ciclo de sincronização.
* **PurgeRunHistoryInterval**. Os registos de operação de tempo devem ser mantidos. Estes registos podem ser revistos no gestor de serviços de sincronização. O padrão é manter estes registos durante 7 dias.
* **SyncCycleEnabled**. Indica se o programador está a executar os processos de importação, sincronização e exportação como parte do seu funcionamento.
* **ManutençãoEnabled**. Mostra se o processo de manutenção está ativado. Atualiza os certificados/chaves e limpa o registo de operações.
* **StagingModeEnabled**. Mostra se [o modo de preparação](how-to-connect-sync-staging-server.md) está ativado. Se esta definição estiver ativada, então suprime as exportações de funcionamento, mas ainda funciona a importação e a sincronização.
* **ProgramadorSpenso.** Configurar por Connect durante uma atualização para bloquear temporariamente o programador de funcionamento.

Pode alterar algumas destas definições com `Set-ADSyncScheduler` . Os seguintes parâmetros podem ser modificados:

* PersonalizadoSSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* ManutençãoEnabled

Em construções anteriores de Azure AD Connect, **isStagingModeEnabled** foi exposto em Set-ADSyncScheduler. Não é **suportado** para definir esta propriedade. A propriedade **SchedulerSuspended** só deve ser modificada pelo Connect. Não é **suportado** para definir isto com PowerShell diretamente.

A configuração do programador é armazenada em Azure AD. Se tiver um servidor de paragem, qualquer alteração no servidor primário também afeta o servidor de paragem (exceto isStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>PersonalizadoSSyncCycleInterval
Sintaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, mm - minutos, ss - segundos

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Muda o programador para funcionar a cada 3 horas.

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
As alterações alteram o programador para ser executado diariamente.

### <a name="disable-the-scheduler"></a>Desative o programador  
Se precisar de fazer alterações de configuração, então pretende desativar o programador. Por exemplo, quando [configurar a filtragem](how-to-connect-sync-configure-filtering.md) ou [fazer alterações às regras de sincronização](how-to-connect-sync-change-the-configuration.md).

Para desativar o programador, corra `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Desative o programador](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Quando tiver feito as alterações, não se esqueça de voltar a ativar o programador com `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Inicie o programador
O programador é executado por defeito a cada 30 minutos. Em alguns casos, pode querer executar um ciclo de sincronização entre os ciclos programados ou precisa executar um tipo diferente.

### <a name="delta-sync-cycle"></a>Ciclo de sincronização delta
Um ciclo de sincronização delta inclui os seguintes passos:


- Importação delta em todos os Conectores
- Sincronização delta em todos os Conectores
- Exportação em todos os Conectores

### <a name="full-sync-cycle"></a>Ciclo de sincronização completo
Um ciclo de sincronização completo inclui os seguintes passos:

- Importação completa em todos os Conectores
- Sincronização completa em todos os Conectores
- Exportação em todos os Conectores

Pode ser que tenha uma mudança urgente que deve ser sincronizada imediatamente, e é por isso que precisa de executar manualmente um ciclo. 

Se necessitar de executar manualmente um ciclo de sincronização, então a partir da execução PowerShell `Start-ADSyncSyncCycle -PolicyType Delta` .

Para iniciar um ciclo de sincronização completo, corra `Start-ADSyncSyncCycle -PolicyType Initial` a partir de um pedido PowerShell.   

Executar um ciclo de sincronização completo pode ser muito demorado, leia a secção seguinte para ler como otimizar este processo.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Etapas de sincronização necessárias para diferentes alterações de configuração
Diferentes alterações de configuração requerem diferentes etapas de sincronização para garantir que as alterações são corretamente aplicadas a todos os objetos.

- Adicionou mais objetos ou atributos a serem importados de um diretório de origem (adicionando/modificando as regras de sincronização)
    - É necessária uma importação completa no conector para esse diretório de origem
- Alterações às regras de sincronização
    - É necessário um Sincronização Completa no Conector para as regras de sincronização alteradas
- Filtragem alterada [para](how-to-connect-sync-configure-filtering.md) que um número diferente de objetos deve ser incluído
    - É necessária uma importação completa no Conector para cada Conector AD, a menos que esteja a utilizar a filtragem baseada em atributos com base em atributos que já estão a ser importados para o motor de sincronização

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Personalizar um ciclo de sincronização executar a mistura certa de passos de sincronização Delta e Full
Para evitar a execução de um ciclo de sincronização completo, pode marcar conectores específicos para executar um passo completo utilizando os seguintes cmdlets.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Exemplo: Se estornou as regras de sincronização do Conector "AD Forest A" que não requerem que sejam importados novos atributos, executaria os seguintes cmdlets para executar um ciclo de sincronização delta que também fez um passo full Sync para esse Conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Exemplo: Se estornou as regras de sincronização do Conector "AD Forest A" para que agora exijam que um novo atributo seja importado, executará os seguintes cmdlets para executar um ciclo de sincronização delta que também fez um passo full import, Full Sync para esse Conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Pare o agendador
Se o programador estiver atualmente a executar um ciclo de sincronização, poderá ter de o parar. Por exemplo, se iniciar o assistente de instalação e tiver este erro:

![Os programas de screenshot não podem alterar a mensagem de erro de configuração.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Quando um ciclo de sincronização está em funcionamento, não é possível fazer alterações de configuração. Pode esperar até que o agendador termine o processo, mas também pode pará-lo para que possa fazer as suas alterações imediatamente. Parar o ciclo atual não é prejudicial e as alterações pendentes são processadas com o próximo passo.

1. Comece por dizer ao programador para parar o seu ciclo atual com o cmdlet PowerShell `Stop-ADSyncSyncCycle` .
2. Se utilizar uma construção antes do 1.1.281, então parar o programador não impede o conector atual da sua tarefa atual. Para forçar o Conector a parar, tome as seguintes ações:

   ![O Screenshot mostra o Gestor de Serviço de Sincronização com conectores selecionados e um conector em execução destacado com a ação Stop selecionada.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Inicie **o Serviço de Sincronização** a partir do menu inicial. Vá a **Connectors**, realce o Conector com o estado **running**, e selecione **Stop** from the Actions.

O agendador ainda está ativo e recomeça na próxima oportunidade.

## <a name="custom-scheduler"></a>Agendador personalizado
Os cmdlets documentados nesta secção só estão disponíveis na construção [1.1.130.0](reference-connect-version-history.md) e posterior.

Se o programador incorporado não satisfizer os seus requisitos, então pode agendar os Conectores utilizando o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Desta forma, pode iniciar um perfil para um Conector:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a utilizar para [nomes de conector](how-to-connect-sync-service-manager-ui-connectors.md) e [nomes de perfil de execução](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontrados na [UI do Gestor de Serviços de Sincronização](how-to-connect-sync-service-manager-ui.md).

![Invocar perfil de execução](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é sincronizado, ou seja, não devolve o controlo até que o Conector tenha concluído a operação, com sucesso ou com um erro.

Ao agendar os seus Conectores, a recomendação é agendar os mesmos na seguinte ordem:

1. (Full/Delta) Importação de diretórios no local, tais como Ative Directory
2. (Full/Delta) Importação do Azure AD
3. (Full/Delta) Sincronização de diretórios no local, tais como Ative Directory
4. (Full/Delta) Sincronização a partir de Azure AD
5. Exportação para Azure AD
6. Exportação para diretórios no local, tais como Ative Directory

Esta ordem é como o programador incorporado executa os Conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Também pode monitorizar o motor de sincronização para ver se está ocupado ou inativo. Este cmdlet devolve um resultado vazio se o motor de sincronização estiver inativo e não estiver a funcionar com um Conector. Se um Conector estiver em funcionamento, devolve o nome do Conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado da execução do conector](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um estado onde o motor de sincronização está inativo. A segunda linha de quando o Conector AD AZure está em funcionamento.

## <a name="scheduler-and-installation-wizard"></a>Programador e assistente de instalação
Se iniciar o assistente de instalação, o programador está temporariamente suspenso. Este comportamento deve-se ao facto de se ter assumido que faz alterações de configuração e estas definições não podem ser aplicadas se o motor de sincronização estiver a funcionar ativamente. Por esta razão, não deixe o assistente de instalação aberto, uma vez que impede que o motor de sincronização efetua quaisquer ações de sincronização.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
