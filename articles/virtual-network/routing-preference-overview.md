---
title: Preferência de encaminhamento em Azure
description: Saiba como pode escolher como as suas rotas de tráfego entre o Azure e a Internet com preferência por encaminhamento.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 0ae06a1c3d486b5d5998b4c6d050d86f50910a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598039"
---
# <a name="what-is-routing-preference-preview"></a>O que é a preferência de encaminhamento (pré-visualização)?

A preferência de encaminhamento Azure permite-lhe escolher como as suas rotas de tráfego entre o Azure e a Internet. Pode optar por desviar o tráfego através da rede Microsoft, ou, através da rede ISP (internet pública). Estas opções também são referidas como *encaminhamento* de batata fria e *encaminhamento* de batata quente, respectivamente. O preço de transferência de dados da Egress varia de acordo com a seleção de encaminhamento. Pode escolher a opção de encaminhamento enquanto cria um endereço IP público. O endereço IP público pode ser associado a recursos como máquina virtual, conjuntos de escala de máquinavirtual, equilibrador de carga virado para a Internet, etc. Também pode definir a preferência de encaminhamento para recursos de armazenamento Azure, tais como blobs, ficheiros, web e Azure DataLake. Por padrão, o tráfego é encaminhado através da rede global da Microsoft para todos os serviços Azure.

> [!IMPORTANT]
> A preferência por encaminhamento está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="routing-via-microsoft-global-network"></a>Encaminhamento via rede global da Microsoft

Ao direcionar o seu tráfego através da rede global da *Microsoft,* o tráfego é entregue através de uma das maiores redes do mundo, abrangendo mais de 160.000 milhas de fibra com mais de 165 bordas Point of Presence (POP). A rede está bem aprovisionada com múltiplos caminhos de fibra redundante para garantir uma fiabilidade e disponibilidade excepcionalmente elevadas. A engenharia de tráfego é gerida por um controlador WAN definido por software que garante uma seleção de caminhos de latência baixa para o seu tráfego e oferece o desempenho da rede premium.

