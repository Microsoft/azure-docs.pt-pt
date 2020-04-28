---
title: Troubleshoot domain-join com Azure AD Domain Services [ Microsoft Docs
description: Aprenda a resolver problemas comuns quando tenta juntar-se a um VM ou ligar uma aplicação aos Serviços de Domínio de Diretório Ativo Azure e não pode ligar ou autenticar o domínio gerido.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299113"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Problemas de resolução de problemas de união de domínio com um domínio gerido pela Azure AD Domain Services

Quando tentar juntar-se a uma máquina virtual (VM) ou ligar uma aplicação a um domínio gerido pelo Azure Ative Directory Services (AD DS), poderá ter um erro que não consegue fazê-lo. Para resolver problemas de união de domínios, reveja em qual dos seguintes pontos tem um problema:

* Se não receber um pedido de autenticação, o VM ou aplicação não pode ligar-se ao domínio gerido pelo Azure AD DS.
    * Comece a resolver problemas de [conectividade para a adesão ao domínio.](#connectivity-issues-for-domain-join)
* Se receber um erro durante a autenticação, a ligação ao domínio gerido pelo Azure AD DS é bem sucedida.
    * Comece a resolver [problemas relacionados com credenciais durante a adesão ao domínio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemas de conectividade para a adesão ao domínio

Se o VM não conseguir encontrar o domínio gerido pelo Azure AD DS, normalmente existe um problema de ligação ou configuração de rede. Reveja as seguintes medidas de resolução de problemas para localizar e resolver o problema:

1. Certifique-se de que o VM está ligado à mesma rede virtual, ou a uma rede virtual esparsada que está ativada para O DS Azure. Caso contrário, o VM não consegue encontrar e ligar-se ao domínio para se juntar.
    * Se o VM não estiver ligado à mesma rede virtual, confirme que o peering virtual de rede ou ligação VPN é *Ativo* ou *Ligado* para permitir que o tráfego flua corretamente.
1. Tente pingar o domínio usando o nome de domínio do domínio `ping aaddscontoso.com`gerido azure AD DS, tais como .
    * Se a resposta de ping falhar, tente pingar os endereços IP para o domínio apresentado na página de visão `ping 10.0.0.4`geral no portal para o seu domínio gerido pelo Azure AD DS, como .
    * Se conseguir obter o acesso ao endereço IP com sucesso, mas não o domínio, o DNS pode estar incorretamente configurado. Certifique-se de que configura os servidores DNS geridos pelo Azure AD DNs para a rede virtual.
1. Tente lavar a cache de resolver DNS `ipconfig /flushdns`na máquina virtual, como .

### <a name="network-security-group-nsg-configuration"></a>Configuração do Grupo de Segurança da Rede (NSG)

Quando cria um domínio gerido pelo Azure AD DS, é também criado um grupo de segurança de rede com as regras adequadas para uma operação de domínio bem sucedida. Se editar ou criar regras adicionais de grupo de segurança de rede, poderá bloquear involuntariamente as portas necessárias para que o Azure AD DS forneça serviços de ligação e autenticação. Estas regras do grupo de segurança da rede podem causar problemas como a sincronização de passwords não estar a ser concluída, os utilizadores não poderem iniciar sessão ou problemas de adesão ao domínio.

Se continuar a ter problemas de ligação, reveja as seguintes etapas de resolução de problemas:

1. Verifique o estado de saúde do seu domínio gerido azure AD DS no portal Azure. Se tiver um alerta para *a AADDS001,* uma regra do grupo de segurança da rede está a bloquear o acesso.
1. Reveja as regras exigidas para os [portos e as regras][network-ports]do grupo de segurança da rede. Certifique-se de que não são aplicadas regras do grupo de segurança da rede aplicadas ao VM ou à rede virtual que está a ligar a partir de bloquear estas portas de rede.
1. Uma vez resolvidos quaisquer problemas de configuração do grupo de segurança da rede, o alerta *AADDS001* desaparece da página de saúde em cerca de 2 horas. Com a conectividade da rede agora disponível, tente juntar-se novamente ao VM.

## <a name="credentials-related-issues-during-domain-join"></a>Questões relacionadas com credenciais durante a adesão ao domínio

Se obtém uma caixa de diálogo que pede credenciais para aderir ao domínio gerido pelo Azure AD DS, o VM é capaz de se ligar ao domínio utilizando a rede virtual Azure. O processo de união de domínio falha na autenticação ao domínio ou autorização para completar o processo de adesão ao domínio utilizando as credenciais.

Para resolver problemas relacionados com credenciais, reveja os seguintes passos de resolução de problemas:

1. Tente utilizar o formato UPN `dee@aaddscontoso.onmicrosoft.com`para especificar credenciais, tais como . Certifique-se de que esta UPN está corretamente configurada em Azure AD.
    * O *Nome SAMAccountName* para a sua conta pode ser autogerado se houver vários utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for excessivamente longo. Por isso, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.
1. Tente usar as credenciais para uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS para se juntar aos VMs ao domínio gerido.
1. Certifique-se de que ativou a sincronização da [palavra-passe][enable-password-sync] e esperou o suficiente para que a sincronização inicial da palavra-passe esteja concluída.

## <a name="next-steps"></a>Passos seguintes

Para uma compreensão mais profunda dos processos de Diretório Ativo como parte da operação de associação de domínios, consulte questões de [adesão e autenticação.][join-authentication-issues]

Se ainda tiver problemas em juntar o seu VM ao domínio gerido pelo Azure AD DS, encontre ajuda e abra um bilhete de apoio para o [Azure Ative Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
