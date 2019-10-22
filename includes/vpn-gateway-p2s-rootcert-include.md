---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "67184119"
---
Use um certificado raiz que foi gerado com uma solução corporativa (recomendado) ou gere um certificado autoassinado. Depois de criar o certificado raiz, exporte os dados do certificado público (não a chave privada) como um arquivo X. 509. cer codificado na base64. Em seguida, carregue os dados do certificado público no servidor do Azure.

* **Certificado corporativo:** Se você estiver usando uma solução corporativa, poderá usar sua cadeia de certificados existente. Adquira o arquivo. cer do certificado raiz que você deseja usar.
* **Certificado raiz autoassinado:** Se você não estiver usando uma solução de certificado corporativo, crie um certificado raiz autoassinado. Caso contrário, os certificados criados não serão compatíveis com suas conexões P2S e os clientes receberão um erro de conexão quando tentarem se conectar. Pode utilizar o Azure PowerShell, MakeCert ou OpenSSL. As etapas nos artigos a seguir descrevem como gerar um certificado raiz autoassinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): estas instruções requerem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado de raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): utilize o MakeCert se não tiver acesso a um computador com o Windows 10 para gerar certificados. Embora o MakeCert seja preterido, você ainda pode usá-lo para gerar certificados. Os certificados de cliente gerados por meio do certificado raiz podem ser instalados em qualquer cliente P2S com suporte.
  * [Instruções para Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
