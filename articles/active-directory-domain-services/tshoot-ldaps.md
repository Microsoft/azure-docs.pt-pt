---
title: Resolução de problemas garante LDAP nos Serviços de Domínio da AD Azure [ Microsoft Docs
description: Saiba como resolver problemas seguros LDAP (LDAPS) para um domínio gerido pelo Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77132172"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Problemas seguros problemas de conectividade LDAP para um domínio gerido pelo Azure Ative Directory Domain Services

As aplicações e serviços que utilizam o protocolo de acesso ao diretório leve (LDAP) para comunicar com o Azure Ative Directory Domain Services (Azure AD DS) podem ser [configurados para utilizar lDAP seguro](tutorial-configure-ldaps.md). Um certificado adequado e portas de rede exigidas devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda-o a resolver problemas com acesso lDAP seguro em DS AD Azure.

## <a name="common-connection-issues"></a>Questões comuns de conexão

Se tiver problemas em ligar-se a um domínio gerido pelo Azure AD DS utilizando LDAP seguro, reveja os seguintes passos de resolução de problemas. Após cada passo de resolução de problemas, tente ligar-se ao domínio gerido pelo Azure AD DS novamente:

* A cadeia de emitente do certificado LDAP seguro deve ser confiada ao cliente. Pode adicionar a autoridade de certificação Root (CA) à loja de certificados de raiz fidedigna no cliente para estabelecer o fundo.
    * Certifique-se de [exportar e aplicar o certificado aos computadores clientes.][client-cert]
* Verifique se o certificado LDAP seguro para o seu domínio gerido tem o nome DNS no *Assunto* ou o atributo *de Nomes Alternativos sujeitos.*
    * Reveja os requisitos seguros de [certificado LDAP][certs-prereqs] e crie um certificado de substituição, se necessário.
* Verifique se o cliente LDAP, tal como *o Ldp.exe,* se liga ao ponto final lDAP seguro utilizando um nome DNS, e não o endereço IP.
    * O certificado aplicado ao domínio gerido pela AD DS azure não inclui os endereços IP do serviço, apenas os nomes DNS.
* Verifique o nome DNS a que o cliente LDAP se liga. Deve ser resolvido com o endereço IP público para um LDAP seguro no domínio gerido pelo Azure AD DS.
    * Se o nome DNS se resolver no endereço IP interno, atualize o registo DNS para resolver o endereço IP externo.
* Para a conectividade externa, o grupo de segurança da rede deve incluir uma regra que permita o tráfego para a porta TCP 636 a partir da internet.
    * Se conseguir ligar-se ao domínio gerido pelo Azure AD DS utilizando LDAP seguro a partir de recursos diretamente ligados à rede virtual, mas não a ligações externas, certifique-se de criar uma regra do grupo de segurança de rede que permita o [tráfego LDAP seguro][ldaps-nsg].

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
