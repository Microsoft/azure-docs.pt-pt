---
title: 'Azure Active Directory Domain Services: Ativar a sincronização de hash de palavra-passe | Documentos da Microsoft'
description: Introdução aos Serviços de Domínio do Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 734e4bad970229ea8eb770d586062b098e586353
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234900"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Ativar a sincronização de hash de palavras-passe com o Azure Active Directory Domain Services
Em tarefas anteriores, ativou o Azure Active Directory Domain Services do seu inquilino do Azure Active Directory (Azure AD). A tarefa seguinte consiste em ativar a sincronização de hashes de palavras-passe necessária para a autenticação NT LAN Manager (NTLM) e Kerberos com os Serviços de Domínio do Azure AD. Assim que a sincronização de hash de palavras-passe estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os passos envolvidos são diferentes para contas de utilizador apenas na cloud vs contas de utilizador que são sincronizadas a partir do seu diretório no local com o Azure AD Connect. 

<br>

| **Tipo de conta de utilizador** | **Passos a realizar** |
| --- |---|
| **Contas de utilizador da cloud criadas no Azure AD** |**&#x2713;** [Siga as instruções neste artigo](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Contas de utilizador sincronizadas a partir de um diretório no local** |**&#x2713;** : [sincronizar as hashes de palavras-passe das contas de utilizador sincronizadas a partir do seu AD no local com o seu domínio gerido](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Poderá ter de concluir ambos os conjuntos de passos.**
> Se o seu inquilino do Azure AD tiver uma combinação de utilizadores apenas na cloud e utilizadores do seu AD no local, tem de executar ambos os conjuntos de passos.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Tarefa 5: ativar a sincronização de hashes de palavras-passe do seu domínio gerido para contas de utilizador apenas na cloud
Para autenticar os utilizadores no domínio gerido, o Azure Active Directory Domain Services precisa de hashes de palavras-passe num formato adequado para autenticação NTLM e Kerberos. O Azure AD não gera nem armazena hashes de palavras-passe no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure Active Directory Domain Services para o seu inquilino. Por motivos óbvios de segurança, o Azure AD também não armazena quaisquer credenciais de palavras-passes no formato de texto não encriptado. Por isso, o Azure AD não tem uma forma de gerar automaticamente estes hashes de palavras-passe NTLM ou Kerberos, com base nas credenciais existentes dos utilizadores.

> [!NOTE]
> **Se a sua organização tiver contas de utilizador apenas na cloud, todos os utilizadores que precisam de utilizar o Azure Active Directory Domain Services têm de alterar as respetivas palavras-passe.** Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Essas contas de utilizador não são sincronizadas a partir de um diretório no local.
>
>

Este processo de alteração de palavras-passe faz com que os hashes de palavras-passe necessários pelo Azure Active Directory Domain Services para a autenticação Kerberos e NTLM sejam gerados no Azure AD. Pode optar por expirar as palavras-passe para todos os utilizadores no inquilino que necessitam de utilizar o Azure Active Directory Domain Services ou instruí-los para alterarem as palavras-passe.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Ativar a geração de hashes de palavras-passe NTLM e Kerberos para uma conta de utilizador apenas na cloud
Seguem-se as instruções que tem de fornecer aos utilizadores, para que possam alterar as respetivas palavras-passe:

1. Aceda à página do [Painel de Acesso do Azure AD](https://myapps.microsoft.com) para a sua organização.

    ![Iniciar o painel de acesso do Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. No canto superior direito, clique no seu nome e selecione **Perfil** no menu.

    ![Selecionar perfil](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Na página **Perfil**, clique em **Alterar palavra-passe**.

    ![Clicar em "Alterar palavra-passe"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Se a opção **Alterar palavra-passe** não estiver apresentada na página do Painel de Acesso, certifique-se de que a sua organização configurou a [gestão de palavras-passe no Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. Na página **Alterar palavra-passe**, escreva a palavra-passe existente (antiga), escreva uma palavra-passe nova e, em seguida, confirme-a.

    ![O utilizador altera a palavra-passe](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Clique em **Submeter**.

Uns minutos depois de ter alterado a palavra-passe, a nova palavra-passe pode ser utilizada no Azure Active Directory Domain Services. Após cerca de 20 minutos, pode iniciar sessão em computadores associados ao domínio gerido com a palavra-passe recentemente alterada.

## <a name="related-content"></a>Conteúdo relacionado
* [Como atualizar a sua própria palavra-passe](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
* [Introdução à Gestão de Palavras-passe no Azure AD](../active-directory/authentication/quickstart-sspr.md)
* [Ativar a sincronização de hashes de palavras-passe com o Azure Active Directory Domain Services de um inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Gerir um domínio do Azure AD Domain Services](manage-domain.md)
* [Associar uma máquina virtual do Windows a um domínio gerido do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
