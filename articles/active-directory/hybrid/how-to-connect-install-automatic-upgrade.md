---
title: 'Azure AD Connect: Atualização automática | Microsoft Docs'
description: Este tópico descreve a funcionalidade de atualização automática incorporada na sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317621"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: atualização automática
Esta característica foi introduzida com a construção [1.1.105.0 (lançada fevereiro de 2016)](reference-connect-version-history.md).  Esta funcionalidade foi atualizada na [build 1.1.561](reference-connect-version-history.md) e agora suporta cenários adicionais que anteriormente não eram suportados.

## <a name="overview"></a>Descrição Geral
Certificar-se de que a instalação Azure AD Connect está sempre atualizada nunca foi tão fácil com a funcionalidade **de atualização automática.** Esta funcionalidade é ativada por padrão para instalações expressas e atualizações dirSync. Quando uma nova versão é lançada, a sua instalação é automaticamente atualizada.
A atualização automática é ativada por padrão para o seguinte:

* Instalação de configurações expressas e atualizações dirSync.
* Utilizando o SQL Express LocalDB, que é o que as definições express usam sempre. DirSync com SQL Express também usa LocalDB.
* A conta AD é a conta MSOL_ por defeito criada pelas definições Express e DirSync.
* Tem menos de 100.000 objetos no metaverso.

O estado atual da atualização automática pode ser visualizado com o cmdlet PowerShell `Get-ADSyncAutoUpgrade` . Tem os seguintes estados:

| Estado | Comentário |
| --- | --- |
| Ativado |A atualização automática está ativada. |
| Suspenso |Definido apenas pelo sistema. O sistema não é **atualmente** elegível para receber atualizações automáticas. |
| Desativado |A atualização automática está desativada. |

Pode alterar entre **Ativado** e **Desativado** com `Set-ADSyncAutoUpgrade` . Apenas o sistema deve definir o estado **suspenso**.  Antes de 1.1.750.0, o Set-ADSyncAutoUpgrade cmdlet bloquearia o Auto-upgrade se o estado de atualização automática fosse definido para suspenso. Esta funcionalidade mudou agora para não bloquear o AutoUpgrade.

A atualização automática está a utilizar o Azure AD Connect Health para a infraestrutura de upgrade. Para o upgrade automático para funcionar, certifique-se de que abriu os URLs no seu servidor proxy para **Azure AD Connect Health,** conforme documentado nos [intervalos de URLs e endereço IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se o **UI do Serviço de Sincronização** estiver a funcionar no servidor, a atualização é suspensa até que a UI esteja fechada.

## <a name="troubleshooting"></a>Resolução de problemas
Se a instalação Connect não se atualizar como esperado, siga estes passos para descobrir o que pode estar errado.

Em primeiro lugar, não deverá esperar que a atualização automática seja tentada no primeiro dia em que uma nova versão seja lançada. Há uma aleatoriedade intencional antes de uma atualização ser tentada, por isso não se assuste se a sua instalação não for atualizada imediatamente.

Se acha que algo não está bem, então primeiro corra `Get-ADSyncAutoUpgrade` para garantir que a atualização automática está ativada.

Se o estado for suspenso, pode usar `Get-ADSyncAutoUpgrade -Detail` para ver a razão.  A razão da suspensão pode conter qualquer valor de corda, mas normalmente conterá o valor de cadeia do UpgradeResult, `UpgradeNotSupportedNonLocalDbInstall` `UpgradeAbortedAdSyncExeInUse` ou.  Um valor composto também pode ser devolvido, tal como `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Também é possível obter um resultado que não seja um UpgradeResult, ou seja, 'AADHealthEndpointNotDefined' ou 'DirSyncInPlaceUpgradeNonLocalDb'.

Em seguida, certifique-se de que abriu os URLs necessários no seu representante ou firewall. A atualização automática está a utilizar o Azure AD Connect Health, conforme descrito na [visão geral](#overview). Se utilizar um representante, certifique-se de que a Saúde foi configurada para utilizar um [servidor proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Teste também a [conectividade de Saúde](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ao Azure AD.

Com a conectividade com a Azure AD verificada, é hora de olhar para os eventlogs. Inicie o espectador do evento e olhe no eventlog **da Aplicação.** Adicione um filtro de eventlog para a fonte **Azure AD Connect Upgrade** e a gama de ID do evento **300-399**.  
![Screenshot que mostra a janela "Filter Current Log" com "Fontes de evento" e a caixa de IDs de evento "Incluir/Excluir" realçada.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Agora pode ver os registos associados ao estado de atualização automática.  
![Filtro Eventlog para upgrade automático](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

O código de resultados tem um prefixo com uma visão geral do estado.

| Prefixo do código de resultados | Description |
| --- | --- |
| Com êxito |A instalação foi atualizada com sucesso. |
| UpgradeAborted |Uma condição temporária parou a atualização. Será novamente julgado e a expectativa é que tenha sucesso mais tarde. |
| UpgradeNotSupported |O sistema tem uma configuração que está a impedir que o sistema seja automaticamente atualizado. Será novamente julgado para ver se o Estado está a mudar, mas a expectativa é que o sistema tenha de ser atualizado manualmente. |

Aqui está uma lista das mensagens mais comuns que encontra. Não enumera todos, mas a mensagem de resultados deve ser clara com o problema.

| Mensagem de Resultados | Description |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Não podia escrever ao registo. |
| UpgradeAbortedInsuficientDatabasePermissions |O grupo de administradores incorporados não tem permissões para a base de dados. Atualização manual para a versão mais recente do Azure AD Connect para resolver este problema. |
| UpgradeAbortedInsufficientDiskSpace |Não há espaço suficiente para suportar uma atualização. |
| UpgradeAbortedSecurityGroupsNotPresent |Não consegui encontrar e resolver todos os grupos de segurança utilizados pelo motor de sincronização. |
| UpgradeAbortedServiceCanNotBeStarted |O NT Service **Microsoft Azure AD Sync** não foi iniciado. |
| UpgradeAbortedServiceCanNotBes coberto |O NT Service **Microsoft Azure AD Sync** não conseguiu parar. |
| UpgradeAbortedServiceIsNotRunning |O NT Service **Microsoft Azure AD Sync** não está a funcionar. |
| UpgradeAbortedSyncCycleDisabled |A opção SyncCycle no [programador](how-to-connect-sync-feature-scheduler.md) foi desativada. |
| UpgradeAbortedSyncExeInUse |O [uI do gestor de serviços de sincronização](how-to-connect-sync-service-manager-ui.md) está aberto no servidor. |
| UpgradeAbortedSyncOrConfigurationInProgress |O assistente de instalação está em execução ou foi agendada uma sincronização fora do programador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportouCustomizedSyncRules |Adicionou as suas próprias regras personalizadas à configuração. |
| UpgradeNotSupportedInvalidPersistedState |A instalação não é uma definição Express ou uma atualização DirSync. |
| UpgradeNotSupportoLocalDbinstall |Não está a utilizar uma base de dados SQL Server Express LocalDB. |
|UpgradeNotSupedLocaldbSizeExceed|O tamanho DB local é maior ou igual a 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Os uploads de dados de saúde foram desativados do portal|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
