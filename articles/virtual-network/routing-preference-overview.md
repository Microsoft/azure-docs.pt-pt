---
title: Preferência de encaminhamento em Azure
description: Saiba como pode escolher como as suas rotas de tráfego entre Azure e a Internet com preferência por encaminhamento.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 56fd9898065dd55b9a56547a775f7f2aa4a56156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693735"
---
# <a name="what-is-routing-preference"></a>O que é a preferência de encaminhamento?

A preferência por encaminhamento azure permite-lhe escolher como as suas rotas de tráfego entre Azure e a Internet. Pode optar por encaminhar o tráfego através da rede Microsoft, ou através da rede ISP (internet pública). Estas opções também são referidas como *encaminhamento de batata fria* e *encaminhamento de batatas quentes,* respectivamente. O preço de transferência de dados da Egress varia em função da seleção de encaminhamento. Pode escolher a opção de encaminhamento enquanto cria um endereço IP público. O endereço IP público pode ser associado a recursos como máquina virtual, conjuntos de escala de máquinas virtuais, balanceador de carga virado para a Internet, etc. Também pode definir a preferência de encaminhamento para recursos de armazenamento Azure, tais como bolhas, ficheiros, web e Azure DataLake. Por padrão, o tráfego é encaminhado através da rede global da Microsoft para todos os serviços Azure.

## <a name="routing-via-microsoft-global-network"></a>Encaminhamento através da rede global da Microsoft

Ao encaminhar o seu tráfego através da rede global da *Microsoft,* o tráfego é entregue através de uma das maiores redes do mundo, abrangendo mais de 160.000 milhas de fibra com mais de 165 pontas de Ponta de Presença (POP). A rede está bem abastado com múltiplos caminhos de fibra redundantes para garantir uma fiabilidade e disponibilidade excepcionalmente elevadas. A engenharia de tráfego é gerida por um controlador WAN definido por software que garante uma baixa seleção de caminhos de latência para o seu tráfego e oferece o desempenho da rede premium.

![Encaminhamento através da rede global da Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Tráfego ingresso:** O anúncio global da BGP Anycast garante que o tráfego de entrada entra na rede da Microsoft mais próxima do utilizador. Por exemplo, se um utilizador de Singapura aceder aos recursos Azure alojados em Chicago, EUA, então o tráfego é introduzido na rede global da Microsoft em Singapore Edge POP e viaja na rede da Microsoft para o serviço hospedado em Chicago.

**Tráfego de Egress:** O tráfego de saídas segue o mesmo princípio. O tráfego percorre a maior parte da sua viagem na rede global da Microsoft e sai mais próximo do utilizador. Por exemplo, se o tráfego de Azure Chicago estiver destinado a um utilizador de Singapura, então o tráfego viaja na rede da Microsoft de Chicago para Singapura, e sai da rede microsoft em Singapore Edge POP.

Tanto o tráfego de entradas como de saídas permanece a maior parte das viagens na rede global da Microsoft. Isto também é conhecido como *roteamento de batata fria*.


## <a name="routing-over-public-internet-isp-network"></a>Encaminhamento através da Internet pública (rede ISP)

A nova escolha de *encaminhamento de linha internet* minimiza as viagens na rede global da Microsoft e utiliza a rede ISP de trânsito para encaminhar o seu tráfego. Esta opção de encaminhamento otimizada em termos de custos oferece um desempenho de rede comparável a outros fornecedores de nuvem.

![Encaminhamento através da Internet pública](media/routing-preference-overview/route-via-isp-network.png)

**Tráfego ingresso:** O caminho de entrada utiliza *o encaminhamento de batatas quentes,* o que significa que o tráfego entra na rede da Microsoft que está mais próxima da região de serviços hospedado. Por exemplo, se um utilizador de Singapura acede aos recursos Azure alojados em Chicago, o tráfego viaja pela internet pública e entra na rede global da Microsoft em Chicago.

**Tráfego de Egress:** O tráfego de saídas segue o mesmo princípio. O tráfego sai da rede da Microsoft na mesma região onde o serviço está hospedado. Por exemplo, se o tráfego do seu serviço em Azure Chicago estiver destinado a um utilizador de Singapura, então o tráfego sai da rede da Microsoft em Chicago e viaja através da internet pública para o utilizador em Singapura.

## <a name="supported-services"></a>Serviços suportados

O IP público com a escolha de preferência de encaminhamento "Microsoft Global Network" pode ser associado a quaisquer serviços Azure. No entanto, o IP público com escolha de preferência de encaminhamento **A Internet** pode ser associada com os seguintes recursos Azure:

* Máquina virtual
* Conjuntos de dimensionamento de máquinas virtuais
* Azure Kubernetes Service (AKS)
* Equilibrador de carga virado para a Internet
* Gateway de Aplicação
* Azure Firewall

Para armazenamento, os pontos finais primários utilizam sempre a rede global da **Microsoft.** Pode ativar pontos finais secundários com **internet** como escolha para o encaminhamento de tráfego. Os serviços de armazenamento suportados são:

* Blobs
* Ficheiros
* Web
* Azure DataLake

## <a name="pricing"></a>Preços
A diferença de preços entre ambas as opções reflete-se nos preços de transferência de dados da internet. O preço de transferência de dados **da rede global da Microsoft** é o mesmo que o preço atual da saída da Internet. Visite [a página de preços da largura de banda Azure](https://azure.microsoft.com/pricing/details/bandwidth/) para obter as informações mais recentes sobre os preços.

## <a name="limitations"></a>Limitações


* A preferência por encaminhamento só é compatível com o SKU padrão redundante de zona do endereço IP público. O SKU básico do endereço IP público não é suportado.
* Atualmente, a preferência de encaminhamento suporta apenas endereços IP públicos IPv4. Os endereços IP públicos IPv6 não são suportados.


## <a name="next-steps"></a>Passos seguintes

* [Configure a preferência de encaminhamento para um VM utilizando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configure a preferência de encaminhamento para um VM utilizando o CLI Azure](configure-routing-preference-virtual-machine-cli.md)
