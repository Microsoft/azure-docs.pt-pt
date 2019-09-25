---
title: Resolver alertas do grupo de segurança de rede no Azure AD DS | Microsoft Docs
description: Saiba como solucionar problemas e resolver alertas de configuração de grupo de segurança de rede para Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257993"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas de configuração de rede no Azure Active Directory Domain Services

Para permitir que aplicativos e serviços se comuniquem corretamente com Azure Active Directory Domain Services (AD DS do Azure), as portas de rede específicas devem estar abertas para que o tráfego flua. No Azure, você controla o fluxo de tráfego usando grupos de segurança de rede. O status de integridade de um domínio gerenciado do Azure AD DS mostrará um alerta se as regras do grupo de segurança de rede necessárias não estiverem em vigor.

Este artigo ajuda você a entender e resolver alertas comuns para problemas de configuração do grupo de segurança de rede.

## <a name="alert-aadds104-network-error"></a>AADDS104 de alerta: Erro de rede

### <a name="alert-message"></a>Mensagem de alerta

*A Microsoft não consegue acessar os controladores de domínio para este domínio gerenciado. Isso pode acontecer se um NSG (grupo de segurança de rede) configurado em sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é se há uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.*

Regras de grupo de segurança de rede inválidas são a causa mais comum de erros de rede para o Azure AD DS. O grupo de segurança de rede para a rede virtual deve permitir o acesso a portas e protocolos específicos. Se essas portas estiverem bloqueadas, a plataforma do Azure não poderá monitorar ou atualizar o domínio gerenciado. A sincronização entre o diretório do Azure AD e o domínio gerenciado do Azure AD DS também é afetada. Certifique-se de manter as portas padrão abertas para evitar a interrupção no serviço.

## <a name="default-security-rules"></a>Regras de segurança predefinidas

As regras de segurança de entrada e saída padrão a seguir são aplicadas ao grupo de segurança de rede para um domínio gerenciado do Azure AD DS. Essas regras mantêm o Azure AD DS seguro e permitem que a plataforma do Azure monitore, gerencie e atualize o domínio gerenciado. Você também pode ter uma regra adicional que permita o tráfego de entrada se [Configurar o LDAP seguro][configure-ldaps].

### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Priority | Name | Port | Protocol | Origem | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Any | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | AllVnetInBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | Negar |

### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Priority | Name | Port | Protocol | Origem | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | Internet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | Negar |

>[!NOTE]
> O Azure AD DS precisa de acesso irrestrito de saída da rede virtual. Não recomendamos que você crie regras adicionais que restrinjam o acesso de saída para a rede virtual.

## <a name="verify-and-edit-existing-security-rules"></a>Verificar e editar as regras de segurança existentes

Para verificar as regras de segurança existentes e verificar se as portas padrão estão abertas, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Na página **visão geral** , as regras de segurança de entrada e saída existentes são mostradas.

    Examine as regras de entrada e saída e compare com a lista de regras necessárias na seção anterior. Se necessário, selecione e exclua as regras personalizadas que bloqueiam o tráfego necessário. Se alguma das regras necessárias estiver ausente, adicione uma regra na próxima seção.

    Depois de adicionar ou excluir regras para permitir o tráfego necessário, a integridade do domínio gerenciado AD DS do Azure se atualiza automaticamente em duas horas e remove o alerta.

### <a name="add-a-security-rule"></a>Adicionar uma regra de segurança

Para adicionar uma regra de segurança ausente, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Em **configurações** no painel esquerdo, clique em regras de *segurança de entrada* ou em *regras de segurança de saída* , dependendo de qual regra você precisa adicionar.
1. Selecione **Adicionar**e crie a regra necessária com base na porta, no protocolo, na direção, etc. Quando estiver pronto, selecione **OK**.

Leva alguns minutos para que a regra de segurança seja adicionada e mostrada na lista.

## <a name="next-steps"></a>Passos seguintes

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
