---
title: 'Azure AD Connect: Atualização automática / Microsoft Docs'
description: Este tópico descreve a funcionalidade de atualização automática incorporada na sincronização Azure AD Connect.
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
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a05de8bf6a6e4ab79e63d6634ddb1b79fae6045f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680214"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: atualização automática
Esta funcionalidade foi introduzida com a build [1.1.105.0 (lançado em fevereiro de 2016)](reference-connect-version-history.md#111050).  Esta funcionalidade foi atualizada na [build 1.1.561](reference-connect-version-history.md#115610) e agora suporta cenários adicionais que anteriormente não eram suportados.

## <a name="overview"></a>Descrição geral
Certificar-se de que a instalação Azure AD Connect está sempre atualizada nunca foi tão fácil com a funcionalidade de **atualização automática.** Esta funcionalidade está ativada por padrão para instalações expressas e atualizações do DirSync. Quando uma nova versão é lançada, a sua instalação é automaticamente atualizada.
A atualização automática está ativada por predefinição para o seguinte:

* Instalação de definições de expresso e upgrades dirSync.
* Utilizando o SQL Express LocalDB, que é o que as definições express usam sempre. DirSync com SQL Express também usa LocalDB.
* A conta AD é a conta MSOL_ predefinida criada pelas definições do Express e pelo DirSync.
* Tenha menos de 100.000 objetos no metaverso.

O estado atual de atualização automática pode ser visto com o cmdlet PowerShell `Get-ADSyncAutoUpgrade` . Tem os seguintes estados:

| Estado | Comentário |
| --- | --- |
| Ativado |A atualização automática está ativada. |
| Suspenso |Definido apenas pelo sistema. O sistema não é **atualmente** elegível para receber atualizações automáticas. |
| Desativado |A atualização automática está desativada. |

Pode alterar entre **Ativado** e **Desativado** com `Set-ADSyncAutoUpgrade` . Só o sistema deve definir o estado **suspenso.**  Antes de 1.1.750.0 o cmdlet Set-ADSyncAutoUpgrade bloquearia a atualização automática se o estado de atualização automática fosse definido para suspenso. Esta funcionalidade mudou agora para que não bloqueie o AutoUpgrade.

A atualização automática está a utilizar a Azure AD Connect Health para a infraestrutura de upgrade. Para que a atualização automática funcione, certifique-se de que abriu os URLs no seu servidor proxy para **a Azure AD Connect Health,** conforme documentado nas gamas de [endereços Do Office 365 URLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se o Gestor **de Serviços** de Sincronização UI estiver a funcionar no servidor, a atualização é suspensa até que o UI esteja fechado.

## <a name="troubleshooting"></a>Resolução de problemas
Se a instalação Connect não se atualizar como esperado, siga estes passos para saber o que pode estar errado.

Em primeiro lugar, não deve esperar que a atualização automática seja tentada no primeiro dia em que uma nova versão seja lançada. Há uma aleatoriedade intencional antes de uma atualização ser tentada, por isso não se assuste se a sua instalação não for atualizada imediatamente.

Se achar que algo não está certo, então primeiro corra `Get-ADSyncAutoUpgrade` para garantir que a atualização automática está ativada.

Em seguida, certifique-se de que abriu os URLs necessários no seu proxy ou firewall. A atualização automática está a utilizar a Azure AD Connect Health, conforme descrito na [visão geral](#overview). Se utilizar um representante, certifique-se de que a Saúde foi configurada para utilizar um [servidor proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Teste também a [conectividade de saúde](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) com a AD Azure.

Com a conectividade com a Azure AD verificada, é hora de olhar para os logs de eventos. Inicie o espectador do evento e procure no log de eventos da **Aplicação.** Adicione um filtro de eventlog para a fonte **Azure AD Connect Upgrade** e o intervalo de id do evento **300-399**.  
![Filtro de log de evento para atualização automática](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Pode agora ver os logs de eventos associados ao estado de atualização automática.  
![Filtro de log de evento para atualização automática](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

O código de resultados tem um prefixo com uma visão geral do estado.

| Prefixo do código de resultados | Descrição |
| --- | --- |
| Êxito |A instalação foi atualizada com sucesso. |
| UpgradeAbortado |Uma condição temporária parou a atualização. Será novamente julgado e a expectativa é que tenha sucesso mais tarde. |
| UpgradeNotSupported |O sistema tem uma configuração que está a bloquear a atualização automática do sistema. Será novamente tentado para ver se o Estado está a mudar, mas a expectativa é que o sistema tenha de ser atualizado manualmente. |

Aqui está uma lista das mensagens mais comuns que encontra. Não enumera tudo, mas a mensagem de resultados deve ser clara com o problema.

| Mensagem de Resultados | Descrição |
| --- | --- |
| **UpgradeAbortado** | |
| UpgradeNãoAlteradoNãoSetUpgradeMarker |Não podia escrever para o registo. |
| UpgradeAbortedInávelDatabasePermissions |O grupo de administradores incorporados não tem permissões para a base de dados. Atualize manualmente para a versão mais recente do Azure AD Connect para resolver este problema. |
| UpgradeAbortedIndDiskSpace |Não há espaço de disco suficiente para suportar uma atualização. |
| UpgradeAbortedSecurityGroupsNotPresent |Não conseguiu encontrar e resolver todos os grupos de segurança utilizados pelo motor de sincronização. |
| UpgradeAbortedServiceCanNotBeStarted |O serviço NT **Microsoft Azure AD Sync** não começou. |
| UpgradeAbortedServiceCanNotBeStopped |O Serviço NT **Microsoft Azure AD Sync** não conseguiu parar. |
| UpgradeAbortedServiceIsNotRunning |O serviço NT **Microsoft Azure AD Sync** não está a funcionar. |
| UpgradeSyncCycleDisabled |A opção SyncCycle no [programador](how-to-connect-sync-feature-scheduler.md) foi desativada. |
| UpgradeAbortedSyncExeInUse |O gestor de serviços de [sincronização UI](how-to-connect-sync-service-manager-ui.md) está aberto no servidor. |
| UpgradeAbortedSyncorConfigurationInProgress |O assistente de instalação está em execução ou uma sincronização foi programada fora do programador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Selecionou a Adfs como método de inscrição. |
| UpgradeNotSupportedCustomizedSyncRules |Adicionou as suas próprias regras personalizadas à configuração. |
| UpgradeNotSupportedDeviceWritebackEnabled |Ativou a funcionalidade de reprodução do [dispositivo.](how-to-connect-device-writeback.md) |
| UpgradeNotSupportedGroupWritebackEnabled |Permitiu a funcionalidade de redação em grupo. |
| UpgradeNotSupportedInvalidedPersistedState |A instalação não é uma definição express ou uma atualização DirSync. |
| UpgradeNotSupportedMetaverseSizeMeadoeeded |Tem mais de 100.000 objetos no metaverso. |
| UpgradeNotSupportedMultiForestSetup |Estás a ligar-te a mais do que uma floresta. A configuração expressa só se liga a uma floresta. |
| UpgradeNotSupportedNonLocalDbInstall |Não está a utilizar uma base de dados LocalDB do SQL Server Express. |
| UpgradeNotSupportedNonMsolAccount |A [conta AD DS Connector](reference-connect-accounts-permissions.md#ad-ds-connector-account) já não é a conta padrão MSOL_. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Ao configurar o AAD Connect, optou por *Não Configurar* ao selecionar o método de início de sessão. |
| UpgradeNotSupportedStagingModeEnabled |O servidor está programado para estar no [modo de preparação](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Ativou a funcionalidade de [reutilização](how-to-connect-preview.md#user-writeback) do utilizador. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
