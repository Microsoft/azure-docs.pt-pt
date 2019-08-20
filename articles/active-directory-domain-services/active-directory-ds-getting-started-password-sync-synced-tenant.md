---
title: 'Azure AD Domain Services: Habilitar a sincronização de senha | Microsoft Docs'
description: Introdução aos Serviços de Domínio do Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 0b38bdba0d88da9296106411737c280dcf6d5df1
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613182"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Ativar a sincronização de hash de palavras-passe com o Azure Active Directory Domain Services

Um inquilino do Azure AD sincronizado é definido para sincronizar com o diretório local da sua organização com o Azure AD Connect. Por predefinição, o Azure AD Connect não faz a sincronização de hashes de credenciais NTLM e Kerberos com o Azure AD. Para utilizar os Serviços de Domínio do Azure AD, tem de configurar o Azure AD Connect para sincronizar os hashes de credenciais necessários para a autenticação NTLM e Kerberos. Os passos seguintes ativam a sincronização dos hashes de credenciais necessários do seu diretório no local com o seu inquilino do Azure AD.

> [!NOTE]
> **Se a organização tiver contas de utilizador que são sincronizadas a partir do seu diretório no local, tem de ativar a sincronização de hashes NTLM e Kerberos para poder utilizar o domínio gerido.** Uma conta de utilizador sincronizada é uma conta que foi criada no seu diretório no local e está sincronizada com o seu inquilino do Azure AD através do Azure AD Connect. A sincronização de hash de senha é um processo diferente da sincronização de usuário/objeto. Você precisa desabilitar/habilitar a sincronização de hash de senha para obter a sincronização de senha completa e, em seguida, verá as atualizações de hash de senha do lote no log de eventos do aplicativo.

Neste artigo, você habilita a sincronização de hashes de credenciais necessárias para a autenticação Kerberos e NTLM (NT LAN Manager) para Azure AD Domain Services. Assim que a sincronização de credenciais estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os passos envolvidos são diferentes para contas de utilizador apenas na cloud vs contas de utilizador que são sincronizadas a partir do seu diretório no local com o Azure AD Connect.

| **Tipo de conta de utilizador** | **Passos a realizar** |
| --- | --- |
| **Contas de utilizador sincronizadas a partir de um diretório no local** |**&#x2713;** [Siga as instruções neste artigo](active-directory-ds-getting-started-password-sync-synced-tenant.md#install-or-update-azure-ad-connect) |
| **Contas de utilizador da cloud criadas no Azure AD** |**&#x2713;** [Sincronizar as palavras-passe para contas de utilizador apenas na cloud com o seu domínio gerido](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) |

> [!TIP]
> **Poderá ter de concluir ambos os conjuntos de passos.**
> Se o seu inquilino do Azure AD tiver uma combinação de utilizadores apenas na cloud e utilizadores do seu AD no local, tem de executar ambos os conjuntos de passos.

## <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar o Azure AD Connect

Instale a versão mais recente recomendada do Azure AD Connect num computador associado a um domínio. Se tiver uma instância existente do programa de configuração do Azure AD Connect, terá de atualizá-la para utilizar a versão mais recente do Azure AD Connect. Para evitar problemas/erros conhecidos que possam já ter sido corrigidos, utilize sempre a versão mais recente do Azure AD Connect.

**[Transferir o Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)**

Versão recomendada: **1.1.614.0** – publicado em 5 de setembro de 2017.

> [!WARNING]
> TEM de instalar a versão mais recente recomendada do Azure AD Connect para permitir que as credenciais de palavra-passe legadas (necessárias para a autenticação NTLM e Kerberos) sejam sincronizadas com o seu inquilino do Azure AD. Esta funcionalidade não está disponível em versões anteriores do Azure AD Connect ou com a ferramenta DirSync legada.

Instruções de instalação para o Azure AD Connect estão disponíveis no seguinte artigo - [Introdução ao Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md)

## <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Ativar a sincronização de hashes de credencial de NTLM e Kerberos para o Azure AD

Execute o seguinte script do PowerShell em cada floresta do AD. O script permite que os hashes de palavra-passe NTLM e Kerberos de todos os utilizadores locais sejam sincronizados com o seu inquilino do Azure AD. O script também inicia uma sincronização completa no Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
```

Dependendo do tamanho do seu diretório (número de utilizadores, grupos, etc.), a sincronização de credenciais com o Azure AD é demorada. As palavras-passe poderão ser usadas no domínio gerido dos Serviços de Domínio do Azure AD pouco tempo depois dos hashes de credencial serem sincronizados para o Azure AD.

## <a name="next-steps"></a>Passos Seguintes

> [!NOTE]
> A **sincronização de hash de senha** é um processo diferente da sincronização de usuário/objeto. Você precisa desabilitar/habilitar a sincronização de hash de senha para obter a sincronização de senha completa e, em seguida, verá as atualizações de hash de senha do lote no log de eventos do aplicativo.

* [Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um diretório do Azure AD apenas na nuvem](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
* [Gerenciar um domínio de Azure AD Domain Services](tutorial-create-management-vm.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
