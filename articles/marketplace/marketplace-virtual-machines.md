---
title: Guia de publicação de oferta de máquina virtual para o Mercado Azure
description: Este artigo descreve os requisitos para publicar uma máquina virtual e um teste gratuito de software a ser implementado a partir do Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687501"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guia de publicação de oferta de máquina virtual

As imagens Virtual Machine são uma das principais formas de publicar uma solução no Mercado Azure. Utilize este guia para compreender os requisitos desta oferta. 

Estas são ofertas de transações que são implementadas e faturadas através do Marketplace. A chamada à ação que um utilizador vê é "Get It Now".

## <a name="free-trial"></a>Avaliação Gratuita 

Pode providenciar para que os utilizadores testem a sua oferta acedendo a licenças de software de prazo limitado ao utilizar o modelo de faturação Bring Your Own License (BYOL). 

## <a name="test-drive"></a>Versão de Teste

Implementa uma ou mais máquinas virtuais através de aplicações de infraestruturas como serviço (IaaS) ou software-as-a-service (SaaS). Um benefício da opção de publicação de test drive é o fornecimento automatizado de uma máquina virtual ou de toda a solução liderada por uma visita guiada hospedada por parceiros. Um test drive fornece uma avaliação sem custos adicionais para o seu cliente. O seu cliente não precisa de ser um cliente Azure existente para se envolver com a experiência experimental. 

Contacte-nos no [amp-testdrive](mailto:amp-testdrive@microsoft.com) para começar. 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem uma aplicação Marketplace   |    Uma ou mais máquinas virtuais através do IaaS ou do SaaS.      |

## <a name="interactive-demo"></a>Demonstração Interativa

Fornece uma experiência guiada da sua solução aos seus clientes através de uma demonstração interativa. O benefício da opção de publicação de demonstração interativa é que você fornece uma experiência experimental sem um provisionamento complicado da sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de Máquina Virtual

Utilize o tipo de oferta de máquina virtual quando colocar um aparelho virtual na subscrição associada ao seu cliente. Os VMs estão totalmente habilitados a fazer comércio utilizando modelos de licenciamento pay-as-you-go ou bring-your-your-own-license (BYOL). A Microsoft acolhe a transação de comércio e fatura o seu cliente em seu nome. Obtém o benefício de utilizar a relação de pagamento preferida entre o seu cliente e a Microsoft, incluindo quaisquer Acordos empresariais.

> [!NOTE]
> Neste momento, os compromissos monetários associados a um Acordo empresarial podem ser usados contra o uso do Azure do seu VM, mas não contra as suas taxas de licenciamento de software.  
> 
> [!NOTE]
> Você é capaz de restringir a descoberta e implementação do seu VM a um conjunto específico de clientes, publicando a imagem e preços como uma oferta privada. O Private oferece desbloquear a capacidade de criar ofertas exclusivas para os seus clientes mais próximos e oferecer software e termos personalizados. Os termos personalizados permitem-lhe destacar uma variedade de cenários, incluindo ofertas orientadas por campo com preços e termos especializados, bem como acesso antecipado a software de lançamento limitado. As ofertas privadas permitem-lhe dar preços ou produtos específicos a um conjunto limitado de clientes, criando um novo SKU com esses detalhes.  
> *   Para mais informações sobre Ofertas Privadas, visite as Ofertas Privadas na página Azure Marketplace localizada em [azure.microsoft.com/blog/private-offers-on-azure-marketplace.](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)  

| Requisito | Detalhes |  
|:--- |:--- | 
| Faturação e medição | O seu VM deve apoiar a faturação mensal BYOL ou Pay-As-You-Go. |  
| Disco rígido virtual compatível com azure (VHD) | Os VMs devem ser construídos em Windows ou Linux. <ul> <li>Para obter mais informações sobre a criação de um VHD Linux, consulte [as distribuições linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD windows, consulte [Criar um VHD compatível com Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](./cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, 

- [Saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o mercado.

Se está registado e está a criar uma nova oferta ou a trabalhar numa já existente,

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [criar uma oferta de máquina virtual](./partner-center-portal/azure-vm-create-offer.md) para mais informações.
