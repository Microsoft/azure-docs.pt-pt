---
title: Solucionar problemas de conexão no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas e erros comuns de entrada de usuário no Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827056"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de conexão de conta com um Azure AD Domain Services domínio gerenciado

Os motivos mais comuns para uma conta de usuário que não podem entrar em um domínio gerenciado do Azure AD DS incluem os seguintes cenários:

* [A conta ainda não está sincronizada com o Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [O Azure AD DS não tem os hashes de senha para permitir que a conta entre.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A conta está bloqueada.](#the-account-is-locked-out)

> [!TIP]
> O Azure AD DS não pode sincronizar em credenciais para contas externas ao locatário do Azure AD. Os usuários externos não podem entrar no domínio gerenciado AD DS do Azure.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A conta ainda não está sincronizada no Azure AD DS

Dependendo do tamanho do seu diretório, pode levar algum tempo para que as contas de usuário e os hashes de credenciais estejam disponíveis no Azure AD DS. Para diretórios grandes, essa sincronização unidirecional inicial do Azure AD pode levar algumas horas e até um ou dois dias. Certifique-se de Aguardar tempo suficiente antes de tentar novamente a autenticação.

Para ambientes híbridos que o usuário Azure AD Connect para sincronizar dados do diretório local no Azure AD, certifique-se de executar a versão mais recente do Azure AD Connect e ter [configurado o Azure ad Connect para executar uma sincronização completa depois de habilitar o Azure AD DS][azure-ad-connect-phs]. Se você desabilitar o Azure AD DS e, em seguida, reabilitar, será necessário seguir estas etapas novamente.

Se você continuar tendo problemas com as contas que não estão sincronizando por meio do Azure AD Connect, reinicie o serviço Azure AD Sync. No computador com Azure AD Connect instalado, abra uma janela de prompt de comando e execute os seguintes comandos:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>O Azure AD DS não tem os hashes de senha

O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena nenhuma credencial de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente esses hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambientes híbridos com sincronização local

Para ambientes híbridos que usam Azure AD Connect para sincronizar de um ambiente de AD DS local, você pode gerar e sincronizar localmente os hashes de senha NTLM ou Kerberos necessários no Azure AD. Depois de criar o domínio gerenciado AD DS do Azure, [habilite a sincronização de hash de senha para Azure Active Directory Domain Services][azure-ad-connect-phs]. Sem concluir esta etapa de sincronização de hash de senha, você não pode entrar em uma conta usando o Azure AD DS. Se você desabilitar o Azure AD DS e, em seguida, reabilitar, será necessário seguir essas etapas novamente.

Para obter mais informações, consulte [como funciona a sincronização de hash de senha para o Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambientes somente de nuvem sem sincronização local

O Azure AD DS domínios gerenciados sem sincronização local, somente contas no Azure AD, também precisam gerar os hashes de senha NTLM ou Kerberos necessários. Se uma conta somente em nuvem não puder entrar, um processo de alteração de senha será concluído com êxito para a conta depois de habilitar o Azure AD DS?

* **Não, a senha não foi alterada.**
    * [Altere a senha da conta][enable-user-accounts] para gerar os hashes de senha necessários e aguarde 15 minutos antes de tentar entrar novamente.
    * Se você desabilitar o Azure AD DS e, em seguida, reabilitar, cada conta deverá seguir as etapas novamente para alterar a senha e gerar os hashes de senha necessários.
* **Sim, a senha foi alterada.**
    * Tente entrar usando o formato *UPN* , como `driley@contoso.com`, em vez do formato *sAMAccountName* como `CONTOSO\deeriley`.
    * O *sAMAccountName* pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é o mesmo que outro usuário no domínio gerenciado. É garantido que o formato *UPN* seja exclusivo em um locatário do Azure AD.

## <a name="the-account-is-locked-out"></a>A conta está bloqueada

Uma conta de usuário no Azure AD DS será bloqueada quando um limite definido para tentativas de entrada malsucedidas for atingido. Esse comportamento de bloqueio de conta foi projetado para protegê-lo de tentativas de entrada de força bruta repetidas que podem indicar um ataque digital automatizado.

Por padrão, se houver 5 tentativas de senha inadequadas em 2 minutos, a conta será bloqueada por 30 minutos.

Para obter mais informações e como resolver problemas de bloqueio de conta, consulte [solucionar problemas de bloqueio de conta no Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Passos seguintes

Se você ainda tiver problemas para ingressar sua VM no domínio gerenciado AD DS do Azure, [Encontre ajuda e abra um tíquete de suporte para Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
