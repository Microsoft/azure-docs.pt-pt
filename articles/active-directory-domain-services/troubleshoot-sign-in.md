---
title: Sinal de resolução de problemas em problemas nos Serviços de Domínio da AD Azure  Microsoft Docs
description: Saiba como resolver o sinal de utilizador comum em problemas e erros nos Serviços de Domínio de Diretório Ativo Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612739"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemas de entrada na conta com um domínio gerido pelos Serviços de Domínio Da Azure AD

As razões mais comuns para uma conta de utilizador que não pode iniciar sessão num domínio gerido pelo Azure AD DS incluem os seguintes cenários:

* [A conta ainda não está sincronizada no Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [O Azure AD DS não tem a palavra-passe hashes para deixar a conta entrar.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A conta está bloqueada.](#the-account-is-locked-out)

> [!TIP]
> A Azure AD DS não pode sincronizar em credenciais para contas externas ao inquilino da AD Azure. Os utilizadores externos não podem iniciar sessão no domínio gerido pelo Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A conta ainda não está sincronizada no Azure AD DS

Dependendo do tamanho do seu diretório, pode demorar algum tempo para que as contas dos utilizadores e hashes credenciais estejam disponíveis no Azure AD DS. Para grandes diretórios, esta sincronização inicial de ida do Azure AD pode demorar algumas horas, e até um dia ou dois. Certifique-se de que espera o suficiente para voltar a tentar a autenticação.

Para ambientes híbridos que o utilizador Azure AD Connect sincronize os dados de diretório no local no Azure AD, certifique-se de que executa a versão mais recente do Azure AD Connect e configurou o [Azure AD Connect para realizar uma sincronização completa após ativar o Azure AD DS][azure-ad-connect-phs]. Se desativar o Azure AD DS e, em seguida, voltar a ativar, tem de seguir estes passos novamente.

Se continuar a ter problemas com contas que não sincronizam através do Azure AD Connect, reinicie o Serviço de Sincronização Azure AD. A partir do computador com Azure AD Connect instalado, abra uma janela de comando rápida e execute os seguintes comandos:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS não tem as hashes de senha

A Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena credenciais de senha em formato de texto claro. Portanto, a Azure AD não pode gerar automaticamente estas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Ambientes híbridos com sincronização no local

Para ambientes híbridos que utilizam o Azure AD Connect para sincronizar a partir de um ambiente AD DS no local, pode gerar e sincronizar localmente as hashes de senha NTLM ou Kerberos necessárias para o Azure AD. Depois de criar o seu domínio gerido pelo Azure AD DS, ative a sincronização de hash de [palavra-passe para os Serviços de Domínio de Diretório Ativo Azure][azure-ad-connect-phs]. Sem completar este passo de sincronização de hash de palavra-passe, não pode iniciar sessão numa conta utilizando O DS Azure. Se desativar o Azure AD DS e, em seguida, voltar a ativar, terá de seguir esses passos novamente.

Para mais informações, consulte como funciona a [sincronização de hash de palavra-passe para O DS Azure AD][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Ambientes só para nuvens sem sincronização no local

O Azure AD DS geriu domínios sem sincronização no local, apenas contas em Azure AD, também precisam de gerar os hashes de senha NTLM ou Kerberos necessários. Se uma conta apenas na nuvem não conseguir iniciar sessão, tem um processo de alteração de senha concluído com sucesso para a conta depois de ativar o Azure ADS?

* **Não, a senha não foi alterada.**
    * [Altere a palavra-passe para a conta][enable-user-accounts] para gerar as hashes de senha necessárias e, em seguida, aguarde 15 minutos antes de tentar iniciar sessão novamente.
    * Se desativar o Azure AD DS e, em seguida, voltar a ativar, cada conta deve seguir novamente os passos para alterar a sua palavra-passe e gerar as hashes de senha necessárias.
* **Sim, a senha foi alterada.**
    * Tente iniciar sessão utilizando o formato *UPN,* como `driley@aaddscontoso.com`, em vez do formato *SAMAccountName* como `AADDSCONTOSO\deeriley`.
    * O *Nome SAMAccountPode* ser gerado automaticamente para utilizadores cujo prefixo UPN seja excessivamente longo ou seja o mesmo que outro utilizador no domínio gerido. O formato *UPN* é garantido ser único dentro de um inquilino Azure AD.

## <a name="the-account-is-locked-out"></a>A conta está bloqueada.

Uma conta de utilizador em Azure AD DS é bloqueada quando um limiar definido para tentativas de entrada mal sucedidas foi cumprido. Este comportamento de bloqueio de conta foi concebido para protegê-lo de tentativas repetidas de sinal de força bruta que podem indicar um ataque digital automatizado.

Por predefinição, se houver 5 tentativas de senha em 2 minutos, a conta fica bloqueada durante 30 minutos.

Para obter mais informações e como resolver problemas de bloqueio de conta, consulte problemas de bloqueio de [conta troubleshoot em Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas em juntar o seu VM ao domínio gerido pelo Azure AD DS, encontre ajuda e abra um bilhete de apoio para o [Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
