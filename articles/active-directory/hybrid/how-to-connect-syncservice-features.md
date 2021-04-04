---
title: Funcionalidades e configuração do serviço Azure AD Connect e configuração | Microsoft Docs
description: Descreve funcionalidades laterais de serviço para o serviço de sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410543"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funcionalidades do serviço de sincronização do Azure AD Connect

A função de sincronização do Azure AD Connect tem dois componentes:

* O componente no local chamado **Azure AD Connect sync**, também chamado **sync engine**.
* O serviço residente em Azure AD também conhecido como **Azure AD Connect sync service**

Este tópico explica como funcionam as seguintes funcionalidades do serviço de **sincronização Azure AD Connect** e como pode configurá-las utilizando o Windows PowerShell.

Estas definições são configuradas pelo [Módulo de Diretório Ativo Azure para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)). Faça o download e instale-o separadamente do Azure AD Connect. Os cmdlets documentados neste tema foram introduzidos no [lançamento de março de 2016 (construção 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou não produzir o mesmo resultado, certifique-se de que executa a versão mais recente.

Para ver a configuração no seu diretório AD Azure, corra `Get-MsolDirSyncFeatures` .  
![Resultado get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Muitas destas definições só podem ser alteradas pelo Azure AD Connect.

As seguintes definições podem ser configuradas `Set-MsolDirSyncFeature` por:

| DirSyncFeature | Comentário |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Permite que os objetos se juntem ao nome do utilizadorPrincipalName, além do endereço SMTP primário. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Permite ao motor de sincronização atualizar o atributo userPrincipalName para utilizadores geridos/licenciados (não federados). |

Depois de ter ativado uma funcionalidade, esta não poderá ser novamente desativada.

> [!NOTE]
> A partir de 24 de agosto de 2016, a funcionalidade *Duplicado atribui resiliência* ao atributo duplicado é ativada por padrão para novos diretórios AZure AD. Esta funcionalidade também será lançada e ativada nos diretórios criados antes desta data. Receberá uma notificação por e-mail quando o seu diretório estiver prestes a ativar esta funcionalidade.
> 
> 

As seguintes definições são configuradas pelo Azure AD Connect e não podem ser modificadas `Set-MsolDirSyncFeature` por:

| DirSyncFeature | Comentário |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Ativar a gravação do dispositivo](how-to-connect-device-writeback.md) |
| Diretórios |[Azure AD Connect Sync: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateNResiliency](#duplicate-attribute-resiliency) |Permite que um atributo seja colocado em quarentena quando é uma duplicação de outro objeto em vez de falhar todo o objeto durante a exportação. |
| Sincronização hash de palavra-passe |[Implementação de sincronização de hash de palavra-passe com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Autenticação pass-through|[Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)|
| UniifiedGroupWriteback |Repetição de escrita do grupo|
| UserWriteback |Não apoiado atualmente. |

## <a name="duplicate-attribute-resiliency"></a>Duplicação da resiliência do atributo

Em vez de não providenciar objetos com UPNs /proxyAddresses duplicados, o atributo duplicado é "quarentena" e é atribuído um valor temporário. Quando o conflito é resolvido, a UPN temporária é alterada automaticamente para o valor adequado. Para mais detalhes, consulte [a sincronização de identidade e duplicar a resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName soft match

Quando esta funcionalidade está ativada, o soft-match é ativado para UPN, além do [endereço SMTP primário](https://support.microsoft.com/kb/2641663), que está sempre ativado. O soft-match é usado para combinar os utilizadores de nuvem existentes em Azure AD com os utilizadores no local.

Se precisar de combinar contas AD no local com as contas existentes criadas na nuvem e não estiver a utilizar o Exchange Online, então esta funcionalidade é útil. Neste cenário, geralmente não tem uma razão para definir o atributo SMTP na nuvem.

Esta funcionalidade está acesa por padrão para os recém-criados diretórios Azure AD. Pode ver se esta funcionalidade está ativada para si executando:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esta funcionalidade não estiver ativada para o seu diretório AD Azure, então pode ative-la executando:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar atualizações do UserPrincipalName

Historicamente, as atualizações ao atributo UserPrincipalName utilizando o serviço de sincronização a partir do local foram bloqueadas, a menos que ambas as condições fossem verdadeiras:

* O utilizador é gerido (não federado).
* O utilizador não foi designado uma licença.

> [!NOTE]
> A partir de março de 2019, é permitida a sincronização das alterações da UPN nas contas de utilizadores federadas.
> 

Ativar esta funcionalidade permite ao motor de sincronização atualizar o nome do utilizadorPrincipalName quando este é alterado no local e utiliza a sincronização de haxixe de palavra-passe ou a autenticação de passagem.

Esta funcionalidade está acesa por padrão para os recém-criados diretórios Azure AD. Pode ver se esta funcionalidade está ativada para si executando:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esta funcionalidade não estiver ativada para o seu diretório AD Azure, então pode ative-la executando:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de ativar esta funcionalidade, os valores do nome do utilizador existentes permanecerão como está. Na próxima alteração do atributo UserPrincipalName no local, a sincronização delta normal nos utilizadores irá atualizar a UPN.  

## <a name="see-also"></a>Ver também

* [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md)
* [Integrando as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).