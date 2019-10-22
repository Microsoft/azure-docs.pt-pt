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
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69520870"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

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
