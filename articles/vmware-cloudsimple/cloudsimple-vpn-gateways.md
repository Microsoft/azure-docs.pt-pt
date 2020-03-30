---
title: Solução Azure VMware by CloudSimple - Gateways VPN
description: Saiba mais sobre os conceitos VPN site-to-site da CloudSimple e conceitos de VPN ponto-a-site
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024862"
---
# <a name="vpn-gateways-overview"></a>Visão geral dos gateways VPN

Um gateway VPN é usado para enviar tráfego encriptado entre uma rede da região CloudSimple em um local no local, ou um computador através da internet pública.  Cada região pode ter um gateway VPN, que pode suportar múltiplas ligações. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

CloudSimple fornece dois tipos de gateways VPN:

* Gateway VPN site-to-site
* Gateway VPN ponto-a-local

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN site-to-site

Um portal VPN site-to-site é usado para enviar tráfego encriptado entre uma rede da região CloudSimple e um centro de dados no local. Utilize esta ligação para definir a gama subnets/CIDR, para o tráfego de rede entre a sua rede no local e a rede da região CloudSimple.

O gateway VPN permite-lhe consumir serviços a partir de instalações na sua Nuvem Privada, e serviços na sua Nuvem Privada a partir da rede no local.  A CloudSimple fornece um servidor VPN baseado em políticas para estabelecer a ligação a partir da sua rede no local.

Utilize casos para VPN site-to-site:

* Acessibilidade do seu vCenter De Nuvem Privada a partir de qualquer estação de trabalho na sua rede no local.
* Utilização do seu Diretório Ativo no local como fonte de identidade vCenter.
* Transferência conveniente de modelos VM, ISOs e outros ficheiros dos seus recursos no local para o seu vCenter de Nuvem Privada.
* Acessibilidade das cargas de trabalho que correm na sua Nuvem Privada a partir da sua rede no local.

![Topologia de ligação VPN site-to-site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma ligação VPN site-to-site utiliza os seguintes parâmetros criptográficos padrão para estabelecer uma ligação segura.  Quando criar uma ligação a partir do seu dispositivo VPN no local, utilize qualquer um dos seguintes parâmetros suportados pelo seu gateway VPN no local.

#### <a name="phase-1-proposals"></a>Propostas da fase 1

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versão do IKE | IKEv1 | IKEv1 | IKEv1 |
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (Grupo DH) | 2 | 2 | 2 |
| Tempo de Vida | 28 800 segundos | 28 800 segundos | 28 800 segundos |
| Tamanho dos dados | 4GB | 4GB | 4GB |

#### <a name="phase-2-proposals"></a>Propostas de fase 2

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de Sigilo Avançado Perfeito (Grupo PFS) | Nenhuma | Nenhuma | Nenhuma |
| Tempo de Vida | 1.800 segundos | 1.800 segundos | 1.800 segundos |
| Tamanho dos dados | 4GB | 4GB | 4GB |


> [!IMPORTANT]
> Coloque o Pinça MSS TCP a 1200 no seu dispositivo VPN. Ou se os seus dispositivos VPN não suportarem a fixação de MSS, pode, em alternativa, definir o MTU na interface do túnel para 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN ponto-a-local

Uma VPN Ponto-a-Site é usada para enviar tráfego encriptado entre uma rede da região CloudSimple e um computador cliente.  A VPN ponto-a-site é a forma mais fácil de aceder à sua rede Private Cloud, incluindo o seu vCenter de Nuvem Privada e VMs de carga de trabalho.  Utilize a conectividade VPN ponto-a-local se estiver a ligar-se remotamente à Nuvem Privada.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o gateway VPN](vpn-gateway.md)
