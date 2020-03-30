---
title: Resolver alertas de grupo de segurança de rede em Azure AD DS Microsoft Docs
description: Saiba como resolver problemas e resolver alertas de configuração de grupos de segurança de rede para os Serviços de Domínio do Diretório Ativo do Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257993"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas de configuração da rede nos Serviços de Domínio de Diretório Ativo do Azure

Para permitir que as aplicações e serviços se comuniquem corretamente com os Serviços de Domínio do Diretório Ativo azure (Azure AD DS), portas de rede específicas devem estar abertas para permitir o fluxo de tráfego. Em Azure, controla-se o fluxo de tráfego utilizando grupos de segurança da rede. O estado de saúde de um domínio gerido pela AD DS azure mostra um alerta se as regras exigidas do grupo de segurança da rede não estiverem em vigor.

Este artigo ajuda-o a compreender e a resolver alertas comuns para problemas de configuração do grupo de segurança da rede.

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: Erro da rede

### <a name="alert-message"></a>Mensagem de alerta

*A Microsoft não consegue contactar os controladores de domínio para este domínio gerido. Isto pode acontecer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerido. Outra razão possível é se existe uma rota definida pelo utilizador que bloqueia a entrada de tráfego a partir da internet.*

As regras inválidas do grupo de segurança da rede são a causa mais comum de erros de rede para o Azure AD DS. O grupo de segurança da rede para a rede virtual deve permitir o acesso a portas e protocolos específicos. Se estas portas estiverem bloqueadas, a plataforma Azure não pode monitorizar ou atualizar o domínio gerido. A sincronização entre o diretório Azure AD e o domínio gerido pela AD DS azure também é impactada. Certifique-se de que mantém as portas por defeito abertas para evitar interrupções no serviço.

## <a name="default-security-rules"></a>Regras de segurança predefinidas

As seguintes regras de segurança de entrada e saída são aplicadas ao grupo de segurança da rede para um domínio gerido pelo Azure AD DS. Estas regras mantêm o Azure AD DS seguro e permitem que a plataforma Azure monitorize, gere e atualize o domínio gerido. Também pode ter uma regra adicional que permite o tráfego de entrada se [configurar o LDAP seguro][configure-ldaps].

### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Prioridade | Nome | Porta | Protocolo | Origem | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 101      | PermitirSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Qualquer | Permitir |
| 201      | PermitirRD | 3389 | TCP | Corpnetsaw | Qualquer | Permitir |
| 301      | Permitiro Remoting | 5986| TCP | AzureActiveDirectoryDomainServices | Qualquer | Permitir |
| 65000    | AllVnetInBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Permitir |
| 65001    | AllowAzureLoadBalancerInBound | Qualquer | Qualquer | AzureLoadBalancer | Qualquer | Permitir |
| 65500    | DenyAllInBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Prioridade | Nome | Porta | Protocolo | Origem | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Permitir |
| 65001    | AllowAzureLoadBalancerOutBound | Qualquer | Qualquer |  Qualquer | Internet | Permitir |
| 65500    | DenyAllOutBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

>[!NOTE]
> O Azure AD DS necessita de acesso de saída sem restrições da rede virtual. Não recomendamos que crie quaisquer regras adicionais que restringem o acesso à rede virtual.

## <a name="verify-and-edit-existing-security-rules"></a>Verificar e editar as regras de segurança existentes

Para verificar as regras de segurança existentes e certificar-se de que as portas padrão estão abertas, complete os seguintes passos:

1. No portal Azure, procure e selecione **grupos**de segurança da Rede .
1. Escolha o grupo de segurança da rede associado ao seu domínio gerido, como *AADDS-contoso.com-NSG*.
1. Na página **de visão geral,** as regras de segurança de entrada e saída existentes são mostradas.

    Reveja as regras de entrada e saída e compare-se com a lista das regras exigidas na secção anterior. Se necessário, selecione e, em seguida, elimine quaisquer regras personalizadas que bloqueiem o tráfego exigido. Se faltar alguma das regras exigidas, adicione uma regra na secção seguinte.

    Depois de adicionar ou eliminar regras para permitir o tráfego necessário, a saúde gerida pelo domínio do Azure AD DS atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="add-a-security-rule"></a>Adicionar uma regra de segurança

Para adicionar uma regra de segurança em falta, complete os seguintes passos:

1. No portal Azure, procure e selecione **grupos**de segurança da Rede .
1. Escolha o grupo de segurança da rede associado ao seu domínio gerido, como *AADDS-contoso.com-NSG*.
1. Em **Definições** no painel esquerdo, clique em regras de *segurança de entrada* ou regras de segurança de *saída,* dependendo da regra que precisa adicionar.
1. Selecione **Adicionar,** em seguida, criar a regra necessária com base na porta, protocolo, direção, etc. Quando estiver pronto, selecione **OK**.

Leva alguns momentos para que a regra de segurança seja adicionada e mostre-se na lista.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
