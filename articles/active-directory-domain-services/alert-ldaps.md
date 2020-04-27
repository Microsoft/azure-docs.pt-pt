---
title: Resolver alertas lDAP seguros nos Serviços de Domínio Da AD do Azure Microsoft Docs
description: Aprenda a resolver problemas e a resolver alertas comuns com LDAP seguro para serviços de domínio de diretório ativo Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "71258056"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas LDAP seguros nos Serviços de Domínio de Diretório Ativo azure

As aplicações e serviços que utilizam o protocolo de acesso ao diretório leve (LDAP) para comunicar com o Azure Ative Directory Domain Services (Azure AD DS) podem ser [configurados para utilizar lDAP seguro](tutorial-configure-ldaps.md). Um certificado adequado e portas de rede exigidas devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda-o a compreender e a resolver alertas comuns com acesso lDAP seguro em DS AD Azure.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Configuração segura da rede LDAP

### <a name="alert-message"></a>Mensagem de alerta

*O LDAP seguro através da internet está ativado para o domínio gerido. No entanto, o acesso ao porto 636 não está bloqueado utilizando um grupo de segurança da rede. Isto pode expor as contas de utilizador no domínio gerido a ataques de força bruta de senha.*

### <a name="resolution"></a>Resolução

Quando ativa lDAP seguro, é aconselhável criar regras adicionais que restringem o acesso lDAPS de entrada a endereços IP específicos. Estas regras protegem o domínio gerido pela AD DS do Azure DS de ataques de força bruta. Para atualizar o grupo de segurança da rede para restringir o acesso à porta TCP 636 para LDAP seguro, preencha as seguintes etapas:

1. No portal Azure, procure e selecione **grupos**de segurança da Rede .
1. Escolha o grupo de segurança da rede associado ao seu domínio gerido, como *AADDS-contoso.com-NSG,* e, em seguida, selecione regras de **segurança de entrada**
1. **+ Adicione** uma regra para a porta TCP 636. Se necessário, selecione **Advanced** na janela para criar uma regra.
1. Para a **Fonte,** escolha *endereços IP* do menu suspenso. Introduza os endereços IP de origem que pretende conceder acesso para tráfego LDAP seguro.
1. Escolha *Qualquer como* **Destino,** em seguida, insira *636* para **as gamas**de porta de destino .
1. Estabeleça o **Protocolo** como *TCP* e a **Ação** a *Permitir*.
1. Especifique a prioridade para a regra e, em seguida, introduza um nome como *RestrictLDAPS*.
1. Quando estiver pronto, selecione **Adicionar** para criar a regra.

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

> [!TIP]
> A porta TCP 636 não é a única regra necessária para que o Azure AD DS corra sem problemas. Para saber mais, consulte os grupos de [segurança da Rede AD DS azure e as portas necessárias.](network-considerations.md#network-security-groups-and-required-ports)

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Certificado LDAP seguro expirando

### <a name="alert-message"></a>Mensagem de alerta

*O certificado LDAP seguro para o domínio gerido expirará em [data].*

### <a name="resolution"></a>Resolução

Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Aplique o certificado de substituição ao Azure AD DS e distribua o certificado a quaisquer clientes que se conectem utilizando LDAP seguro.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
