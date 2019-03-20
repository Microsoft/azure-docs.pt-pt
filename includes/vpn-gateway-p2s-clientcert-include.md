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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964249"
---
Cada computador cliente que se liga a uma VNet com uma ligação ponto a Site tem de ter um certificado de cliente instalado. Gerá-lo a partir do certificado de raiz e instalá-lo em cada computador cliente. Se não instalar um certificado de cliente válido, a autenticação irá falhar quando o cliente tenta estabelecer ligação à VNet.

Pode optar por gerar um certificado exclusivo para cada cliente ou pode utilizar o mesmo certificado para vários clientes. A vantagem de gerar certificados para cada cliente individual é a capacidade de revogar um único certificado. Caso contrário, se vários clientes utilizam o mesmo certificado de cliente para autenticar e o revogar, terá de gerar e instalar novos certificados para cada cliente que utiliza esse certificado.

Pode gerar certificados de cliente através dos seguintes métodos:

- **Certificado da empresa:**

  - Se estiver a utilizar uma solução de certificado de empresa, gere um certificado de cliente com o formato do valor de nome comum *name\@oseudominio.com*. Utilize este formato, em vez do *domínio name\username* formato.
  - Certifique-se de que o certificado de cliente baseia-se um modelo de certificado de utilizador que tenha *autenticação de cliente* listado como o primeiro item na lista de utilizadores. Verificar o certificado ao fazer duplo clique e exibindo **utilização de chave avançada** no **detalhes** separador.

- **Certificado de raiz autoassinado:** Siga os passos dos seguintes artigos de certificados P2S para que os certificados de cliente que criar serão compatíveis com as suas ligações de P2S. As etapas nestes artigos geram um certificado de cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Estas instruções requerem o Windows 10 e o PowerShell gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente P2S suportado.
  * [Instruções do MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Utilize o MakeCert se não tiver acesso a um computador Windows 10 para gerar certificados. Embora o MakeCert foi preterido, ainda pode utilizá-lo para gerar certificados. Pode instalar os certificados gerados em qualquer cliente P2S suportado.
  * [Instruções para Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Ao gerar um certificado de cliente a partir de um certificado de raiz autoassinado, este é instalado automaticamente no computador que utilizou para gerá-lo. Se pretender instalar um certificado de cliente noutro computador cliente, exporte-o como um ficheiro. pfx, juntamente com a cadeia de certificados inteira. Se o fizer, irá criar um ficheiro. pfx que contém as informações de certificado de raiz necessárias para o cliente autenticar. 

**Para exportar o certificado**

Para obter os passos exportar um certificado, consulte [gerar e exportar certificados para ligações ponto a Site com o PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
