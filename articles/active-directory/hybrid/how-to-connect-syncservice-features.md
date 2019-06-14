---
title: Funcionalidades de serviço de sincronização do Azure AD Connect e configuração | Documentos da Microsoft
description: Descreve as funcionalidades do lado do serviço para serviço de sincronização do Azure AD Connect.
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
ms.openlocfilehash: be67a6f287e2d6e77070928cbe12542857696011
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347557"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funcionalidades de serviço de sincronização do Azure AD Connect

A funcionalidade de sincronização do Azure AD Connect tem dois componentes:

* O componente no local com o nome **do Azure AD Connect**, também conhecido como **motor de sincronização**.
* O serviço que residem no Azure AD também conhecido como **serviço de sincronização do Azure AD Connect**

Este tópico explica como as seguintes funcionalidades do **serviço de sincronização do Azure AD Connect** de trabalho e como pode configurá-las a com o Windows PowerShell.

Estas definições são configuradas através da [módulo Azure Active Directory para Windows PowerShell](https://aka.ms/aadposh). Baixe e instale-o separadamente do Azure AD Connect. Os cmdlets documentados neste tópico foram introduzidos no [versão de Março de 2016 (compilação 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou não produzem o mesmo resultado, em seguida, certifique-se de que executar a versão mais recente.

Para ver a configuração no diretório do Azure AD, execute `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures result](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Muitas destas definições só podem ser alteradas pelo Azure AD Connect.

As seguintes definições podem ser configuradas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Permite que os objetos Junte-se no userPrincipalName, além do endereço SMTP principal. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Permite que o motor de sincronização atualizar o atributo userPrincipalName para utilizadores (não federadas) geridos/licenciados. |

Depois de ter ativado um recurso, não é possível desativar novamente.

> [!NOTE]
> A partir de 24 de Agosto de 2016 a funcionalidade *resiliência de atributos duplicados* está ativada por predefinição para o novo Azure diretórios AD. Esta funcionalidade também será implementada e ativada em diretórios criados antes desta data. Receberá uma notificação por e-mail quando o seu diretório está prestes a se tornar esta funcionalidade ativada.
> 
> 

As seguintes definições são configuradas pelo Azure AD Connect e não pode ser modificadas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Ativar a repetição de escrita do dispositivo](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Sincronização do Azure AD Connect: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Permite que um atributo ser colocado em quarentena quando ele é um duplicado de outro objeto em vez de todo o objeto a falhar durante a exportação. |
| Sincronização hash de palavra-passe |[Implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Autenticação pass-through|[Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Pré-visualização: Repetição de escrita do grupo](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Não são atualmente suportados. |

## <a name="duplicate-attribute-resiliency"></a>Resiliência de atributos duplicados

Em vez de realizar a ativação para aprovisionar os objetos com UPNs duplicados / proxyAddresses, o atributo duplicado foi "colocado em quarentena" e é atribuído um valor temporário. Quando o conflito é resolvido, o UPN temporário é automaticamente alterado para o valor adequado. Para obter mais detalhes, consulte [resiliência de atributos de sincronização e duplicação de identidade](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondência de forma recuperável de UserPrincipalName

Quando esta funcionalidade está ativada, a correspondência de forma recuperável está ativada para UPN além da [endereço SMTP principal](https://support.microsoft.com/kb/2641663), que está sempre ativado. Configuração soft-match é utilizado para corresponder a utilizadores de nuvem existente no Azure AD com utilizadores no local.

Se precisar de correspondência de contas no local AD com as contas existentes criadas na nuvem e não estiver a utilizar Exchange Online, em seguida, esse recurso é útil. Neste cenário, normalmente não têm um motivo para definir o atributo de SMTP na cloud.

Esta funcionalidade está em por predefinição para recentemente criada diretórios do Azure AD. Pode ver se esta funcionalidade está ativada para ao executar:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esta funcionalidade não está ativada para o diretório do Azure AD, pode ativá-la ao executar:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName

Historicamente, as atualizações para o atributo UserPrincipalName com o serviço de sincronização no local foi bloqueado, a menos que ambas estas condições forem verdadeiras:

* O utilizador for gerido (não federadas).
* Não foi atribuído uma licença do utilizador.

Para obter mais detalhes, consulte [nomes de utilizador no Office 365, o Azure ou o Intune não correspondem o UPN no local ou o ID de início de sessão alternativo](https://support.microsoft.com/kb/2523192).

Ativar esta funcionalidade permite que o motor de sincronização atualizar o userPrincipalName quando é alterado no local e utilizar a autenticação de sincronização ou pass-through de hash de palavra-passe. Se usar a Federação, esta funcionalidade não é suportada.

Esta funcionalidade está em por predefinição para recentemente criada diretórios do Azure AD. Pode ver se esta funcionalidade está ativada para ao executar:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esta funcionalidade não está ativada para o diretório do Azure AD, pode ativá-la ao executar:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de ativar esta funcionalidade, os valores de userPrincipalName existentes permanecerão como-é. Na próxima alteração do userPrincipalName atributo no local, a sincronização normal delta em utilizadores atualizará o UPN.  

## <a name="see-also"></a>Consulte também

* [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
