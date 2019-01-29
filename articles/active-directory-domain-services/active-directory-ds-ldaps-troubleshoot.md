---
title: Resolver problemas relacionados com o LDAP seguro (LDAPS) no Azure AD Domain Services | Documentos da Microsoft
description: Resolver problemas de LDAP seguro (LDAPS) para um domínio gerido do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 97a2ee23435d0b29565bc3bf1dcb426e9e83fd31
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185220"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Resolver problemas de LDAP seguro (LDAPS) para um domínio gerido do Azure AD Domain Services

## <a name="connection-issues"></a>Problemas de ligação
Se tiver problemas em ligar para o domínio gerido com o secure LDAP:

* A cadeia de emissor de certificado de secure LDAP tem de ser fidedigno no cliente. Pode optar por adicionar a autoridade de certificação de raiz para o arquivo de certificados de raiz fidedigna no cliente para estabelecer a confiança.
* Certifique-se de que o cliente LDAP (por exemplo, ldp.exe) estabelece uma ligação para o ponto de extremidade do LDAP seguro com um nome DNS, não o endereço IP.
* Verifique o nome DNS, o cliente LDAP que se liga a. Ele deve resolver para o endereço IP público para o LDAP seguro no domínio gerido.
* Certifique-se de que o certificado de secure LDAP para o seu domínio gerido com o nome DNS no assunto ou o atributo de nomes alternativos do requerente.
* As definições de NSG para a rede virtual tem de permitir o tráfego para a porta 636 da internet. Este passo aplica-se apenas se ativou o acesso de secure LDAP pela internet.


## <a name="need-help"></a>Precisa de ajuda?
Se ainda tiver problemas em ligar para o domínio gerido com o LDAP seguro, [contacte a equipa de produto](active-directory-ds-contact-us.md) para obter ajuda. Inclua as seguintes informações para ajudar a diagnosticar o problema melhor:
* Captura de ecrã do ldp.exe estabelecer a ligação e a falhar.
* O ID de inquilino do Azure AD e o nome de domínio DNS do seu domínio gerido.
* Nome de utilizador exato que está a tentar ligar como.


## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrar política de grupo num domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)
