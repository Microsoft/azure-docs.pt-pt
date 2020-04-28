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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184119"
---
Utilize um certificado de raiz que tenha sido gerado com uma solução empresarial (recomendada) ou gere um certificado auto-assinado. Depois de criar o certificado de raiz, exporte os dados do certificado público (não a chave privada) como ficheiro Base64 codificado X.509 .cer. Em seguida, faça o upload dos dados do certificado público para o servidor Azure.

* **Certificado de empresa:** Se estiver a usar uma solução empresarial, pode utilizar a sua cadeia de certificados existente. Adquira o ficheiro .cer para o certificado de raiz que pretende utilizar.
* **Certificado de raiz auto-assinado:** Se não estiver a usar uma solução de certificado de empresa, crie um certificado de raiz auto-assinado. Caso contrário, os certificados que criar não serão compatíveis com as suas ligações P2S e os clientes receberão um erro de ligação quando tentarem ligar-se. Pode utilizar o Azure PowerShell, MakeCert ou OpenSSL. Os passos nos seguintes artigos descrevem como gerar um certificado de raiz auto-assinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): estas instruções requerem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado de raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): utilize o MakeCert se não tiver acesso a um computador com o Windows 10 para gerar certificados. Embora o MakeCert esteja deprecida, ainda pode usá-lo para gerar certificados. Os certificados de cliente que gere a partir do certificado raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções para Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
