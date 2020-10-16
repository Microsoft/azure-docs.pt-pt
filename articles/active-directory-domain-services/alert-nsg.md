---
title: Resolver alertas de grupo de segurança de rede em Azure AD DS / Microsoft Docs
description: Saiba como resolver problemas e resolver alertas de configuração do grupo de segurança de rede para serviços de domínio do diretório ativo Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: f8917d7bd8fc1a4091607b9a405cfefbb51bc188
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962790"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas de configuração de rede nos Serviços de Domínio do Diretório Ativo Azure

Para permitir que aplicações e serviços se comuniquem corretamente com um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS), as portas de rede específicas devem estar abertas para permitir o fluxo de tráfego. Em Azure, controla-se o fluxo de tráfego utilizando grupos de segurança de rede. O estado de saúde de um domínio gerido pela Azure AD DS mostra um alerta se as regras necessárias do grupo de segurança de rede não estiverem em vigor.

Este artigo ajuda-o a compreender e a resolver alertas comuns para problemas de configuração do grupo de segurança da rede.

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: Erro de rede

### <a name="alert-message"></a>Mensagem de alerta

*A Microsoft não consegue chegar aos controladores de domínio para este domínio gerido. Isto pode acontecer se um grupo de segurança de rede (NSG) configurar no seu sistema de rede virtual bloqueia o acesso ao domínio gerido. Outra razão possível é se existe uma rota definida pelo utilizador que bloqueia a entrada de tráfego a partir da internet.*

As regras do grupo de segurança da rede inválidas são a causa mais comum de erros de rede para Azure AD DS. O grupo de segurança da rede virtual deve permitir o acesso a portas e protocolos específicos. Se estas portas estiverem bloqueadas, a plataforma Azure não pode monitorizar ou atualizar o domínio gerido. A sincronização entre o diretório AD Azure e a Azure AD DS também é impactada. Certifique-se de que mantém as portas padrão abertas para evitar interrupções no serviço.

## <a name="default-security-rules"></a>Regras de segurança predefinidas

As seguintes regras de segurança de entrada e saída são aplicadas ao grupo de segurança da rede para um domínio gerido. Estas regras mantêm o Azure AD DS seguro e permitem que a plataforma Azure monitorize, gere e atualize o domínio gerido.

### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Prioridade | Nome | Porta | Protocolo | Origem | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 101      | Permite Que o SyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Qualquer | Permitir |
| 201      | PermitirRD | 3389 | TCP | Serra CorpNet | Qualquer | Permitir |
| 301      | Permitir aRemotagem de Admissão | 5986| TCP | AzureActiveDirectoryDomainServices | Qualquer | Permitir |
| 65000    | AllVnetInBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Permitir |
| 65001    | AllowAzureLoadBalancerInBound | Qualquer | Qualquer | AzureLoadBalancer | Qualquer | Permitir |
| 65500    | DenyAllInBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

> [!NOTE]
> Também pode ter uma regra adicional que permite o tráfego de entrada se [configurar LDAP seguro][configure-ldaps]. Esta regra adicional é necessária para a comunicação LDAPS correta.

### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Prioridade | Nome | Porta | Protocolo | Origem | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Permitir |
| 65001    | Permitir a Entrada de Água | Qualquer | Qualquer |  Qualquer | Internet | Permitir |
| 65500    | DenyAllOutBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

>[!NOTE]
> O Azure AD DS necessita de acesso de saída ilimitado a partir da rede virtual. Não recomendamos que crie quaisquer regras adicionais que restrinjam o acesso de saída para a rede virtual.

## <a name="verify-and-edit-existing-security-rules"></a>Verificar e editar as regras de segurança existentes

Para verificar as regras de segurança existentes e certificar-se de que as portas predefinidos estão abertas, complete os seguintes passos:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao seu domínio gerido, como *a AADDS-contoso.com-NSG*.
1. Na página **'Visão Geral',** são apresentadas as regras de segurança de entrada e saída existentes.

    Reveja as regras de entrada e saída e compare-as com a lista de regras exigidas na secção anterior. Se necessário, selecione e, em seguida, elimine quaisquer regras personalizadas que bloqueiem o tráfego necessário. Se faltar alguma das regras exigidas, adicione uma regra na secção seguinte.

    Depois de adicionar ou eliminar regras para permitir o tráfego necessário, a saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="add-a-security-rule"></a>Adicionar uma regra de segurança

Para adicionar uma regra de segurança em falta, complete os seguintes passos:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao seu domínio gerido, como *a AADDS-contoso.com-NSG*.
1. Em **Definições** no painel esquerdo, clique nas *regras de segurança de entrada* ou de *saída,* dependendo da regra que precisa de adicionar.
1. **Selecione Adicionar,** em seguida, crie a regra necessária com base na porta, protocolo, direção, etc. Quando estiver pronto, selecione **OK**.

Leva alguns momentos para que a regra de segurança seja adicionada e mostrada na lista.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
