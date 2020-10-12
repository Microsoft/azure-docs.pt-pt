---
title: Resolver alertas LDAP seguros nos Serviços de Domínio AZure AD / Microsoft Docs
description: Saiba como resolver problemas e resolver alertas comuns com LDAP seguro para serviços de domínio do Diretório Ativo Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: d987bbbe2a35dd24341b75d5663bab33d8c3bdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220257"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas de LDAP seguros nos Serviços de Domínio do Diretório Ativo Azure

As aplicações e serviços que utilizam o protocolo de acesso leve ao diretório (LDAP) para comunicar com o Azure Ative Directory Domain Services (Azure AD DS) podem ser [configurados para utilizar LDAP seguro](tutorial-configure-ldaps.md). Um certificado adequado e portas de rede necessárias devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda-o a compreender e a resolver alertas comuns com acesso LDAP seguro em Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Configuração segura da rede LDAP

### <a name="alert-message"></a>Mensagem de alerta

*O LDAP seguro através da internet está ativado para o domínio gerido. No entanto, o acesso à porta 636 não é bloqueado através de um grupo de segurança de rede. Isto pode expor as contas dos utilizadores no domínio gerido a ataques de força bruta de palavra-passe.*

### <a name="resolution"></a>Resolução

Quando ativa lDAP seguro, recomenda-se criar regras adicionais que restringem o acesso lDAPS de entrada a endereços IP específicos. Estas regras protegem o domínio gerido de ataques de força bruta. Para atualizar o grupo de segurança da rede para restringir o acesso à porta 636 da TCP para um LDAP seguro, complete os seguintes passos:

1. No portal Azure, procure e selecione **grupos de segurança da Rede**.
1. Escolha o grupo de segurança de rede associado ao seu domínio gerido, como *AADDS-contoso.com-NSG,* selecione **as regras de segurança de entrada**
1. **Selecione + Adicione** para criar uma regra para a porta TCP 636. Se necessário, selecione **Advanced** na janela para criar uma regra.
1. Para **obter,** escolha *endereços IP* no menu suspenso. Insira os endereços IP de origem que pretende conceder acesso para tráfego LDAP seguro.
1. Escolha *Qualquer* como **destino,** em seguida, insira *636* para **as gamas portuárias de destino.**
1. Estabeleça o **Protocolo** como *TCP* e a **Ação a** *Permitir*.
1. Especifique a prioridade para a regra e, em seguida, introduza um nome como *RestrictLDAPS*.
1. Quando estiver pronto, **selecione Adicionar** para criar a regra.

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

> [!TIP]
> A porta 636 da TCP não é a única regra necessária para que a Azure AD DS corra sem problemas. Para saber mais, consulte os [grupos de segurança da Rede Azure AD DS e as portas necessárias.](network-considerations.md#network-security-groups-and-required-ports)

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Certificado LDAP seguro expirando

### <a name="alert-message"></a>Mensagem de alerta

*O certificado LDAP seguro para o domínio gerido expirará em [data].*

### <a name="resolution"></a>Resolução

Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Aplique o certificado de substituição ao Azure AD DS e distribua o certificado a todos os clientes que se conectem através de LDAP seguro.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
