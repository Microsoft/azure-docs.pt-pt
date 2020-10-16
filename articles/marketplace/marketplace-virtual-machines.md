---
title: Guia de publicação de ofertas de máquinas virtuais no Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de uma máquina virtual e um teste gratuito de software a ser implantado a partir do Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 09/04/2020
ms.openlocfilehash: cc6b040731cbeb7271d7a7c0de1c32fa2d007013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484193"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Guia de publicação para ofertas de máquinas virtuais

Publicar imagens de máquinas virtuais (VM) é uma das principais formas de publicar uma solução para o Azure Marketplace. Utilize este guia para compreender os requisitos para este tipo de oferta. 

Ofertas de máquinas virtuais são ofertas de transações que são implementadas e faturadas através do Azure Marketplace. A opção de listagem que um utilizador vê é *Get It Now*.

## <a name="free-trial"></a>Avaliação gratuita 

Para que os utilizadores testem a sua oferta, aceda a licenças de software de prazo limitado quando utilizar o modelo de faturação de trazer a sua própria licença (BYOL). 

## <a name="test-drive"></a>Unidade de teste

Pode implementar uma ou mais máquinas virtuais através de infraestruturas como um serviço (IaaS) ou software como aplicações de serviço (SaaS). Um benefício da opção de publicação de *test drive* é a configuração automatizada de uma máquina virtual ou solução inteira liderada por uma visita guiada organizada por parceiros. Um test drive permite que os seus clientes avaliem VMs sem custos adicionais para eles. Um cliente não precisa de ser um cliente Azure existente para se envolver com a experiência do teste. 

Para obter mais informações sobre test drives, veja [o que é um test drive?](what-is-test-drive.md)

|Requisitos  |Detalhes |
|---------|---------|
| Você tem uma aplicação Azure Marketplace   |  Uma ou mais máquinas virtuais através do IaaS ou saaS.      |

## <a name="interactive-demo"></a>Demonstração interativa

Com esta oferta, você dá aos seus clientes uma experiência guiada da sua solução usando uma demonstração interativa. O benefício de uma opção de publicação de demonstração interativa é que você pode oferecer uma experiência experimental sem ter que fornecer uma configuração complicada da sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Utilize o tipo de oferta *de máquina virtual* quando colocar um aparelho virtual na subscrição que está associada ao seu cliente. Os VMs são totalmente habilitados para o comércio, utilizando modelos de licenciamento pay-as-you-go ou bring-your-own-license (BYOL). A Microsoft acolhe a transação de comércio e fatura o seu cliente em seu nome. Obtém o benefício de utilizar a relação de pagamento preferida entre o seu cliente e a Microsoft, incluindo quaisquer Acordos empresariais.

> [!NOTE]
> Neste momento, os compromissos monetários associados a um Acordo de Empresa podem ser usados contra o uso do Azure do seu VM, mas não contra as suas taxas de licenciamento de software.  
> 
> [!NOTE]
> Pode restringir a descoberta e implantação do seu VM a um conjunto específico de clientes, publicando a imagem e o preço como Oferta Privada. As Ofertas Privadas desbloqueiam a capacidade de criar ofertas exclusivas para os seus clientes mais próximos e oferecer software e termos personalizados. Os termos personalizados permitem-lhe destacar uma variedade de cenários, incluindo acordos liderados por campo com preços e termos especializados, bem como acesso precoce a software de lançamento limitado. As Ofertas Privadas permitem-lhe dar preços ou produtos específicos a um conjunto limitado de clientes, criando um novo plano com esses detalhes.  
>
> Para mais informações, consulte [Ofertas Privadas no Azure Marketplace.](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | O seu VM deve apoiar a faturação mensal byol ou pay-as-you-go. |  
| Disco rígido virtual compatível com Azure (VHD) | Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações sobre a criação de um VHD, consulte: <ul> <li>[Distribuição linux endossada em Azure](../virtual-machines/linux/endorsed-distros.md) (para Linux VHDs).</li> <li>[Crie um VHD compatível com Azure](./partner-center-portal/azure-vm-create-offer.md) (para VHDs windows).</li> </ul> |  

>[!Note]
>O opt-in do canal parceiro Cloud Solution Provider (CSP) já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros da Microsoft CSP, consulte [os Fornecedores de Soluções Cloud.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a [fazer crescer o seu negócio na nuvem com o Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [Criar uma oferta de máquina virtual](./partner-center-portal/azure-vm-create-offer.md) para obter mais informações.
