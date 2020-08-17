---
title: Bastião Azure Microsoft Docs
description: Saiba mais sobre o Azure Bastion, que fornece conectividade RDP/SSH segura e sem costura às suas máquinas virtuais sem expor as portas RDP/SSH externamente.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 8b27a7c2fd03ed0a80f1775465f1f1bbb44f0202
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270347"
---
# <a name="what-is-azure-bastion"></a>O que é o Azure Bastion?

O serviço Azure Bastion é um novo serviço PaaS totalmente gerido pela plataforma que fornece dentro da sua rede virtual. Fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre TLS. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público.

A Bastion proporciona uma conectividade segura de PDR e SSH a todos os VMs da rede virtual em que é alovisionado. A utilização do Azure Bastion protege as suas máquinas virtuais de expor portas RDP/SSH ao mundo exterior, ao mesmo tempo que fornece acesso seguro utilizando RDP/SSH. Com o Azure Bastion, liga-se à máquina virtual diretamente a partir do portal Azure. Não precisa de um cliente adicional, agente ou software.

## <a name="architecture"></a>Arquitetura

A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual. Uma vez que fornece um serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH está disponível para todos os seus VMs na mesma rede virtual.

RDP e SSH são alguns dos meios fundamentais através dos quais pode ligar-se às suas cargas de trabalho em execução em Azure. Expor portas RDP/SSH através da Internet não é desejado e é visto como uma superfície de ameaça significativa. Isto deve-se frequentemente a vulnerabilidades de protocolo. Para conter esta superfície de ameaça, pode implantar anfitriões de bastião (também conhecidos como servidores de salto) no lado público da sua rede de perímetro. Os servidores hospedeiros de bastião são projetados e configurados para resistir a ataques. Os servidores de bastião também fornecem conectividade RDP e SSH às cargas de trabalho que estão atrás do bastião, bem como mais dentro da rede.

![arquitetura](./media/bastion-overview/architecture.png)

Esta figura mostra a arquitetura de um destacamento de Azure Bastion. Neste diagrama:

* O hospedeiro Bastion está implantado na rede virtual.
* O utilizador liga-se ao portal Azure utilizando qualquer navegador HTML5.
* O utilizador seleciona a máquina virtual para se ligar.
* Com um único clique, a sessão RDP/SSH abre no navegador.
* Não é necessário um IP público no Azure VM.

## <a name="key-features"></a>Principais funcionalidades

As seguintes funcionalidades estão disponíveis:

* **RDP e SSH diretamente no portal Azure:** Você pode diretamente chegar à sessão RDP e SSH diretamente no portal Azure usando uma experiência sem emenda de clique único.
* **Sessão remota sobre TLS e firewall traversal para RDP/SSH:** O Azure Bastion utiliza um cliente web baseado em HTML5 que é automaticamente transmitido para o seu dispositivo local, de modo a obter a sua sessão RDP/SSH sobre tLS na porta 443, permitindo-lhe atravessar firewalls corporativos de forma segura.
* **Não é necessário um IP público no Azure VM:** O Azure Bastion abre a ligação RDP/SSH à sua máquina virtual Azure utilizando IP privado no seu VM. Não precisa de um IP público na sua máquina virtual.
* **Sem ações de gestão dos NSGs:** Azure Bastion é um serviço PaaS de plataforma totalmente gerido a partir de Azure que é endurecido internamente para fornecer-lhe conectividade RDP/SSH segura. Não precisa aplicar NSGs na sub-rede Azure Bastion. Como o Azure Bastion se conecta às suas máquinas virtuais em IP privado, pode configurar os seus NSGs para permitir apenas RDP/SSH a partir de Azure Bastion. Isto remove o incómodo de gerir os NSGs cada vez que precisa de se ligar de forma segura às suas máquinas virtuais.
* **Proteção contra a varredura da porta:** Como não precisa de expor as suas máquinas virtuais à Internet pública, os seus VMs estão protegidos contra a verificação por portas por utilizadores fraudulentos e maliciosos localizados fora da sua rede virtual.
* **Proteja-se contra explorações de dia zero. Endurecimento em apenas um lugar:** Azure Bastion é um serviço PaaS totalmente gerido pela plataforma. Como está no perímetro da sua rede virtual, não precisa de se preocupar em endurecer cada uma das máquinas virtuais da sua rede virtual. A plataforma Azure protege contra explorações de zero dias mantendo o Bastião Azure endurecido e sempre atualizado para si.

## <a name="faq"></a>FAQ

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar um recurso de anfitrião Azure Bastion](bastion-create-host-portal.md).
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
