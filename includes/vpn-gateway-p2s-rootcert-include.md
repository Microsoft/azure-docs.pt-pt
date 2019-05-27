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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157346"
---
Utilizar um certificado de raiz que tenha sido criado com uma solução empresarial (recomendada) ou gerar um certificado autoassinado. Depois de criar o certificado de raiz, exporte os dados de certificado público (não a chave privada) como ficheiro. cer X.509 com codificação de um Base64. Em seguida, carregue os dados de certificado público para o servidor do Azure.

* **Certificado da empresa:** Se estiver a utilizar uma solução empresarial, pode utilizar a sua cadeia de certificados existente. Adquira já o ficheiro. cer para o certificado de raiz que pretende utilizar.
* **Certificado de raiz autoassinado:** Se não estiver a utilizar uma solução de certificado de empresa, crie um certificado de raiz autoassinado. Caso contrário, os certificados que criar não serão compatíveis com as suas ligações de P2S e os clientes irão receber um erro de ligação quando tentarem estabelecer ligação. Pode utilizar o Azure PowerShell, MakeCert ou OpenSSL. Os passos nos seguintes artigos descrevem como gerar um certificado de raiz autoassinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Estas instruções requerem o Windows 10 e o PowerShell gerar certificados. Os certificados de cliente gerados a partir do certificado de raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Utilize o MakeCert se não tiver acesso a um computador Windows 10 para gerar certificados. Embora o MakeCert foi preterido, ainda pode utilizá-lo para gerar certificados. Certificados de cliente que irá gerar a partir do certificado de raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções para Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
