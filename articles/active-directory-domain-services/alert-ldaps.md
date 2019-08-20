---
title: 'Azure Active Directory Domain Services: Solucionar problemas de LDAP seguro | Microsoft Docs'
description: LDAP Seguro de solução de problemas para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8f9f4a8b52548dad011f5e825fa42c50da970ea7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613153"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services-solução de problemas de configuração de LDAP Seguro

Este artigo fornece soluções para problemas comuns ao [Configurar o LDAP seguro](tutorial-configure-ldaps.md) para Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: LDAP Seguro configuração do grupo de segurança de rede

**Mensagem de alerta:**

*LDAP Seguro pela Internet está habilitada para o domínio gerenciado. No entanto, o acesso à porta 636 não é bloqueado usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado para ataques de força bruta de senha.*

### <a name="secure-ldap-port"></a>Porta LDAP Seguro

Quando o LDAP seguro está habilitado, recomendamos a criação de regras adicionais para permitir o acesso de LDAPs de entrada apenas de determinados endereços IP. Essas regras protegem seu domínio contra ataques de força bruta que podem representar uma ameaça à segurança. A porta 636 permite o acesso ao domínio gerenciado. Aqui está como atualizar seu NSG para permitir o acesso para LDAP Seguro:

1. Navegue até a [guia grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) na portal do Azure
2. Escolha o NSG associado ao seu domínio da tabela.
3. Clique em **regras de segurança de entrada**
4. Criar a regra 636 da porta
   1. Clique em **Adicionar** na barra de navegação superior.
   2. Escolha os **endereços IP** para a origem.
   3. Especifique os intervalos de porta de origem para esta regra.
   4. Insira "636" para os intervalos de porta de destino.
   5. O protocolo é **TCP**.
   6. Dê à regra um nome, uma descrição e uma prioridade apropriados. A prioridade dessa regra deve ser maior que a prioridade da regra "negar tudo", se você tiver uma.
   7. Clique em **OK**.
5. Verifique se a regra foi criada.
6. Verifique a integridade do seu domínio em duas horas para garantir que você concluiu as etapas corretamente.

> [!TIP]
> A porta 636 não é a única regra necessária para que Azure AD Domain Services seja executada sem problemas. Para saber mais, visite os artigos [diretrizes de rede](network-considerations.md) ou solução de problemas de configuração do [NSG](alert-nsg.md) .
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: LDAP Seguro certificado expirando

**Mensagem de alerta:**

*O certificado LDAP seguro para o domínio gerenciado expirará em [date]].*

**Resolução:**

Crie um novo certificado LDAP seguro seguindo as etapas descritas no artigo [Configurar LDAP seguro](tutorial-configure-ldaps.md) .

## <a name="contact-us"></a>Contacte-nos
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
