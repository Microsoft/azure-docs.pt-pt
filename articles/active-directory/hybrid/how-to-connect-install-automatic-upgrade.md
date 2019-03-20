---
title: 'Azure AD Connect: A atualização automática | Documentos da Microsoft'
description: Este tópico descreve a funcionalidade de atualização automática incorporada na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003907"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Atualização automática
Esta funcionalidade foi introduzida com compilação [1.1.105.0 (lançado em Fevereiro de 2016)](reference-connect-version-history.md#111050).  Esta funcionalidade foi atualizada na [criar 1.1.561](reference-connect-version-history.md#115610) e agora oferece suporte a cenários adicionais que foram anteriormente não suportados.

## <a name="overview"></a>Descrição geral
Certificar-se de que a instalação do Azure AD Connect é sempre atualizada nunca foi tão fácil com o **a atualização automática** funcionalidade. Esta funcionalidade está ativada por predefinição para instalações express e atualiza do DirSync. Quando for lançada uma nova versão, a instalação é atualizada automaticamente.
A atualização automática está ativada por predefinição para o seguinte:

* Expresse a instalação de definições e atualizações de DirSync.
* Utilizar o SQL Express LocalDB, que é o que as definições Express sempre usar. DirSync com o SQL Express também utilizar a LocalDB.
* A conta do AD é a conta de msol _ predefinida criada pelo definições Express e o DirSync.
* Ter menos de 100 000 objetos no metaverso.

O estado atual da atualização automática pode ser visualizado com o cmdlet do PowerShell `Get-ADSyncAutoUpgrade`. Ele tem os seguintes Estados:

| Estado | Comentário |
| --- | --- |
| Ativado |A atualização automática está ativada. |
| Suspenso |Definido pelo sistema apenas. O sistema está **atualmente, não é** elegíveis para receber atualizações automáticas. |
| Desativado |A atualização automática está desativada. |

Pode alternar entre **Enabled** e **desativada** com `Set-ADSyncAutoUpgrade`. Apenas o sistema deve definir o estado **suspenso**.  Antes de 1.1.750.0 o cmdlet Set-ADSyncAutoUpgrade bloquearia Autoupgrade se o estado de atualização automática foi definido como suspenso. Esta funcionalidade foi alterado, de modo que não bloqueie AutoUpgrade.

A atualização automática está a utilizar o Azure AD Connect Health para a infraestrutura de atualização. Para a atualização automática trabalhar, certifique-se de abrir os URLs em seu servidor de proxy para **do Azure AD Connect Health** conforme documentado no [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se o **Synchronization Service Manager** interface do Usuário está em execução no servidor, em seguida, a atualização está suspenso até que a interface do Usuário seja fechado.

## <a name="troubleshooting"></a>Resolução de problemas
Se a instalação Connect não atualizar-se conforme esperado, em seguida, siga estes passos para descobrir o que poderá estar errado.

Em primeiro lugar, não deve esperar que a atualização automática para ser tentada o primeiro dia que é lançada uma nova versão. Há uma aleatoriedade intencional antes de uma atualização é tentada, pelo que não se alarme se a instalação não é imediatamente atualizada.

Se achar que algo não está correto, em seguida, execute primeiro `Get-ADSyncAutoUpgrade` para garantir que a atualização automática está ativada.

Em seguida, certifique-se de que abrir os URLs necessários no proxy ou firewall. Atualização automática está a utilizar o Azure AD Connect Health conforme descrito no [descrição geral](#overview). Se utilizar um proxy, certifique-se de que o estado de funcionamento foi configurado para utilizar um [servidor proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Também testar a [conectividade de estado de funcionamento](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para o Azure AD.

Com a conectividade para o Azure AD verificado, é hora de examinar o eventlogs. Iniciar o Visualizador de eventos e examinar os **aplicativo** registo de eventos. Adicionar um filtro de registo de eventos para a origem **do Azure AD Connect atualizar** e o intervalo de id de evento **300 399**.  
![Filtro de registo de eventos para a atualização automática](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Agora, pode ver o eventlogs associados com o estado de atualização automática.  
![Filtro de registo de eventos para a atualização automática](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

O código de resultado tem um prefixo com uma visão geral do Estado.

| Prefixo de código de resultado | Descrição |
| --- | --- |
| Êxito |A instalação foi atualizada com êxito. |
| UpgradeAborted |Uma condição temporária parado a atualização. Ele será repetido novamente e a expectativa é que ele for concluída com êxito mais tarde. |
| UpgradeNotSupported |O sistema tem uma configuração que está a bloquear o sistema sejam atualizados automaticamente. Será repetida para ver se o estado está mudando, mas a expectativa é que o sistema tem de ser atualizado manualmente. |

Aqui está uma lista das mensagens mais comuns que encontrar. Não listar todos, mas a mensagem de resultado deve ser clara com que o problema é.

| Mensagem de resultado | Descrição |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Não foi possível escrever no registo. |
| UpgradeAbortedInsufficientDatabasePermissions |O grupo de administradores internos não tem permissões para a base de dados. Atualize manualmente para a versão mais recente do Azure AD Connect para resolver este problema. |
| UpgradeAbortedInsufficientDiskSpace |Não existe espaço em disco suficiente para suportar uma atualização. |
| UpgradeAbortedSecurityGroupsNotPresent |Não foi possível encontrar e resolver todos os grupos de segurança usados pelo mecanismo de sincronização. |
| UpgradeAbortedServiceCanNotBeStarted |O serviço NT **Microsoft Azure AD Sync** falhou ao iniciar. |
| UpgradeAbortedServiceCanNotBeStopped |O serviço NT **Microsoft Azure AD Sync** Falha ao parar. |
| UpgradeAbortedServiceIsNotRunning |O serviço NT **Microsoft Azure AD Sync** não está em execução. |
| UpgradeAbortedSyncCycleDisabled |A opção de SyncCycle na [agendador](how-to-connect-sync-feature-scheduler.md) foi desativada. |
| UpgradeAbortedSyncExeInUse |O [o Gestor do serviço de sincronização da interface do Usuário](how-to-connect-sync-service-manager-ui.md) está aberta no servidor. |
| UpgradeAbortedSyncOrConfigurationInProgress |O Assistente de instalação está em execução ou uma sincronização foi agendada fora o scheduler. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Selecionou Adfs como o método de início de sessão. |
| UpgradeNotSupportedCustomizedSyncRules |Adicionou suas próprias regras personalizadas para a configuração. |
| UpgradeNotSupportedDeviceWritebackEnabled |Ativou o [repetição de escrita do dispositivo](how-to-connect-device-writeback.md) funcionalidade. |
| UpgradeNotSupportedGroupWritebackEnabled |Ativou o [repetição de escrita do grupo](how-to-connect-preview.md#group-writeback) funcionalidade. |
| UpgradeNotSupportedInvalidPersistedState |A instalação não é um definições Express ou uma atualização do DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Tem mais de 100 000 objetos no metaverso. |
| UpgradeNotSupportedMultiForestSetup |Está a ligar mais do que uma floresta. Configuração rápida apenas se conecta a uma floresta. |
| UpgradeNotSupportedNonLocalDbInstall |Não estiver a utilizar uma base de dados do SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |O [conta de conector do AD DS](reference-connect-accounts-permissions.md#ad-ds-connector-account) não é a conta de msol _ predefinida mais. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Quando configurar o AAD Connect, que escolheu *não Configure* ao selecionar o método de início de sessão. |
| UpgradeNotSupportedPtaSignInMethod | Tiver selecionado autenticação pass-through como o método de início de sessão. |
| UpgradeNotSupportedStagingModeEnabled |O servidor está definido para estar no [modo de teste](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Ativou o [repetição de escrita do utilizador](how-to-connect-preview.md#user-writeback) funcionalidade. |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
