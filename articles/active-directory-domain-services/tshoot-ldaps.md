---
title: Solucionar problemas de LDAP Seguro (LDAPs) no Azure AD Domain Services | Microsoft Docs
description: Solucionar problemas de LDAP Seguro (LDAPs) para um domínio Azure AD Domain Services gerenciado
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617053"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas de LDAP Seguro (LDAPs) para um domínio Azure AD Domain Services gerenciado

## <a name="connection-issues"></a>Problemas de ligação
Se você tiver problemas para se conectar ao domínio gerenciado usando o LDAP seguro:

* A cadeia do emissor do certificado LDAP seguro deve ser confiável no cliente. Você pode optar por adicionar a autoridade de certificação raiz ao repositório de certificados raiz confiáveis no cliente para estabelecer a relação de confiança.
* Verifique se o cliente LDAP (por exemplo, LDP. exe) se conecta ao ponto de extremidade LDAP seguro usando um nome DNS, não o endereço IP.
* Verifique o nome DNS ao qual o cliente LDAP se conecta. Ele deve resolver para o endereço IP público para LDAP seguro no domínio gerenciado.
* Verifique se o certificado LDAP seguro para seu domínio gerenciado tem o nome DNS no atributo assunto ou nomes alternativos da entidade.
* As configurações de NSG para a rede virtual devem permitir o tráfego para a porta 636 da Internet. Esta etapa se aplica somente se você tiver habilitado o acesso LDAP seguro pela Internet.


## <a name="need-help"></a>Precisa de ajuda?
Se você ainda tiver problemas para se conectar ao domínio gerenciado usando o LDAP seguro, [contate a equipe do produto](contact-us.md) para obter ajuda. Inclua as seguintes informações para ajudar a diagnosticar o problema melhor:
* Uma captura de tela do LDP. exe fazendo a conexão e falhando.
* Sua ID de locatário do Azure AD e o nome de domínio DNS do seu domínio gerenciado.
* Nome de usuário exato que você está tentando associar.


## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](tutorial-create-instance.md)
* [Gerenciar um domínio de Azure AD Domain Services](tutorial-create-management-vm.md)
* [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Gerenciar Política de Grupo para Azure AD Domain Services](manage-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)
