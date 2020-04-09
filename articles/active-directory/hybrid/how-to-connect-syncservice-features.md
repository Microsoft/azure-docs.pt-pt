---
title: Funcionalidades e configuração do serviço de sincronização Azure AD Connect Microsoft Docs
description: Descreve as funcionalidades do lado do serviço para o serviço de sincronização Azure AD Connect.
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
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3f6b698922440c6e3e9b488cca93ca8d98d9c59
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983080"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funcionalidades do serviço de sincronização do Azure AD Connect

A funcionalidade de sincronização do Azure AD Connect tem dois componentes:

* O componente no local denominado **Azure AD Connect sync,** também chamado **motor de sincronização.**
* O serviço residente em Azure AD também conhecido como serviço de **sincronização Azure AD Connect**

Este tópico explica como as seguintes funcionalidades do serviço de **sincronização Azure AD Connect** funcionam e como pode configurá-las utilizando o Windows PowerShell.

Estas definições são configuradas pelo [Módulo de Diretório Ativo Azure para windows PowerShell](https://aka.ms/aadposh). Descarregue e instale-o separadamente a partir do Azure AD Connect. Os cmdlets documentados neste tema foram introduzidos no lançamento de março de [2016 (construção 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou se não produzirem o mesmo resultado, certifique-se de que executa a versão mais recente.

Para ver a configuração no seu diretório Azure AD, corra `Get-MsolDirSyncFeatures`.  
![Resultado get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Muitas destas definições só podem ser alteradas pelo Azure AD Connect.

As seguintes definições `Set-MsolDirSyncFeature`podem ser configuradas por:

| DirSyncFeature | Comentário |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Permite que os objetos se juntem ao userPrincipalName para além do endereço SMTP primário. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Permite ao motor sincronizado atualizar o atributo do userPrincipalName para utilizadores geridos/licenciados (não federados). |

Depois de ter ativado uma funcionalidade, não pode voltar a ser desativada.

> [!NOTE]
> A partir de 24 de agosto de 2016, a funcionalidade *Duplicate atribui a resiliência* é ativada por padrão para novos diretórios da AD Azure. Esta funcionalidade também será lançada e ativada em diretórios criados antes desta data. Receberá uma notificação por e-mail quando o seu diretório estiver prestes a ativar esta funcionalidade.
> 
> 

As seguintes definições são configuradas pelo Azure `Set-MsolDirSyncFeature`AD Connect e não podem ser modificadas por:

| DirSyncFeature | Comentário |
| --- | --- |
| DispositivoWriteback |[Azure AD Connect: Habilitar a reescrita do dispositivo](how-to-connect-device-writeback.md) |
| Extensões de Diretório |[Sincronização Azure AD Connect: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicadoProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Permite que um atributo seja colocado em quarentena quando é uma duplicação de outro objeto em vez de falhar todo o objeto durante a exportação. |
| Sincronização hash de palavra-passe |[Implementação de sincronização de hash de senha com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Autenticação pass-through|[Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)|
| UnificadoGroupWriteback |[Pré-visualização: Reprodução do grupo](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Não apoiado atualmente. |

## <a name="duplicate-attribute-resiliency"></a>Duplicar a resiliência do atributo

Em vez de não fornecer objetos com UPNs/proxyAddresss duplicados, o atributo duplicado é "colocado em quarentena" e é atribuído um valor temporário. Quando o conflito é resolvido, a UPN temporária é alterada automaticamente para o valor adequado. Para mais detalhes, consulte a [sincronização da identidade e duplicado a resiliência do atributo.](how-to-connect-syncservice-duplicate-attribute-resiliency.md)

## <a name="userprincipalname-soft-match"></a>UserPrincipalName soft match

Quando esta funcionalidade está ativada, o soft-match está ativado para a UPN para além do [endereço SMTP primário](https://support.microsoft.com/kb/2641663), que está sempre ativado. O soft-match é usado para combinar os utilizadores de nuvem existentes em Azure AD com utilizadores no local.

Se precisa de combinar contas AD no local com contas existentes criadas na nuvem e não estiver a utilizar o Exchange Online, então esta funcionalidade é útil. Neste cenário, geralmente não tem uma razão para definir o atributo SMTP na nuvem.

Esta funcionalidade está em predefinição para os diretórios Azure AD recém-criados. Pode ver se esta funcionalidade está ativada para si executando:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esta funcionalidade não estiver ativada para o seu diretório Azure AD, então pode capacitá-la executando:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar as atualizações do UserPrincipalName

Historicamente, as atualizações ao atributo UserPrincipalName utilizando o serviço de sincronização a partir do local foram bloqueadas, a menos que ambas as condições fossem verdadeiras:

* O utilizador é gerido (não federado).
* O utilizador não foi atribuído a uma licença.

> [!NOTE]
> A partir de março de 2019, é permitida a sincronização das alterações da UPN para contas de utilizadores federados.
> 

Ativar esta funcionalidade permite ao motor sincronizado atualizar o userPrincipalName quando este é alterado no local e utiliza a sincronização de hash de palavra-passe ou a autenticação pass-through.

Esta funcionalidade está em predefinição para os diretórios Azure AD recém-criados. Pode ver se esta funcionalidade está ativada para si executando:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esta funcionalidade não estiver ativada para o seu diretório Azure AD, então pode capacitá-la executando:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de ativar esta funcionalidade, os valores do userPrincipalName existentes permanecerão como e-e-is. Na próxima alteração do atributo do userPrincipalName no local, a sincronização delta normal dos utilizadores irá atualizar a UPN.  

## <a name="see-also"></a>Consulte também

* [Sincronização azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrando as suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
