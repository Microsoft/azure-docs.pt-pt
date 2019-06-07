---
title: Gateways de VPN na solução de VMware ao CloudSimple - Azure
description: Saiba mais sobre o VPN de site a site CloudSimple e conceitos VPN ponto a site
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b2630614e549181f4dd2f4e79871c4594d09201
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496847"
---
# <a name="vpn-gateways-overview"></a>Descrição geral de gateways VPN

Um gateway VPN é utilizado para enviar tráfego encriptado entre uma rede de região CloudSimple numa localização no local ou um computador através da Internet pública.  Cada região pode ter apenas um gateway VPN. No entanto, pode criar várias ligações para o mesmo gateway de VPN. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

CloudSimple fornece dois tipos de gateways de VPN:

* Gateway de VPN de site a site
* Gateway de VPN ponto a site

## <a name="site-to-site-vpn-gateway"></a>Gateway de VPN de site a site

Um gateway de VPN de site a site é utilizado para enviar tráfego encriptado entre uma rede de região CloudSimple e um datacenter no local. Utilize esta ligação para definir o intervalo de sub-redes/CIDR, para tráfego de rede entre a sua rede no local e a rede de região de CloudSimple.

O gateway de VPN, pode consumir os serviços no local na sua nuvem privada e serviços na sua nuvem privada, da rede no local.  CloudSimple fornece um servidor VPN baseado em políticas para estabelecer ligação a partir da sua rede no local.

Casos de utilização para VPN de site para site incluem:

* Acessibilidade do vCenter sua nuvem privada em qualquer estação de trabalho na sua rede no local.
* Utilização do Active Directory no local como uma origem de identidades do vCenter.
* Conveniente transferência de modelos VM, ISOs e outros arquivos de seus recursos no local para o vCenter de nuvem privada.
* Acessibilidade de cargas de trabalho em execução na sua nuvem privada a partir da sua rede no local.

![Topologia de ligação de VPN de site a Site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma ligação de VPN de site a site utiliza os seguintes parâmetros de criptografia predefinido para estabelecer uma ligação segura.  Quando cria uma ligação do dispositivo VPN no local, os parâmetros têm de corresponder.

Uma ligação de VPN de site a site utiliza os seguintes parâmetros de criptografia predefinido para estabelecer uma ligação segura.  Quando cria uma ligação do dispositivo VPN no local, tem de utilizar qualquer um dos seguintes parâmetros suportados pelo gateway de VPN no local.

#### <a name="phase-1-proposals"></a>Propostas de fase 1

| Parâmetro | Proposta de 1 | Proposta de 2 | Proposta de 3 |
|-----------|------------|------------|------------|
| Versão do IKE | IKEv1 | IKEv1 | IKEv1 |
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman (DH grupo) | 1 | 1 | 1 |
| Tempo de vida | 28 800 segundos | 28 800 segundos | 28 800 segundos |
| Tamanho dos dados | 4GB | 4GB | 4GB |


#### <a name="phase-2-proposals"></a>Propostas de fase 2 

| Parâmetro | Proposta de 1 | Proposta de 2 | Proposta de 3 |
|-----------|------------|------------|------------|
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Perfeito Forward Secrecy grupo (grupo PFS) | Nenhuma | Nenhum | Nenhuma |
| Tempo de vida | segundos de 1.800 | segundos de 1.800 | segundos de 1.800 |
| Tamanho dos dados | 4GB | 4GB | 4GB |

## <a name="point-to-site-vpn-gateway"></a>Gateway de VPN ponto a site

Uma VPN ponto a site é utilizada para enviar tráfego encriptado entre uma rede de região CloudSimple e um computador cliente.  VPN Point-to-site é a maneira mais fácil de aceder à sua rede de nuvem privada, incluindo a sua carga de trabalho VMs e vCenter de nuvem privada.  Utilize a conectividade VPN ponto a site se estiver a ligar à nuvem privada remotamente.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar o gateway de VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)