---
title: Bastion do Azure | Documentos da Microsoft
description: Saiba mais sobre Bastion do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 06/17/2019
ms.author: cherylmc
ms.openlocfilehash: d153c876366c8c747e06bf50a0ea26bcdcea10eb
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303584"
---
# <a name="what-is-azure-bastion-preview"></a>O que é o Azure Bastion? (Pré-visualização)

O serviço de Azure Bastion é um nova plataforma serviço totalmente gerido PaaS que for aprovisionado no interior da rede virtual. Ele fornece a conectividade RDP/SSH de segura e totalmente integrada às suas máquinas virtuais diretamente no portal do Azure através de SSL. Ao ligar através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público.

 Bastion fornece conectividade segura de RDP e SSH a todas as VMs na rede virtual em que está aprovisionado. Usar Bastion do Azure protege as máquinas virtuais de expor portas RDP/SSH para o mundo exterior, continuando a oferecer acesso seguro através de RDP/SSH. Com o Azure Bastion, vai ligar à máquina virtual diretamente a partir do portal do Azure. Não precisa de um cliente adicionais, o agente ou a parte do software.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="architecture"></a>Arquitetura

Bastion do Azure é implementado na sua rede virtual e, depois de implantados, ele fornece a experiência RDP/SSH segura para todas as máquinas virtuais na sua rede virtual. Depois de aprovisionar um serviço de Bastion do Azure na sua rede virtual, a experiência RDP/SSH está disponível para todas as suas VMs na mesma rede virtual. A implementação é por rede virtual, não por/conta de subscrição ou numa máquina virtual.

RDP e SSH são alguns dos meios fundamentais por meio do qual pode ligar às suas cargas de trabalho em execução no Azure. Expor portas RDP/SSH através da Internet não é desejado e é visto como uma superfície de ameaça significativa. Isto é, muitas vezes, devido a vulnerabilidades de protocolo. Para conter essa superfície de ameaças, pode implementar anfitriões de bastion (também conhecido como atalhos-servidores) no lado público da sua rede de perímetro. Servidores de anfitrião de bastião são criados e configurados para resistirem a ataques. Servidores de bastion também fornecem conectividade RDP e SSH para as cargas de trabalho protegidos pelo bastion, bem como outras dentro da rede.

![arquitetura](./media/bastion-overview/architecture.png)

Esta figura mostra a arquitetura de uma implementação de Bastion do Azure. Neste diagrama:

* O anfitrião de bastião é implementado na rede virtual.
* O utilizador liga-se ao portal do Azure com qualquer navegador do HTML5.
* O utilizador seleciona a máquina virtual para ligar a.
* Com um único clique, a sessão do RDP/SSH é aberto no browser.
* Não existe nenhum IP público é necessária na VM do Azure.

## <a name="key-features"></a>Principais funcionalidades

As seguintes funcionalidades estão disponíveis para experimentar durante a pré-visualização pública:

* **RDP e SSH diretamente no portal do Azure:** Pode obter diretamente para a sessão RDP e SSH diretamente no portal do Azure com uma experiência totalmente integrada de único clique.
* **Sessão remota através de SSL e firewall traversal para RDP/SSH:** Bastion do Azure utiliza um cliente de web de HTML5 com base em que é transmitido automaticamente para o seu dispositivo local, para que obtém a sessão RDP/SSH através de SSL na porta 443, permitindo-lhe atravessar firewalls empresariais em segurança.
* **Não existe nenhum IP público necessário na VM do Azure:** Azure Bastion abre a ligação de RDP/SSH à máquina virtual do Azure com o IP privado na sua VM. Não é necessário um IP público na sua máquina virtual.
* **Sem complicação de gerir NSGs:** Bastion do Azure é uma plataforma totalmente gerida do serviço de PaaS do Azure que está protegido, internamente, para fornecer a que proteger a conectividade RDP/SSH. Não precisa de aplicar qualquer NSGs na sub-rede de Bastion do Azure. Como o Azure Bastion liga-se às suas máquinas virtuais através de IP privado, pode configurar os seus NSGs para permitir apenas o RDP/SSH a partir de Bastion do Azure. Esta ação remove a complicação de gerir NSGs sempre que precisar para ligar em segurança às suas máquinas virtuais.
* **Proteção contra a verificação de porta:** Uma vez que não é necessário para expor as suas máquinas virtuais para a Internet pública, as VMs estão protegidas contra por não autorizados e utilizadores mal intencionados localizados fora da rede virtual de verificação de porta.
* **Proteção contra explorações de dia zero. O sistema de proteção num único local apenas:** Bastion do Azure é um serviço de PaaS totalmente gerido de plataforma. Uma vez que ele reside no perímetro da sua rede virtual, não precisa se preocupar sobre o sistema de proteção de cada uma das máquinas virtuais na sua rede virtual. A plataforma do Azure protege contra explorações de dia zero, mantendo o Bastion Azure protegida e sempre atualizadas para.

## <a name="faq"></a>FAQ

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Criar um recurso de anfitrião de bastião de Azure](bastion-create-host-portal.md).
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
