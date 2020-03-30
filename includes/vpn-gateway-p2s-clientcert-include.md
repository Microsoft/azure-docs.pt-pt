---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184121"
---
Cada computador cliente que se conectem a um VNet com uma ligação Ponto-a-Local deve ter um certificado de cliente instalado. Gera-se a partir do certificado de raiz e instala-o em cada computador cliente. Se não instalar um certificado de cliente válido, a autenticação falhará quando o cliente tentar ligar-se ao VNet.

Pode optar por gerar um certificado exclusivo para cada cliente ou pode utilizar o mesmo certificado para vários clientes. A vantagem de gerar certificados para cada cliente individual é a capacidade de revogar um único certificado. Caso contrário, se vários clientes usarem o mesmo certificado de cliente para autenticar e o revogarem, terão de gerar e instalar novos certificados para cada cliente que utilize esse certificado.

Pode gerar certificados de cliente utilizando os seguintes métodos:

- **Certificado de empresa:**

  - Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o *nome\@* de formato de valor de nome comum yourdomain.com . Utilize este formato em vez do formato de nome de *domínio\username.*
  - Certifique-se de que o certificado de cliente se baseia num modelo de certificado de utilizador que tem a *Autenticação* do Cliente listado como o primeiro item da lista de utilizadores. Verifique o certificado clicando-o duas vezes e visualizando o **uso da chave melhorada** no separador **Detalhes.**

- **Certificado de raiz auto-assinado:** Siga os passos num dos seguintes artigos de certificado P2S para que os certificados de cliente que criar sejam compatíveis com as suas ligações P2S. Os passos nestes artigos geram um certificado de cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): estas instruções requerem o Windows 10 e o PowerShell para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Utilize o MakeCert se não tiver acesso a um computador Windows 10 para gerar certificados. Embora o MakeCert esteja deprecida, ainda pode usá-lo para gerar certificados. Pode instalar os certificados gerados em qualquer cliente P2S suportado.
  * [Instruções para Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Quando gera um certificado de cliente a partir de um certificado de raiz auto-assinado, é automaticamente instalado no computador que usou para o gerar. Se quiser instalar um certificado de cliente noutro computador cliente, exporte-o como ficheiro .pfx, juntamente com toda a cadeia de certificados. Ao fazê-lo, criará um ficheiro .pfx que contenha as informações de certificado de raiz necessárias para que o cliente autentime. 

**Para exportar o certificado**

Para obter medidas para exportar um certificado, consulte [Certificados de Geração e exportação para o Ponto-a-Local utilizando a PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
