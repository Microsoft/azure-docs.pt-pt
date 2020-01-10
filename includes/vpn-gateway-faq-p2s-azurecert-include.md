---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751468"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>O que devo fazer se estiver recebendo uma incompatibilidade de certificado ao se conectar usando a autenticação de certificado?

Desmarque **"verificar a identidade do servidor Validando o certificado"** ou **adicione o FQDN do servidor junto com o certificado** ao criar um perfil manualmente. Você pode fazer isso executando o **Rasphone** em um prompt de comando e escolhendo o perfil na lista suspensa.

Não é recomendável ignorar a validação de identidade do servidor em geral, mas com a autenticação de certificado do Azure, o mesmo certificado está sendo usado para validação do servidor no protocolo IKEv2/SSTP e no protocolo EAP. Como o certificado do servidor e o FQDN já foram validados pelo protocolo de encapsulamento de VPN, ele é redundante para validar o mesmo novamente no EAP.

![ponto a site](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificado do servidor")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Posso usar minha própria AC raiz de PKI interna para gerar certificados para conectividade ponto a site?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar 20 certificados de raiz.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Posso usar certificados de Azure Key Vault?

Não.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Que ferramentas posso utilizar para criar certificados?

Pode utilizar a sua solução de PKI de Empresa (o PKI interno), o Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Existem instruções para parâmetros e definições de certificado?

* **Solução PKI de Empresa/Interno:** Veja os passos para [Gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Veja o artigo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) para saber quais os passos a seguir.

* **MakeCert:** Veja o artigo [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) para saber quais os passos a seguir.

* **OpenSSL:** 

    * Ao exportar certificados, certifique-se de que converte o certificado de raiz para Base64.

    * Para o certificado de cliente:

      * Ao criar a chave privada, especifique o comprimento como 4096.
      * Ao criar o certificado para o parâmetro *-extensions*, especifique *usr_cert*.
