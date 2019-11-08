---
title: Pré-requisitos de máquina virtual para Microsoft Azure | Azure Marketplace
description: Lista de pré-requisitos necessários para publicar uma oferta de VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824425"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual

Este artigo lista os requisitos técnicos e de negócios que você deve atender antes de publicar uma oferta de VM no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se você ainda não tiver feito isso, examine o [Guia de publicação da oferta da máquina virtual](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

Os pré-requisitos técnicos para publicar uma solução de máquina virtual (VM) são simples:

- Você deve ter uma conta ativa do Azure. Se você não tiver um, poderá se inscrever no site do [Microsoft Azure](https://azure.microsoft.com).  
- Você deve ter um ambiente configurado para oferecer suporte ao desenvolvimento de VM Windows ou Linux.  Para obter mais informações, consulte o site de documentação da VM associada:
    - [Documentação de VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação de VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos comerciais

Os requisitos de negócios incluem as obrigações legais, contratuais e de procedimento: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Você deve ser um Publicador do Cloud Marketplace registrado.  Se você ainda não estiver registrado, siga as etapas no artigo [tornar-se um editor de Marketplace de nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Você deve usar a mesma conta de registro do Microsoft Developer Center para se inscrever no [portal do Cloud Partner](https://cloudpartner.azure.com).
    > Você deve ter apenas um conta Microsoft para suas ofertas do Azure Marketplace. Ele não deve ser específico para serviços individuais ou ofertas.
    
- Sua empresa (ou sua subsidiária) deve estar localizada em uma região de venda/país com suporte do Azure Marketplace.  Para obter uma lista atual desses países/regiões, consulte [Microsoft Azure Marketplace políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Seu produto deve ser licenciado de forma que seja compatível com modelos de cobrança com suporte no Azure Marketplace.  Para obter mais informações, consulte [Opções de cobrança no Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Você é responsável por disponibilizar o suporte técnico aos clientes de maneira comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens da Comunidade.
- Você é responsável por licenciar seu software e quaisquer dependências de software de terceiros.
- Você deve fornecer conteúdo que atenda aos critérios de sua oferta a ser listado no Azure Marketplace e no portal do Azure. <!-- TD: Meaning/links? -->
- Você deve concordar com os termos das [políticas de participação Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e o contrato do editor.
- Você deve obedecer à [Microsoft Azure termos de uso do site](https://azure.microsoft.com/support/legal/website-terms-of-use/), [política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)e [contrato de programa certificado Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Passos seguintes

Depois de atender a esses pré-requisitos, você poderá [criar sua oferta de VM](./cpp-create-offer.md).