![Encaminhamento via rede global da Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Tráfego de ingressos:** O anúncio global do BGP Anycast garante que o tráfego de ingresso entra na rede da Microsoft mais próxima do utilizador. Por exemplo, se um utilizador de Singapura aceder aos recursos Azure hospedados em Chicago, EUA, então o tráfego é introduzido na rede global da Microsoft em Singapura Edge POP e viaja na rede da Microsoft para o serviço hospedado em Chicago.

**Tráfego de egress:** O tráfego de esgress segue o mesmo princípio. O tráfego viaja a maior parte da sua viagem na rede global da Microsoft e sai mais próximo do utilizador. Por exemplo, se o tráfego do Azure Chicago estiver destinado a um utilizador de Singapura, então o tráfego viaja na rede da Microsoft de Chicago para Singapura, e sai da rede microsoft em Singapura Edge POP.

Tanto a entrada como a saída do tráfego permanecem a maior parte das viagens na rede global da Microsoft. Isto também é conhecido como *encaminhamento de batata fria.*


## <a name="routing-over-public-internet-isp-network"></a>Encaminhamento através da Internet pública (rede ISP)

A nova escolha de *encaminhamento* de Internet minimiza as viagens na rede global da Microsoft e utiliza a rede ISP de trânsito para encaminhar o seu tráfego. Esta opção de encaminhamento otimizada por custos oferece um desempenho de rede comparável a outros fornecedores de nuvem.

![Encaminhamento pela Internet pública](media/routing-preference-overview/route-via-isp-network.png)

**Tráfego de ingressos:** O caminho de ingresso utiliza *o encaminhamento* de batata quente, o que significa que o tráfego entra na rede da Microsoft que está mais próxima da região de serviço hospedada. Por exemplo, se um utilizador de Singapura aceder aos recursos Azure hospedados em Chicago, o tráfego viaja pela internet pública e entra na rede global da Microsoft em Chicago.

**Tráfego de egress:** O tráfego de esgress segue o mesmo princípio. O tráfego sai da rede da Microsoft na mesma região que o serviço está hospedado. Por exemplo, se o tráfego do seu serviço no Azure Chicago estiver destinado a um utilizador de Singapura, então o tráfego sai da rede da Microsoft em Chicago e viaja através da internet pública para o utilizador em Singapura.

## <a name="supported-services"></a>Serviços suportados

O IP público com a escolha de preferência de Encaminhamento "Microsoft Global Network" pode estar associado a quaisquer serviços Azure. No entanto, o IP público com a escolha de preferência de encaminhamento **A Internet** pode ser associada aos seguintes recursos Azure:

* Máquina virtual
* Conjuntos de dimensionamento de máquinas virtuais
* Azure Kubernetes Service (AKS)
* Equilibrador de carga virado para a Internet
* Gateway de Aplicação
* Azure Firewall

Para armazenamento, os pontos finais primários usam sempre a rede global da **Microsoft.** Pode ativar pontos finais secundários **com** internet como a sua escolha para o encaminhamento de tráfego. Os serviços de armazenamento suportados são:

* Blobs
* Ficheiros
* Web
* Azure DataLake

## <a name="pricing"></a>Preços
A diferença de preços entre ambas as opções reflete-se nos preços de transferência de dados da Internet. O encaminhamento através do preço global de transferência de dados da **Rede Microsoft** é o mesmo que o preço atual da internet. Visite a página de preços da [largura de banda azure](https://azure.microsoft.com/pricing/details/bandwidth/) para obter as últimas informações sobre preços. O encaminhamento via **Internet pública** tem um preço mais baixo, como mostra a tabela abaixo:

| Região de origem de Egress | 0-5 GB/Mês | 5GB-10 TB/Mês | 10-50 TB/Mês | 50-150 TB/Mês | 150-500 TB/Mês |
| --- | --- | --- | --- | --- | --- |
| Zona 1 | $0/GB | $0.085/GB | $0.065/GB | $0.06/GB | $0.04/GB |
| Zona 2 | $0/GB | $0.11/GB | $0.075/GB | $0.07/GB | $0.06/GB  |

[Contacte-nos](https://azure.microsoft.com/overview/sales-number/) para obter um volume mensal superior a 500 TB.
* Zona 1 — Austrália Central, Austrália Central 2, Canadá Central, Canadá Leste, Norte da Europa, Europa Ocidental, França Central, França Sul, Alemanha Norte (Público), Alemanha West Central (Público), Noruega Leste, Noruega Oeste, Suíça Norte, Suíça Oeste, Reino Unido, Oeste, EUA Central, Leste dos EUA, Leste dos EUA 2, Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA, West US 2 e West Central.

* Zona 2 - Leste asiático, Sudeste Asiático, Austrália Leste, Austrália Sudeste, Índia Central, Índia Do Sul, Índia Ocidental, Japão Leste, Japão Oeste, Coreia Central e Coreia do Sul.

* Zona 3 — Brasil Sul, África do Sul Norte, África do Sul Oeste, Emirados Emirados Unidos e Emirados Norte.

## <a name="availability"></a>Disponibilidade

O apoio à preferência de encaminhamento está disponível nas seguintes regiões para serviços como máquina virtual e equilibrador de carga virado para a Internet que usam um IP público para a via internet - Norte da Europa, Europa Ocidental, França Sul, Reino Unido Sul, Leste dos EUA, Norte Central dos EUA, Centro-Sul dos EUA, Oeste dos EUA, Sudeste Asiático, Alemanha West Central, Suíça Ocidental, Japão Leste e Japão Ocidental.

O apoio à preferência de encaminhamento para a conta de armazenamento está disponível nas seguintes regiões de Azure - França Sul, Centro-Norte dos EUA e Centro-Oeste dos EUA.
## <a name="limitations"></a>Limitações

* A preferência por encaminhamento só é compatível com sKU padrão de endereço IP público. Não é suportado o SKU básico do endereço IP público.
* A preferência por encaminhamento suporta atualmente apenas endereços IP públicos IPv4. Os endereços IP públicos do IPv6 não são suportados.
* As máquinas virtuais com vários NICs podem ter apenas um tipo de preferência de encaminhamento.


## <a name="next-steps"></a>Passos seguintes

* [Configure a preferência de encaminhamento para um VM utilizando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configure a preferência de encaminhamento para um VM utilizando o ClI Azure](configure-routing-preference-virtual-machine-cli.md)
