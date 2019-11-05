---
title: Bastiões do Azure | Microsoft Docs
description: Saiba mais sobre a bastiões do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 4cee16a4b6d4f87c8f09ad742d9a504cfc5fc394
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498325"
---
# <a name="what-is-azure-bastion"></a>O que é o Azure Bastion?

O serviço de bastiões do Azure é um novo serviço de PaaS totalmente gerenciado por plataforma que você provisiona dentro de sua rede virtual. Ele fornece conectividade RDP/SSH segura e direta para suas máquinas virtuais diretamente no portal do Azure sobre SSL. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público.

 A bastiões fornece conectividade segura de RDP e SSH para todas as VMs na rede virtual em que ela é provisionada. Usar a bastiões do Azure protege suas máquinas virtuais contra a exposição de portas RDP/SSH para o mundo exterior enquanto ainda fornece acesso seguro usando RDP/SSH. Com a bastiões do Azure, você se conecta à máquina virtual diretamente do portal do Azure. Você não precisa de um cliente adicional, agente ou parte do software.

## <a name="architecture"></a>Arquitetura

A bastiões do Azure é implantada em sua rede virtual e, uma vez implantada, fornece a experiência RDP/SSH segura para todas as máquinas virtuais em sua rede virtual. Depois de provisionar um serviço de bastiões do Azure em sua rede virtual, a experiência de RDP/SSH estará disponível para todas as suas VMs na mesma rede virtual. A implantação é por rede virtual, não por assinatura/conta ou máquina virtual.

RDP e SSH são alguns dos meios fundamentais pelos quais você pode se conectar às suas cargas de trabalho em execução no Azure. Expor portas RDP/SSH pela Internet não é desejado e é visto como uma superfície de ameaça significativa. Isso geralmente ocorre devido a vulnerabilidades de protocolo. Para conter essa superfície de ameaça, você pode implantar hosts de bastiões (também conhecidos como servidores de salto) no lado público da rede de perímetro. Os servidores host bastião são projetados e configurados para resistir a ataques. Os servidores bastiões também fornecem conectividade RDP e SSH para as cargas de trabalho que estão atrás da bastiões, bem como dentro da rede.

![arquitetura](./media/bastion-overview/architecture.png)

Esta figura mostra a arquitetura de uma implantação de bastiões do Azure. Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário seleciona a máquina virtual à qual se conectar.
* Com um único clique, a sessão RDP/SSH é aberta no navegador.
* Nenhum IP público é necessário na VM do Azure.

## <a name="key-features"></a>Principais funcionalidades

Os seguintes recursos estão disponíveis:

* **RDP e SSH diretamente no portal do Azure:** Você pode acessar diretamente a sessão RDP e SSH diretamente no portal do Azure usando uma experiência simples de clique único.
* **Sessão remota sobre SSL e passagem de firewall para RDP/SSH:** A bastiões do Azure usa um cliente Web baseado em HTML5 que é transmitido automaticamente para seu dispositivo local, para que você obtenha sua sessão RDP/SSH sobre SSL na porta 443, permitindo que você percorra firewalls corporativos com segurança.
* **Nenhum IP público necessário na VM do Azure:** A bastiões do Azure abre a conexão RDP/SSH para sua máquina virtual do Azure usando o IP privado em sua VM. Você não precisa de um IP público em sua máquina virtual.
* **Sem complicações no gerenciamento de NSGs:** A bastiões do Azure é um serviço de PaaS de plataforma totalmente gerenciado do Azure que é protegido internamente para fornecer conectividade RDP/SSH segura. Você não precisa aplicar nenhum NSGs na sub-rede de bastiões do Azure. Como a bastiões do Azure se conecta às suas máquinas virtuais por meio de IP privado, você pode configurar seu NSGs para permitir RDP/SSH somente de bastiões do Azure. Isso elimina a complicação de gerenciar NSGs cada vez que você precisa se conectar com segurança às suas máquinas virtuais.
* **Proteção contra verificação de porta:** Como você não precisa expor suas máquinas virtuais para a Internet pública, suas VMs são protegidas contra a verificação de porta por usuários não autorizados e mal-intencionados localizados fora de sua rede virtual.
* **Proteja-se contra explorações de dia zero. Proteção em apenas um lugar:** a bastiões do Azure é um serviço de PaaS totalmente gerenciado por plataforma. Como ele reside no perímetro da sua rede virtual, você não precisa se preocupar em proteger cada uma das máquinas virtuais em sua rede virtual. A plataforma Azure protege contra explorações de dia zero mantendo a bastiões do Azure protegida e sempre atualizada para você.

## <a name="faq"></a>FAQ

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

* [Crie um recurso do host de bastiões do Azure](bastion-create-host-portal.md).
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
