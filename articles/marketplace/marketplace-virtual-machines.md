---
title: Guia para o Azure Marketplace de publicação de oferta de máquina virtual
description: Este artigo descreve os requisitos para publicar uma máquina virtual e uma versão de avaliação gratuita de software para ser implementadas a partir do Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: b9403855949e87a287f543c2ac41738e92238303
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217211"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guia de publicação de oferta de máquina virtual

Imagens de máquinas virtuais são uma das principais formas que publicar uma solução no Azure Marketplace. Utilize este guia para compreender os requisitos para esta oferta. 

Estes são ofertas de transação que são implementadas e faturadas através do Marketplace. A chamada a ação que um utilizador vê é a "Obter agora."

## <a name="free-trial"></a>Avaliação gratuita 

Pode fazer com que os utilizadores testar a sua oferta acedendo às licenças de software do período de validade limitado ao utilizar o modelo de faturação de traga a sua própria licença (BYOL). Seguem-se os requisitos para implementar esta oferta. 

|Requisitos  |Detalhes  |
|---------|---------|
|Período de avaliação gratuita e a experiência de avaliação     |   Os clientes podem tentar sua aplicação gratuitamente por um período limitado. Os clientes são nota de pagar quaisquer taxas de licenciamento ou de subscrição para a sua oferta. Os clientes não são necessários para pagar o produto de original Microsoft subjacente ou serviço. Todas as opções de avaliação são implementadas para a sua subscrição do Azure. Tem controle único da otimização de custos e a gestão. Pode optar por uma demonstração de avaliação ou interativa gratuita. Não importa o que escolha, a avaliação gratuita tem de fornecer aos clientes uma quantidade predefinida de tempo de testar a sua oferta sem custos adicionais.|
|Solução facilmente configurável e prontos a utilizar    |  A aplicação tem de ser fácil e rápida configurar e configurar.       |
|Disponibilidade / tempo de atividade    |    A aplicação de SaaS ou plataforma tem de ter um tempo de atividade de, pelo menos, 99,9%.     |
|Azure Active Directory     |    A oferta tem de permitir a que Azure Active Directory (Azure AD) federadas início de sessão único (SSO) (SSO federado do Azure AD) com o consentimento ativado.     |

## <a name="test-drive"></a>Versão de Teste

Implementar um ou mais máquinas virtuais por meio de infraestrutura-como-serviço (IaaS) ou aplicações de software-como-serviço (SaaS). Tour interativo de um benefício da unidade de teste que opção de publicação é o aprovisionamento automatizado de uma máquina virtual ou a solução inteira, liderado por um hospedada pelo parceiro. Uma versão de teste fornece uma edição de avaliação sem custos adicionais para o seu cliente. Seu cliente não precisa de ser um cliente existente do Azure a interagir com a experiência de avaliação. 

Contacte-nos [amp-Test-Drive do](mailto:amp-testdrive@microsoft.com) para começar a utilizar. 

|Requisitos  |Detalhes |
|---------|---------|
| Tem um aplicativo do Marketplace   |    Um ou mais máquinas de virtuais por meio de IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Demo Interativa

Fornecer uma experiência orientada da sua solução aos seus clientes através de uma demonstração interativa. O benefício de demonstração interativa, opção de publicação é que fornecer uma experiência de avaliação sem aprovisionar complicadas da sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Utilize o tipo de oferta de máquina virtual ao implementar uma aplicação virtual para a subscrição associada do seu cliente. As VMs são totalmente commerce ativada com modelos de licenciamento de pay as you go ou bring-your-own-license (BYOL). A Microsoft aloja a transação de comércio e cobra o seu cliente em seu nome. Obtém a vantagem de utilizar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo todos os contratos Enterprise.

>[!NOTE]
>Neste momento, compromissos monetários, associados com um contrato Enterprise podem ser utilizado contra a utilização do Azure da sua VM, mas não em relação a taxas de licenciamento de software.  

>[!NOTE]
>É possível restringir a deteção e a implementação da sua VM a um conjunto específico de clientes ao publicar a imagem e preços como uma oferta privada. Ofertas privadas desbloquear a capacidade de criar ofertas exclusivas para os seus clientes mais próximos e personalizada de software e os termos da oferta. Os termos personalizados permitem-lhe destacar uma variedade de cenários, incluindo o campo ministrado por negociações com especializadas de preços e termos, bem como acesso antecipado a software de versão limitada. Ofertas privadas ativar para lhe permitir disponibilizar preços específicos ou produtos para um conjunto limitado de clientes através da criação de um novo SKU com esses detalhes.  
*   Para obter mais informações sobre ofertas privadas, visite as ofertas de privado no Azure Marketplace, página localizada em [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | A VM tem de suportar uma faturação mensal BYOL ou pay as you go. |  
| Compatível com o Azure de disco rígido virtual (VHD) | As VMs devem ser criadas no Windows ou Linux. <ul> <li>Para obter mais informações sobre a criação de um VHD do Linux, consulte [distribuições do Linux apoiadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](./cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="next-steps"></a>Passos Seguintes

Se ainda não fez isso, 

- [Registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e estiver a criar uma nova oferta ou trabalhando num já existente

- [Inicie sessão no Portal de parceiro de Cloud](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Ver [oferta de Máquina Virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) para obter mais informações.
