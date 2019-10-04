---
title: Solucionar problemas de ingresso no domínio com Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas comuns ao tentar ingressar em um domínio uma VM ou conectar um aplicativo a Azure Active Directory Domain Services e você não pode se conectar ou autenticar no domínio gerenciado.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 1016fbc1478ec713d50a2f04bcc80d08288b03f3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827231"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de ingresso no domínio com um Azure AD Domain Services domínio gerenciado

Ao tentar ingressar em uma VM (máquina virtual) ou conectar um aplicativo a um domínio gerenciado Azure Active Directory Domain Services (AD DS), você poderá receber um erro informando que não é possível fazer isso. Para solucionar problemas de ingresso no domínio, examine em quais dos seguintes pontos você tem um problema:

* Se você não receber um prompt de autenticação, a VM ou o aplicativo não poderá se conectar ao domínio gerenciado AD DS do Azure.
    * Comece a solucionar [problemas de conectividade para ingressar no domínio](#connectivity-issues-for-domain-join).
* Se você receber um erro durante a autenticação, a conexão com o domínio gerenciado AD DS do Azure será bem-sucedida.
    * Comece a solucionar problemas [relacionados a credenciais durante o ingresso no domínio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemas de conectividade para ingresso no domínio

Se a VM não conseguir localizar o domínio gerenciado AD DS do Azure, normalmente haverá uma conexão de rede ou um problema de configuração. Examine as seguintes etapas de solução de problemas para localizar e resolver o problema:

1. Verifique se a VM está conectada à mesma rede virtual, ou emparelhada, que está habilitada para o Azure AD DS. Caso contrário, a VM não poderá localizar e se conectar ao domínio para ingressar.
    * Se a VM não estiver conectada à mesma rede virtual, confirme se o emparelhamento de rede virtual ou a conexão VPN está *ativa* ou *conectada* para permitir que o tráfego flua corretamente.
1. Tente executar o ping no domínio usando o nome de domínio do domínio gerenciado AD DS do Azure, como `ping contoso.com`.
    * Se a resposta de ping falhar, tente executar ping nos endereços IP para o domínio exibido na página Visão geral no portal para seu domínio gerenciado AD DS do Azure, como `ping 10.0.0.4`.
    * Se você puder executar o ping com êxito no endereço IP, mas não no domínio, o DNS poderá estar configurado incorretamente. Verifique se você configurou os servidores DNS de domínio gerenciado AD DS do Azure para a rede virtual.
1. Tente liberar o cache do resolvedor de DNS na máquina virtual, como `ipconfig /flushdns`.

### <a name="network-security-group-nsg-configuration"></a>Configuração do NSG (grupo de segurança de rede)

Quando você cria um domínio gerenciado do Azure AD DS, um grupo de segurança de rede também é criado com as regras apropriadas para uma operação de domínio bem-sucedida. Se você editar ou criar regras de grupo de segurança de rede adicionais, poderá bloquear involuntariamente as portas necessárias para o AD DS do Azure fornecer serviços de conexão e autenticação. Essas regras de grupo de segurança de rede podem causar problemas como a sincronização de senha não ser concluída, os usuários não conseguem entrar ou problemas de ingresso no domínio.

Se você continuar tendo problemas de conexão, examine as seguintes etapas de solução de problemas:

1. Verifique o status de integridade do seu domínio gerenciado AD DS do Azure no portal do Azure. Se você tiver um alerta para *AADDS001*, uma regra de grupo de segurança de rede estará bloqueando o acesso.
1. Examine as [portas necessárias e as regras do grupo de segurança de rede][network-ports]. Certifique-se de que nenhuma regra de grupo de segurança de rede aplicada à VM ou à rede virtual à qual você está se conectando bloqueie essas portas de rede.
1. Depois que qualquer problema de configuração do grupo de segurança de rede for resolvido, o alerta *AADDS001* desaparece da página de integridade em cerca de 2 horas. Com a conectividade de rede agora disponível, tente adicionar o domínio à VM novamente.

## <a name="credentials-related-issues-during-domain-join"></a>Problemas relacionados a credenciais durante o ingresso no domínio

Se você receber uma caixa de diálogo que solicita credenciais para ingressar no domínio gerenciado AD DS do Azure, a VM será capaz de se conectar ao domínio usando a rede virtual do Azure. O processo de ingresso no domínio falha ao autenticar no domínio ou autorização para concluir o processo de ingresso no domínio usando as credenciais fornecidas.

Para solucionar problemas relacionados a credenciais, examine as seguintes etapas de solução de problemas:

1. Tente usar o formato UPN para especificar as credenciais, como `dee@contoso.onmicrosoft.com`. Verifique se esse UPN está configurado corretamente no Azure AD.
    * O *sAMAccountName* para sua conta pode ser gerado automaticamente se houver vários usuários com o mesmo prefixo UPN em seu locatário ou se o prefixo UPN for muito longo. Portanto, o formato *sAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.
1. Tente usar as credenciais de uma conta de usuário que pertença ao grupo de *Administradores de DC do AAD* para ingressar VMs no domínio gerenciado AD DS do Azure.
1. Certifique-se de que você [habilitou a sincronização de senha][enable-password-sync] e esperou tempo suficiente para a conclusão da sincronização de senha inicial.

## <a name="next-steps"></a>Passos seguintes

Para obter uma compreensão mais profunda dos processos de Active Directory como parte da operação de ingresso no domínio, consulte [problemas de ingresso e autenticação][join-authentication-issues].

Se você ainda tiver problemas para ingressar sua VM no domínio gerenciado AD DS do Azure, [Encontre ajuda e abra um tíquete de suporte para Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
