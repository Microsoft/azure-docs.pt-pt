---
title: Resolução de problemas de domínio-junta aos Serviços de Domínio AD da Azure Microsoft Docs
description: Saiba como resolver problemas comuns quando tenta aderir a um VM ou ligar uma aplicação aos Serviços de Domínio do Diretório Ativo do Azure e não consegue ligar ou autenticar ao domínio gerido.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 4a472f0d1e31faea6b62eec004543b42e6add4fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039692"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemas de resolução de domínios com um domínio gerido por serviços de domínio do diretório ativo Azure

Quando tentar juntar-se a uma máquina virtual (VM) ou ligar uma aplicação a um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS), poderá obter um erro que não pode fazê-lo. Para resolver problemas de união de domínios, reveja em qual dos seguintes pontos tem um problema:

* Se não receber uma indicação de autenticação, o VM ou a aplicação não pode ligar-se ao domínio gerido Azure AD DS.
    * Comece a resolver [problemas de conectividade para a junção de domínios](#connectivity-issues-for-domain-join).
* Se receber um erro durante a autenticação, a ligação ao domínio gerido é bem sucedida.
    * Comece a resolver [problemas relacionados com credenciais durante a junção de domínios](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemas de conectividade para a junção de domínios

Se o VM não conseguir encontrar o domínio gerido, normalmente existe um problema de ligação de rede ou configuração. Reveja as seguintes etapas de resolução de problemas para localizar e resolver o problema:

1. Certifique-se de que o VM está ligado à mesma rede virtual que o domínio gerido. Caso contrário, o VM não consegue encontrar e ligar-se ao domínio para se juntar.
    * Se o VM não estiver ligado à mesma rede virtual, confirme que o espremiamento de rede virtual ou a ligação VPN estão *Ativos* ou *Ligados* para permitir que o tráfego flua corretamente.
1. Tente pingar o domínio usando o nome de domínio do domínio gerido, tais como `ping aaddscontoso.com` .
    * Se a resposta ao ping falhar, tente verificar os endereços IP para o domínio apresentado na página geral do portal para o seu domínio gerido, tais como `ping 10.0.0.4` .
    * Se conseguir verificar com sucesso o endereço IP, mas não o domínio, o DNS pode estar configurado incorretamente. Certifique-se de que configura os [servidores DNS de domínio gerido para a rede virtual][configure-dns].
1. Tente lavar a cache de resolver DNS na máquina virtual, tal como `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Configuração do Grupo de Segurança de Rede (NSG)

Quando cria um domínio gerido, um grupo de segurança de rede também é criado com as regras apropriadas para um funcionamento de domínio bem sucedido. Se editar ou criar regras adicionais de grupo de segurança de rede, poderá bloquear involuntariamente as portas necessárias para que o Azure AD DS forneça serviços de ligação e autenticação. Estas regras do grupo de segurança de rede podem causar problemas como a sincronização de passwords que não está a completar, os utilizadores não poderem iniciar sedições ou problemas de ligação ao domínio.

Se continuar a ter problemas de ligação, reveja as seguintes etapas de resolução de problemas:

1. Verifique o estado de saúde do seu domínio gerido no portal Azure. Se tiver um alerta para *o AADDS001,* uma regra do grupo de segurança da rede está a bloquear o acesso.
1. Reveja as [regras necessárias para os portos e o grupo de segurança da rede.][network-ports] Certifique-se de que nenhuma regra do grupo de segurança de rede é aplicada à VM ou à rede virtual que está a ligar a partir do bloqueio destas portas de rede.
1. Uma vez resolvidos quaisquer problemas de configuração do grupo de segurança de rede, o alerta *AADDS001* desaparece da página de saúde em cerca de 2 horas. Com a conectividade da rede agora disponível, tente voltar a juntar-se ao VM.

## <a name="credentials-related-issues-during-domain-join"></a>Questões relacionadas com credenciais durante a união de domínios

Se receber uma caixa de diálogo que pede credenciais para se juntar ao domínio gerido, o VM é capaz de se ligar ao domínio utilizando a rede virtual Azure. O processo de união de domínios falha na autenticação do domínio ou na autorização para completar o processo de união de domínios utilizando as credenciais que fornecem.

Para resolver problemas relacionados com as credenciais, reveja as seguintes etapas de resolução de problemas:

1. Tente utilizar o formato UPN para especificar credenciais, tais como `dee@contoso.onmicrosoft.com` . Certifique-se de que este UPN está configurado corretamente no Azure AD.
    * O *NOME SAMAccount* para a sua conta pode ser autogerido se houver vários utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for excessivamente longo. Portanto, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.
1. Tente usar as credenciais para uma conta de utilizador que faz parte do domínio gerido para juntar VMs ao domínio gerido.
1. Certifique-se de que [ativou a sincronização da palavra-passe][enable-password-sync] e esperou o tempo suficiente para que a sincronização inicial da palavra-passe se completasse.

## <a name="next-steps"></a>Passos seguintes

Para obter uma compreensão mais profunda dos processos do Ative Directory como parte da operação de união de domínios, consulte as questões de [Junção e autenticação][join-authentication-issues].

Se ainda tiver problemas em juntar o seu VM ao domínio gerido, [encontre ajuda e abra um bilhete de apoio para o Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
