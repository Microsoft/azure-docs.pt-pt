---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041584"
---
Obtenha o ficheiro .cer para o certificado raiz. Pode utilizar um certificado de raiz que foi gerado com uma solução de empresa (recomendada) ou gerar um certificado auto-assinado. Depois de criar o certificado raiz, exporte os dados do certificado público (não a chave privada) como um ficheiro de .cer X.509 codificado base64. Faça o upload deste ficheiro mais tarde para O Azure.

* **Certificado de empresa:** Se estiver a utilizar uma solução de empresa, pode utilizar a sua cadeia de certificados existente. Adquira o ficheiro .cer para o certificado raiz que pretende utilizar.
* **Certificado de raiz auto-assinado:** Se não estiver a usar uma solução de certificado empresarial, crie um certificado de raiz auto-assinado. Caso contrário, os certificados que criar não serão compatíveis com as suas ligações P2S e os clientes receberão um erro de ligação quando tentarem ligar.. Pode utilizar o Azure PowerShell, MakeCert ou OpenSSL. Os passos dos seguintes artigos descrevem como gerar um certificado de raiz auto-assinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): estas instruções requerem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado de raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): utilize o MakeCert se não tiver acesso a um computador com o Windows 10 para gerar certificados. Embora o MakeCert seja precotado, ainda pode usá-lo para gerar certificados. Os certificados de cliente que gere a partir do certificado raiz podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).