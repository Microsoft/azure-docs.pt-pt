---
title: Guia de publicação de ofertas de máquinas virtuais no Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de uma máquina virtual e um teste gratuito de software a ser implementado a partir do Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: a01c8658ff4929d07cd185dcea45393cfe3b2a17
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858308"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Guia de publicação para ofertas de máquinas virtuais

Publicar imagens de máquinas virtuais (VM) é uma das principais formas de publicar uma solução para o Azure Marketplace. Utilize este guia para compreender os requisitos para este tipo de oferta. 

As ofertas de máquinas virtuais são ofertas de transações que são implementadas e faturadas através do Azure Marketplace. A chamada à ação que um utilizador vê é *Get It Now*.

## <a name="free-trial"></a>Avaliação gratuita 

Para organizar que os utilizadores testem a sua oferta, aceda a licenças de software de prazo limitado quando utilizar o modelo de faturação bring-your-your-own-license (BYOL). 

## <a name="test-drive"></a>Test drive

Pode implementar uma ou mais máquinas virtuais através de infraestruturas como um serviço (IaaS) ou software como aplicações de serviço (SaaS). Um benefício da opção de publicação de *test drive* é a configuração automatizada de uma máquina virtual ou solução inteira liderada por uma visita guiada hospedada por parceiros. Um test drive permite que os seus clientes avaliem VMs sem custos adicionais para eles. Um cliente não precisa de ser um cliente Azure existente para se envolver com a experiência experimental. 

Para começar, contacte-nos por e-mail no [amp-testdrive](mailto:amp-testdrive@microsoft.com). 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem uma app Azure Marketplace   |  Uma ou mais máquinas virtuais através do IaaS ou do SaaS.      |

## <a name="interactive-demo"></a>Demonstração interativa

Com esta oferta, você oferece aos seus clientes uma experiência guiada da sua solução usando uma demonstração interativa. O benefício de uma opção de publicação interativa de demonstração é que você pode oferecer uma experiência de teste sem ter que fornecer uma configuração complicada da sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Utilize o tipo de oferta *de máquina virtual* quando implementar um aparelho virtual para a subscrição que está associada ao seu cliente. Os VMs estão totalmente habilitados ao comércio, utilizando modelos de licenciamento pay-as-you-go ou bring-your-your-own-license (BYOL). A Microsoft acolhe a transação de comércio e fatura o seu cliente em seu nome. Obtém o benefício de utilizar a relação de pagamento preferida entre o seu cliente e a Microsoft, incluindo quaisquer Acordos empresariais.

> [!NOTE]
> Neste momento, os compromissos monetários associados a um Acordo empresarial podem ser usados contra o uso do Azure do seu VM, mas não contra as suas taxas de licenciamento de software.  
> 
> [!NOTE]
> Pode restringir a descoberta e implementação do seu VM a um conjunto específico de clientes, publicando a imagem e o preço como Oferta Privada. As Ofertas Privadas desbloqueiam a capacidade de criar ofertas exclusivas para os seus clientes mais próximos e oferecer software e termos personalizados. Os termos personalizados permitem-lhe destacar uma variedade de cenários, incluindo ofertas orientadas por campo com preços e termos especializados, bem como acesso antecipado a software de lançamento limitado. As Ofertas Privadas permitem-lhe dar preços ou produtos específicos a um conjunto limitado de clientes, criando um novo SKU com esses detalhes.  
>
> Para mais informações, consulte [Ofertas Privadas no Mercado Azure.](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | O seu VM deve apoiar a byol ou a faturação mensal. |  
| Disco rígido virtual compatível com azure (VHD) | Os VMs devem ser construídos em Windows ou Linux. Para mais informações sobre a criação de um VHD, consulte: <ul> <li>[Distribuições linux endossadas em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (para VHDs Linux).</li> <li>[Crie um VHD compatível com Azure](./partner-center-portal/azure-vm-create-offer.md) (para VHDs windows).</li> </ul> |  

>[!Note]
>O canal parceiro Cloud Solution Provider (CSP) já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP, consulte [cloud solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a cultivar o seu negócio de cloud com o [Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [Criar uma oferta de máquina virtual](./partner-center-portal/azure-vm-create-offer.md) para mais informações.
