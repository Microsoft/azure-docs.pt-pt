---
title: Resolver alertas LDAP seguros no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas e resolver alertas comuns com LDAP seguro para Azure Active Directory Domain Services.
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
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "71258056"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: LDAP Seguro alertas no Azure Active Directory Domain Services

Os aplicativos e serviços que usam o protocolo LDAP para se comunicar com Azure Active Directory Domain Services (AD DS do Azure) podem ser [configurados para usar o LDAP seguro](tutorial-configure-ldaps.md). Um certificado apropriado e as portas de rede necessárias devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda você a entender e resolver alertas comuns com acesso LDAP seguro no Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: configuração de rede LDAP Seguro

### <a name="alert-message"></a>Mensagem de alerta

*LDAP Seguro pela Internet está habilitada para o domínio gerenciado. No entanto, o acesso à porta 636 não é bloqueado usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado para ataques de força bruta de senha.*

### <a name="resolution"></a>Resolução

Quando você habilita o LDAP seguro, é recomendável criar regras adicionais que restrinjam o acesso de LDAPs de entrada a endereços IP específicos. Essas regras protegem o domínio gerenciado AD DS do Azure contra ataques de força bruta. Para atualizar o grupo de segurança de rede para restringir o acesso à porta TCP 636 para LDAP seguro, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG*e selecione **regras de segurança de entrada**
1. **+ Adicione** uma regra para a porta TCP 636. Se necessário, selecione **avançado** na janela para criar uma regra.
1. Para a **origem**, escolha *endereços IP* no menu suspenso. Insira os endereços IP de origem que você deseja conceder acesso para tráfego LDAP seguro.
1. Escolha *qualquer* como **destino**e, em seguida, insira *636* para os intervalos de **porta de destino**.
1. Defina o **protocolo** como *TCP* e a **ação** a *permitir*.
1. Especifique a prioridade para a regra e insira um nome como *RestrictLDAPS*.
1. Quando estiver pronto, selecione **Adicionar** para criar a regra.

A integridade do domínio gerenciado do AD DS do Azure se atualiza automaticamente dentro de duas horas e remove o alerta.

> [!TIP]
> A porta TCP 636 não é a única regra necessária para que o AD DS do Azure seja executado sem problemas. Para saber mais, confira o [Azure AD DS grupos de segurança de rede e portas necessárias](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: LDAP Seguro certificado expirando

### <a name="alert-message"></a>Mensagem de alerta

*O certificado LDAP seguro para o domínio gerenciado expirará em [date]].*

### <a name="resolution"></a>Resolução

Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Aplique o certificado de substituição à AD DS do Azure e distribua o certificado para todos os clientes que se conectam usando o LDAP seguro.

## <a name="next-steps"></a>Passos seguintes

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
