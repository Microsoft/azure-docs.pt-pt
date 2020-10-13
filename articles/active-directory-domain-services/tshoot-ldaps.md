---
title: Resolução de problemas segura LDAP em Azure AD Domain Services / Microsoft Docs
description: Saiba como resolver problemas com lDAP (LDAPS) para um domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: dc4a8e6783c699cd81ecab342f5611e564577fad
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967329"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Resolução de problemas garante problemas de conectividade LDAP a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure

As aplicações e serviços que utilizam o protocolo de acesso leve ao diretório (LDAP) para comunicar com o Azure Ative Directory Domain Services (Azure AD DS) podem ser [configurados para utilizar LDAP seguro](tutorial-configure-ldaps.md). Um certificado adequado e portas de rede necessárias devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda-o a resolver problemas com acesso LDAP seguro em Azure AD DS.

## <a name="common-connection-issues"></a>Problemas de ligação comuns

Se tiver problemas em ligar-se a um domínio gerido Azure AD DS utilizando LDAP seguro, reveja as seguintes etapas de resolução de problemas. Após cada passo de resolução de problemas, tente ligar-se novamente ao domínio gerido:

* A cadeia emitente do certificado LDAP seguro deve ser confiada ao cliente. Pode adicionar a autoridade de certificação Root (CA) à loja de certificados de raiz fidedigna no cliente para estabelecer o fundo.
    * Certifique-se [de exportar e aplicar o certificado aos computadores clientes.][client-cert]
* Verifique se o certificado LDAP seguro para o seu domínio gerido tem o nome DNS no *Sujeito* ou o atributo *Nomes Alternativos Sujeitos.*
    * Reveja os [requisitos de certificado LDAP seguros][certs-prereqs] e crie um certificado de substituição, se necessário.
* Verifique se o cliente LDAP, como *ldp.exe, * se conecta ao ponto final LDAP seguro utilizando um nome DNS, não o endereço IP.
    * O certificado aplicado ao domínio gerido não inclui os endereços IP do serviço, apenas os nomes DNS.
* Verifique o nome DNS a que o cliente LDAP se conecta. Deve resolver o endereço IP público para obter LDAP seguro no domínio gerido.
    * Se o nome DNS resolver para o endereço IP interno, atualize o registo DNS para resolver o endereço IP externo.
* Para a conectividade externa, o grupo de segurança da rede deve incluir uma regra que permita o tráfego para a porta TCP 636 a partir da internet.
    * Se conseguir ligar-se ao domínio gerido utilizando LDAP seguro a partir de recursos diretamente ligados à rede virtual, mas não ligações externas, certifique-se de que [cria uma regra de grupo de segurança de rede para permitir o tráfego LDAP seguro][ldaps-nsg].

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
