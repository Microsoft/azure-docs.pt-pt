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
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93376229"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>O que devo fazer se receber um desfasamento de certificado ao ligar usando a autenticação de certificado?

Desmarque **"Verifique a identidade do servidor validando o certificado"** ou **adicione o servidor FQDN juntamente com o certificado** ao criar um perfil manualmente. Pode fazê-lo executando **rasphone** a partir de um pedido de comando e escolhendo o perfil da lista de drop-down.

Contornar a validação da identidade do servidor não é recomendado em geral, mas com a autenticação do certificado Azure, o mesmo certificado está a ser utilizado para validação do servidor no protocolo de túnel VPN (IKEv2/SSTP) e no protocolo EAP. Uma vez que o certificado de servidor e fQDN já é validado pelo protocolo de túnel VPN, é redundante validar o mesmo novamente em EAP.

![auth ponto a-local](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificado de Servidor")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Posso usar o meu próprio CA de raiz PKI interno para gerar certificados para a conectividade ponto-a-local?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar 20 certificados de raiz.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Posso usar certificados do Azure Key Vault?

N.º

### <a name="what-tools-can-i-use-to-create-certificates"></a>Que ferramentas posso utilizar para criar certificados?

Pode utilizar a sua solução de PKI de Empresa (o PKI interno), o Azure PowerShell, MakeCert e OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Existem instruções para parâmetros e definições de certificado?

* **Solução PKI de Empresa/Interno:** Veja os passos para [Gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Veja o artigo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) para saber quais os passos a seguir.

* **MakeCert:** Veja o artigo [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) para saber quais os passos a seguir.

* **Abertura:** 

    * Ao exportar certificados, certifique-se de que converte o certificado de raiz para Base64.

    * Para o certificado de cliente:

      * Ao criar a chave privada, especifique o comprimento como 4096.
      * Ao criar o certificado para o parâmetro *-extensions*, especifique *usr_cert*.
