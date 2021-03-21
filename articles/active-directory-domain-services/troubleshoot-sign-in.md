---
title: Sinal de resolução de problemas em problemas em Azure AD Domain Services | Microsoft Docs
description: Saiba como resolver problemas comuns de inscrição do utilizador e erros nos Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618574"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemas de sessão de conta de resolução de problemas com um domínio gerido por serviços de domínio do diretório ativo Azure

As razões mais comuns para uma conta de utilizador que não pode entrar num domínio gerido por Azure Ative Directory Domain Services (Azure AD DS) incluem os seguintes cenários:

* [A conta ainda não está sincronizada no Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [A Azure AD DS não tem a palavra-passe para deixar a conta entrar.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A conta está bloqueada.](#the-account-is-locked-out)

> [!TIP]
> A Azure AD DS não pode sincronizar credenciais para contas que são externas ao inquilino da AD Azure. Os utilizadores externos não podem entrar no domínio gerido Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Conta ainda não está sincronizada no Azure AD DS

Dependendo do tamanho do seu diretório, pode demorar algum tempo para que as contas de utilizador e as hashes credenciais estejam disponíveis num domínio gerido. Para grandes diretórios, esta sincronização inicial de ida do AZure AD pode demorar algumas horas, e até um dia ou dois. Certifique-se de que espera o suficiente antes de voltar a tentar a autenticação.

Para ambientes híbridos que o utilizador Azure AD Connect para sincronizar os dados do diretório no local em Azure AD, certifique-se de que executa a versão mais recente do Azure AD Connect e [configurou o Azure AD Connect para realizar uma sincronização completa depois de permitir a Azure AD DS][azure-ad-connect-phs]. Se desativar o Azure AD DS e, em seguida, voltar a ativar, tem de seguir estes passos novamente.

Se continuar a ter problemas com contas que não se sincronizam através do Azure AD Connect, reinicie o Serviço de Sincronização AZure AD. A partir do computador com Azure AD Connect instalado, abra uma janela de pedido de comando e, em seguida, execute os seguintes comandos:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS não tem a palavra-passe hashes

O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena quaisquer credenciais de senha em formato de texto claro. Portanto, o Azure AD não pode gerar automaticamente estes hashes de palavra-passe NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambientes híbridos com sincronização no local

Para ambientes híbridos que utilizem O Azure AD Connect para sincronizar a partir de um ambiente AD DS no local, você pode gerar e sincronizar localmente as hashes de palavra-passe NTLM ou Kerberos necessárias em Azure AD. Depois de criar o seu domínio gerido, [ative a sincronização de hash de palavra-passe para os Serviços de Domínio do Diretório Ativo Azure][azure-ad-connect-phs]. Sem completar este passo de sincronização de hash de palavra-passe, não é possível iniciar singir numa conta utilizando o domínio gerido. Se desativar o Azure AD DS e, em seguida, voltar a ativar, tem de seguir esses passos novamente.

Para obter mais informações, consulte [como funciona a sincronização de haxixe de palavra-passe para a Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambientes só em nuvem sem sincronização no local

Os domínios geridos sem sincronização no local, apenas contas em Azure AD, também precisam de gerar os hashes de palavra-passe NTLM ou Kerberos necessários. Se uma conta apenas na nuvem não puder iniciar scontabilidade, um processo de alteração de palavra-passe foi concluído com sucesso para a conta depois de permitir que o Azure AD DS?

* **Não, a senha não foi alterada.**
    * [Altere a palavra-passe para a conta][enable-user-accounts] para gerar os hashes de senha necessários e, em seguida, aguarde 15 minutos antes de tentar iniciar novamente a scontabilidade.
    * Se desativar o Azure AD DS e, em seguida, voltar a ativar, cada conta deve seguir novamente os passos para alterar a sua palavra-passe e gerar os hashes de palavra-passe necessários.
* **Sim, a senha foi alterada.**
    * Tente iniciar sação utilizando o formato *UPN,* `driley@aaddscontoso.com` como, em vez do formato *SAMAccountName* como `AADDSCONTOSO\deeriley` .
    * O *SAMAccountName* pode ser gerado automaticamente para utilizadores cujo prefixo UPN é excessivamente longo ou é o mesmo que outro utilizador no domínio gerido. O formato *UPN* é garantido ser único dentro de um inquilino AZure AD.

## <a name="the-account-is-locked-out"></a>A conta está bloqueada.

Uma conta de utilizador num domínio gerido é bloqueada quando um limiar definido para tentativas de inscrição mal sucedidas foi atingido. Este comportamento de bloqueio de conta foi concebido para protegê-lo de tentativas de inscrição repetidas de força bruta que podem indicar um ataque digital automatizado.

Por padrão, se houver 5 tentativas de senha estragada em 2 minutos, a conta fica bloqueada durante 30 minutos.

Para obter mais informações e como resolver problemas de bloqueio de conta, consulte [problemas de bloqueio de conta de resolução de problemas em Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas em juntar o seu VM ao domínio gerido, [encontre ajuda e abra um bilhete de apoio para o Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md