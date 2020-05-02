---
title: Bastião Azure Microsoft Docs
description: Saiba mais sobre o Bastião Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411104"
---
# <a name="what-is-azure-bastion"></a>O que é o Azure Bastion?

O serviço Azure Bastion é um novo serviço PaaS totalmente gerido pela plataforma que você disponibiliza dentro da sua rede virtual. Fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre TLS. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público.

A Bastion fornece conectividade segura de RDP e SSH a todos os VMs na rede virtual em que é provisionado. A utilização do Azure Bastion protege as suas máquinas virtuais de expor portas RDP/SSH ao mundo exterior, ao mesmo tempo que proporciona acesso seguro utilizando RDP/SSH. Com o Azure Bastion, ligue-se à máquina virtual diretamente do portal Azure. Não precisa de um cliente, agente ou peça de software adicional.

## <a name="architecture"></a>Arquitetura

A implantação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual. Uma vez que você disponibilize um serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH está disponível para todos os seus VMs na mesma rede virtual.

RDP e SSH são alguns dos meios fundamentais através dos quais pode ligar-se às suas cargas de trabalho em funcionamento em Azure. Expor portas RDP/SSH através da Internet não é desejado e é visto como uma superfície de ameaça significativa. Isto deve-se muitas vezes a vulnerabilidades protocolares. Para conter esta superfície de ameaça, pode implantar anfitriões de bastião (também conhecidos como servidores de salto) no lado público da sua rede de perímetro. Os servidores de anfitriões do Bastião são projetados e configurados para resistir a ataques. Os servidores bastion também fornecem conectividade RDP e SSH às cargas de trabalho que estão atrás do bastião, bem como mais dentro da rede.

![arquitetura](./media/bastion-overview/architecture.png)

Esta figura mostra a arquitetura de uma implantação do Azure Bastion. Neste diagrama:

* O anfitrião bastião está implantado na rede virtual.
* O utilizador liga-se ao portal Azure utilizando qualquer navegador HTML5.
* O utilizador seleciona a máquina virtual para se ligar.
* Com um único clique, a sessão RDP/SSH abre no navegador.
* Não é necessário ip público no Azure VM.

## <a name="key-features"></a>Principais funcionalidades

Estão disponíveis as seguintes funcionalidades:

* **RDP e SSH diretamente no portal Azure:** Pode chegar diretamente à sessão rdp e SSH diretamente no portal Azure usando uma experiência perfeita de clique.
* **Sessão remota sobre TLS e firewall transversal para RDP/SSH:** O Azure Bastion utiliza um cliente web baseado em HTML5 que é automaticamente transmitido para o seu dispositivo local, para que obtenha a sua sessão RDP/SSH sobre tLS na porta 443, permitindo-lhe atravessar firewalls corporativos de forma segura.
* **Não é necessário ip público no Azure VM:** A Azure Bastion abre a ligação RDP/SSH à sua máquina virtual Azure utilizando IP privado no seu VM. Não precisa de um IP público na sua máquina virtual.
* **Sem aborrecimentos na gestão de NSGs:** Azure Bastion é um serviço paaS de plataforma totalmente gerido do Azure que é endurecido internamente para fornecer-lhe conectividade RDP/SSH segura. Não precisas de aplicar nsgs na subnet azure bastion. Como a Azure Bastion se conecta às suas máquinas virtuais em IP privado, pode configurar os seus NSGs para permitir apenas RDP/SSH a partir do Azure Bastion. Isto remove o incómodo de gerir nsgs cada vez que precisa de se ligar de forma segura às suas máquinas virtuais.
* **Proteção contra** a varredura portuária: Uma vez que não precisa de expor as suas máquinas virtuais à Internet pública, os seus VMs estão protegidos contra a verificação de portas por utilizadores fraudulentos e maliciosos localizados fora da sua rede virtual.
* **Proteja-se contra façanhas de zero dias. Endurecimento num só local:** O Azure Bastion é um serviço PaaS totalmente gerido pela plataforma. Como está no perímetro da sua rede virtual, não precisa de se preocupar em endurecer cada uma das máquinas virtuais da sua rede virtual. A plataforma Azure protege contra explorações de dia zero mantendo o Bastião Azure endurecido e sempre atualizado para si.

## <a name="faq"></a>FAQ

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

* [Crie um recurso de acolhimento do Azure Bastion.](bastion-create-host-portal.md)
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
