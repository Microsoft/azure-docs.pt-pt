---
title: Gateways de VPN na solução VMware por CloudSimple – Azure
description: Saiba mais sobre os conceitos de VPN site a site e de VPN de ponto a site do CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47d61f80cae926965dd71342980302c2b3045c52
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689636"
---
# <a name="vpn-gateways-overview"></a>Visão geral de gateways de VPN

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede de região CloudSimple em um local ou um computador pela Internet pública.  Cada região pode ter apenas um gateway de VPN. No entanto, pode criar várias ligações para o mesmo gateway de VPN. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

O CloudSimple fornece dois tipos de gateways de VPN:

* Gateway de VPN site a site
* Gateway de VPN ponto a site

## <a name="site-to-site-vpn-gateway"></a>Gateway de VPN site a site

Um gateway de VPN site a site é usado para enviar tráfego criptografado entre uma rede de região CloudSimple e um datacenter local. Use essa conexão para definir o intervalo de sub-redes/CIDR, para a comunicação entre sua rede local e a rede de região CloudSimple.

O gateway de VPN permite que você consuma serviços do local em sua nuvem privada e serviços em sua nuvem privada, da rede local.  O CloudSimple fornece um servidor VPN baseado em políticas para estabelecer conexão de sua rede local.

Os casos de uso para VPN site a site incluem:

* Acessibilidade de sua nuvem privada vCenter de qualquer estação de trabalho em sua rede local.
* Uso de seu Active Directory local como uma fonte de identidade do vCenter.
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos locais para seu vCenter de nuvem privada.
* Acessibilidade de cargas de trabalho em execução em sua nuvem privada da sua rede local.

![Topologia de conexão VPN site a site](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> Você deve fixe TCP MSS em 1078 bytes ou inferior. Ou, se os dispositivos VPN não oferecerem suporte a MSS fixação MSS, você poderá definir, como alternativa, o MTU na interface de túnel como 1118 bytes. 

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma conexão VPN site a site usa os seguintes parâmetros criptográficos padrão para estabelecer uma conexão segura.  Ao criar uma conexão do dispositivo VPN local, use qualquer um dos parâmetros a seguir com suporte no gateway de VPN local.

#### <a name="phase-1-proposals"></a>Propostas da fase 1

| Parâmetro                       | Proposta 1     | Proposta 2     | Proposta 3     |
|---------------------------------|----------------|----------------|----------------|
| Versão do IKE                     | IKEv1          | IKEv1          | IKEv1          |
| Encriptação                      | AES 128        | AES 256        | AES 256        |
| Algoritmo de hash                  | SHA 256        | SHA 256        | SHA 1          |
| Grupo Diffie Hellman (grupo DH) | 2              | 2              | 2              |
| Tempo de vida                       | 28 800 segundos | 28 800 segundos | 28 800 segundos |
| Tamanho dos Dados                       | 4 GB           | 4 GB           | 4 GB           |
| Deteção de Elemento Inutilizado (DPD)       | Desabilitado/desativado   | Desabilitado/desativado   | Desabilitado/desativado   |


#### <a name="phase-2-proposals"></a>Propostas da fase 2 

| Parâmetro                                 | Proposta 1    | Proposta 2    | Proposta 3    |
|-------------------------------------------|---------------|---------------|---------------|
| Encriptação                                | AES 128       | AES 256       | AES 256       |
| Algoritmo de hash                            | SHA 256       | SHA 256       | SHA 1         |
| Grupo de sigilo contínuo (grupo PFS) | Nenhum          | Nenhum          | Nenhuma          |
| Tempo de vida                                 | 1\.800 segundos | 1\.800 segundos | 1\.800 segundos |
| Tamanho dos Dados                                 | 4 GB          | 4 GB          | 4 GB          |

## <a name="point-to-site-vpn-gateway"></a>Gateway de VPN ponto a site

Uma VPN ponto a site é usada para enviar tráfego criptografado entre uma rede de região CloudSimple e um computador cliente.  A VPN ponto a site é a maneira mais fácil de acessar sua rede de nuvem privada, incluindo suas VMs de nuvem privada e de carga de trabalho.  Use a conectividade VPN ponto a site se você estiver se conectando à nuvem privada remotamente.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar gateway de VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)