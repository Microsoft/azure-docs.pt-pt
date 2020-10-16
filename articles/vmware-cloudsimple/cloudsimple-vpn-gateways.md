---
title: Solução Azure VMware by CloudSimple - Gateways VPN
description: Saiba mais sobre as portas VPN do site cloudSimple e ponto a local, que são usadas para enviar tráfego encriptado entre uma região CloudSimple e outros recursos.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140637"
---
# <a name="vpn-gateways-overview"></a>Visão geral das portas da VPN

Um gateway VPN é usado para enviar tráfego encriptado entre uma rede de região CloudSimple em um local no local, ou um computador através da internet pública.  Cada região pode ter uma porta de entrada VPN, que pode suportar múltiplas ligações. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

CloudSimple fornece dois tipos de gateways VPN:

* Gateway VPN site-to-site
* Gateway VPN ponto-a-local

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN site-to-site

Um gateway VPN site-to-site é usado para enviar tráfego encriptado entre uma rede de região CloudSimple e um datacenter no local. Utilize esta ligação para definir a gama sub-redes/CIDR, para o tráfego de rede entre a sua rede no local e a rede da região CloudSimple.

O gateway VPN permite-lhe consumir serviços a partir de instalações na sua Nuvem Privada, e serviços na sua Nuvem Privada a partir da rede no local.  O CloudSimple fornece um servidor VPN baseado em políticas para estabelecer a ligação a partir da sua rede no local.

Utilize casos para VPN site-to-site:

* Acessibilidade do seu VCenter Private Cloud a partir de qualquer estação de trabalho na sua rede no local.
* Utilização do seu Ative Directory no local como fonte de identidade vCenter.
* Transferência conveniente de modelos VM, ISOs e outros ficheiros dos seus recursos no local para o seu VCenter Private Cloud.
* Acessibilidade das cargas de trabalho em execução na sua Nuvem Privada a partir da sua rede no local.

![Topologia de conexão local-a-local](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma ligação VPN local-a-local utiliza os seguintes parâmetros criptográficos predefinidos para estabelecer uma ligação segura.  Quando criar uma ligação a partir do seu dispositivo VPN no local, utilize qualquer um dos seguintes parâmetros que são suportados pelo seu gateway VPN no local.

#### <a name="phase-1-proposals"></a>Propostas da fase 1

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versão do IKE | IKEv1 | IKEv1 | IKEv1 |
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo de haxixe| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (Grupo DH) | 2 | 2 | 2 |
| Tempo de Vida | 28 800 segundos | 28 800 segundos | 28 800 segundos |
| Tamanho dos dados | 4GB | 4GB | 4GB |

#### <a name="phase-2-proposals"></a>Propostas da fase 2

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Encriptação | AES 128 | AES 256 | AES 256 |
| Algoritmo de haxixe| SHA 256 | SHA 256 | SHA 1 |
| Grupo perfeito de sigilo para a frente (Grupo PFS) | Nenhum | Nenhum | Nenhum |
| Tempo de Vida | 1.800 segundos | 1.800 segundos | 1.800 segundos |
| Tamanho dos dados | 4GB | 4GB | 4GB |


> [!IMPORTANT]
> Coloque o GP MSS A 1200 no seu dispositivo VPN. Ou se os seus dispositivos VPN não suportam o aperto de MSS, em alternativa, pode definir a MTU na interface do túnel para 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN ponto-a-local

Uma VPN ponto-a-local é usada para enviar tráfego encriptado entre uma rede de região CloudSimple e um computador cliente.  A VPN ponto-a-local é a forma mais fácil de aceder à sua rede Private Cloud, incluindo o seu VCenter Private Cloud e VMs de carga de trabalho.  Utilize a conectividade VPN ponto-a-local se estiver a ligar-se remotamente à Nuvem Privada.

## <a name="next-steps"></a>Passos seguintes

* [Configurar gateway VPN](vpn-gateway.md)
